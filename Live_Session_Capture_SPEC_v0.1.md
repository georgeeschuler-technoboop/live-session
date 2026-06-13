# Beyond the Map: Live Session — Architecture Spec

**Name.** Resolved: **Beyond the Map: Live Session**, a member of the Beyond the Map family, sibling to the existing Workbook. Earlier drafts called this "the tool" or "Live Session Capture."

**Status:** v0.1 design spec. No code yet. This document exists so the core constraint is on the page before either of us writes a line.

---

### Changelog
- **v0.5 — 2026-06-12.** Locked the product name: Beyond the Map: Live Session, a member of the Beyond the Map family. Updated the title, the name note, and §12 decision 5.
- **v0.4 — 2026-06-11.** Locked the Electric indigo gradient as the signature token (§14) and recorded why it lives in a brighter band than the flat brand indigo. Added the run-of-show and step-advance model and the Wall-preview presenter-view pairing to the Console (§4.3). Added the one-response-per-prompt confirmation/waiting rule to the participant view (§4.1).
- **v0.3 — 2026-06-11.** Rewrote §14 against the actual Beyond the Map source. BtM is a warm-paper base with navy hero/canvas panels and a `--grad-cover` gradient signature, not a dark app. This tool inherits that whole system and swaps only the cover gradient to indigo, with amber stepping out of the signature.
- **v0.2 — 2026-06-11.** Resolved all five open decisions (live-prompt pacing, Wall attribution hidden / Console visible, download handback, new repo, working title). Added §14 visual identity.
- **v0.1 — 2026-06-11.** Initial spec. Establishes the synthesis-provenance principle as Principle 1, the three-view structure (Participant / Wall / Console), the data model that makes provenance structural, the deterministic-first / LLM-later sequencing on shared rails, and the per-participant continuity artifact. Maps the data model to the exact Rogue Water Lab "From Plans to Progress" Part 1 to 3 prompts.

---

## 1. What this is

One tool, one job: **structured plenary capture with facilitator-controlled reveal.**

A live session (Zoom plenary, no breakouts) runs entirely inside the tool. Participants enter their thinking on their own devices. The facilitator drives a screen that is shared into Zoom, and decides what the room sees and when. Zoom carries faces, voices, and logistics. The tool owns the structured conversation and its reveal.

This is the scalpel boundary. The tool rides on top of Zoom. It does not try to be Zoom. As long as it never absorbs video, audio, scheduling, or presence, it stays one tool doing one thing.

It reuses the bones of the existing TNN Pre-Work Tool (three views, Supabase real-time, no-login join, CSV/JSON export). What is new is structured multi-part input instead of a single sticky, polls folded in, the reveal controls on the shared screen, sourced tagging in the backstage view, and a per-participant artifact.

---

## 2. Non-negotiable principles

**Principle 1 — Synthesis is always a labeled set of sourced records.**
The tool never displays a sentence the tool wrote about the room. Everything it shows is one of three things: a count of records, a grouping of records, or a record quoted as-is. A cluster is a set of response IDs. A pattern is a label attached to a set of response IDs. A verbatim line is a participant's own record, shown directly. Because of this, every element of any synthesis traces back to a named participant by construction, not by adding a citation afterward. This holds in the deterministic v1 and it holds when the LLM arrives. The only thing that ever changes is who proposes a grouping.

**Principle 2 — Deterministic first, LLM on the same rails later.**
v1 does no machine pattern-reading. Its intelligence is counts, poll math, search, human tagging, and rule-based flags. The LLM enters later as a proposer of groupings over existing records, never as a writer of prose. See §7.

**Principle 3 — Concepts land in the debrief.**
The tool gives the facilitator a reveal control that Zoom chat structurally cannot. Inputs can be collected silently while people are still typing, then revealed. The tool never pushes a pattern or a framework to the shared screen on its own. The facilitator decides when the room sees structure.

**Principle 4 — Design for boredom.**
The tool is now the room. Whatever the old Chat Curator was as the single highest-failure-risk element, the tool inherits that role. So: no login, join by code, optimistic UI, a refresh that always works, and Zoom chat kept explicitly as the declared fallback for technical and logistic issues only.

**Principle 5 — No login, low friction.**
Fifty-five people get in within the first sixty seconds, by code or link, with a display name and nothing else.

---

## 3. The session is already a form

The Rogue Water Lab session is a structured intake form run across three surfaces (Zoom polls, Zoom chat, a fillable handout) and reassembled by hand. The tool unifies those surfaces. The input schema below is the actual session.

| Part | Poll (categorical) | Free-text capture |
|---|---|---|
| **1 · See Your System** | Where do your projects most often get stuck? (Internal alignment / Community trust / Decision-making / Cross-agency coordination / Funding & resources / Other) | (a) Your project in one sentence. (b) One person whose role would surprise the people writing the formal plan. |
| **2 · Find the Leverage** | Which flow is weakest right now? (Information / Trust / Decision authority) | One concrete example of what that weak flow looks like in your project right now. |
| **3 · Make a Move** | (none) | Four fields: the person or org you will engage; the specific move; what success looks like in two weeks; what could block it. |

Each Part follows the same rhythm the facilitator guide already uses: quiet think, then capture, then full-group reveal, then debrief. The tool is paced to that rhythm by the facilitator.

---

## 4. The three views

Reusing the TNN tool's structure, renamed for this purpose.

### 4.1 Participant view (their device)
- Join with display name and session code. No account.
- Shows only the prompt that is currently live, paced by the facilitator, so the participant's screen matches the shared screen.
- Input-only. The participant sees their own submissions, not other people's. This is deliberate: it keeps the room looking at the shared Wall rather than at fifty-five private feeds.
- One response per prompt. After submitting, the participant sees a confirmation and a quiet waiting state, not a fresh empty field, and can revise until the facilitator advances. An empty field that re-presents after each submit is wrong here: it reads as an endless loop and invites duplicate entries.
- Mobile-first. Assume phones.

### 4.2 Wall view (screen-shared into Zoom)
- Large, legible, projection-friendly. This is the old "present" view.
- Entirely facilitator-controlled. It renders whatever the facilitator selects: live drops landing during a chat-storm, poll results, a spotlight set of chosen verbatim lines, or a confirmed clustered view.
- **Collecting / Revealing** toggle. In Collecting, drops are gathering but the Wall does not show them, or shows only a live count. In Revealing, the facilitator shows what they choose.
- **Attribution** toggle. Default is names hidden on the Wall (see §12). The Console always knows who said what.
- LLM proposals never appear here. Only confirmed, facilitator-pushed content reaches the Wall.
- The live-drops mode is the replacement for "watching the chat fill." Drops animate in as they arrive.

### 4.3 Console view (facilitator and Curator, backstage, own screens)
- **Run of show and step advance.** The ordered session sequence (framing, then each Part's quiet-think, poll, chat-storm, reveal, debrief) lives here as a rail. The facilitator advances by moving the live pointer (`active_prompt_id`), and every participant phone and the Wall follow it through the real-time connection. Advancement is always a deliberate human action, never a timer. This is the control that actually drives the room, and it is the spine of the Console.
- **The firehose:** every response to the live step, attributed (the Console always sees who said what), newest first, with full-text search and filter.
- **Tagging:** create clusters, assign responses to clusters by hand (the sticky-drag move). Live counts per cluster.
- **Polls:** results and breakdown.
- **Quiet-contributor flags:** a list of participants who submitted a substantive response but have not yet been marked as having spoken. Pure rule, no model.
- **Spotlight to Wall:** select responses, push them to the Wall as verbatim. Set the Wall view, toggle Collecting/Revealing, toggle attribution.
- **Exports:** full-session CSV and JSON; per-participant artifact (§8).
- **Wall preview (presenter view).** The Console embeds a live, scaled preview of exactly what the room sees, so a solo facilitator can screen-share the Wall tab and drive from the Console tab beside it, the way presenter view pairs with slides.
- The Curator role does not disappear. It moves here. Two facilitator seats into the same live data, which Supabase real-time already supports. One person drives the Wall, one works the Console.

---

## 5. Data model

Content originates in exactly one place: the `response` table. Everything else points at responses. There is no table where the tool stores generated prose. That absence is the structural guarantee behind Principle 1.

**session**
`id, join_code, title, status, active_part, active_prompt_id, wall_state (collecting | revealing), wall_view (drops | poll | spotlight | clusters), attribution_visible (bool), spotlight_response_ids (array), created_at`
The facilitator-controlled state the Wall reads.

**participant**
`id, session_id, display_name, has_spoken (bool), joined_at, last_seen`

**prompt**
`id, session_id, part, kind (poll | text), label, options (json, for polls), order`
Prompts are data, not hardcoded, so the same tool serves other sessions by loading a different prompt set. Seeded for v1 with the Part 1 to 3 prompts in §3.

**response** — the atomic sourced record
`id, session_id, participant_id, prompt_id, part, value (text or selected option), is_substantive (bool), created_at`
The only place content lives. Every response is bound to one participant and one prompt.

**cluster**
`id, session_id, part, label, color, proposed_by (human | llm), confirmed (bool), created_at`
A named bucket. Carries no content of its own. Its meaning is its membership.

**response_cluster** (join) — this is what a cluster *is*
`response_id, cluster_id, source (human | llm_proposed | llm_confirmed), assigned_at`
A cluster's membership is a set of `response_id`s, full stop. To group is to write rows here.

Computed, no table needed:
- **Quiet contributor** = participant with at least one `is_substantive` response and `has_spoken = false`.
- **Poll result** = count of responses to a poll prompt, grouped by value.

`is_substantive` is set deterministically (a length threshold, or a manual Console toggle), never by a model.

---

## 6. Why this model makes provenance free

A cluster shown on the Wall is rendered by reading its `response_cluster` rows and pulling the referenced `response` records. A "pattern" is a cluster with a label. A "representative line" is a single `response`. At no point does the tool hold a paragraph about the conversation that is not itself a set of records. Ask "where did that come from" about anything the tool ever shows, and the answer is a list of `response` rows, each tied to a `participant`. The citation is the data structure.

---

## 7. Deterministic v1, LLM v2, one architecture

### v1 — fully deterministic
Intelligence is: poll math, response counts, full-text search and filter, human tagging (assign responses to clusters by hand), and the quiet-contributor rule. Zero black box. Everything sourced.

**The trap to avoid.** Do not build lexical clustering (TF-IDF, shared-keyword grouping) as a stand-in for the model. It is more work than calling an LLM, gives worse results, and gets thrown away in v2. v1 does not try to read meaning. It makes sure no drop is missed and everything is sourced, and it leaves the reading of meaning to the Curator, exactly where the facilitator guide already puts it.

**The session structure does much of the work.** The polls pre-bucket people before any clustering. By the Part 2 chat-storm, everyone is already sorted into Information, Trust, or Decision authority by the poll, so the Console is sub-grouping inside a known bucket, not clustering from scratch. Human tagging handles that at fifty-five people.

### v2 — LLM as proposer, on the same rails
The model reads the `response` records for a given session and part and writes `response_cluster` rows with `source = llm_proposed` into clusters it creates with `proposed_by = llm, confirmed = false`. It can also nominate which responses are representative or surprising. It never writes to `response.value`. It never creates anything that is not a set of response IDs. The Curator confirms or reassigns, which flips `confirmed` and updates `source`. Proposed-but-unconfirmed content is visibly marked and never auto-reaches the Wall.

Because v1 and v2 write to the same join table, adding the model changes who proposes a grouping and nothing else. Provenance holds either way.

---

## 8. The continuity artifact

At session end, each participant can receive their own thinking back, assembled from their responses. This is the part almost no one offers, and it is the bridge into the Beyond the Map homework. It is their own work, not a canned profile.

The in-session captures map onto Beyond the Map's seven steps:

| Session capture | Beyond the Map step |
|---|---|
| Part 1 — project in one sentence | Aim |
| Part 1 — surprising person | Candidate broker |
| Part 2 — weakest-flow poll + concrete example | Roles & flows |
| Part 3 — the four move fields | Moves |
| (not captured in class) | Context |
| (not captured in class) | Metrics |

So a participant walks into Beyond the Map with four of its seven steps already drafted in their own words. Context and metrics are what the homework adds on top. The closing slide already promises this in prose; the artifact makes it literal.

**Output forms:**
- A readable handback (their drafted aim, broker, flow, and move) they can keep.
- A JSON export shaped for Beyond the Map import. BtM is localStorage today, so the import path is a future BtM enhancement. Until then the JSON is portable and the readable handback carries the value.

---

## 9. Reliability (design for boredom)

- No login. Join by code or link, display name only.
- Optimistic UI; a manual refresh that always reloads clean state.
- Zoom chat declared as the fallback channel for technical and logistic issues only.
- The Wall degrades gracefully: if real-time stalls, a refresh restores it; a stale Wall is never worse than a frozen Zoom chat.
- Join friction budget: under sixty seconds for the room, link posted once in Zoom chat or on the opening slide.

---

## 10. Roadmap

**v1 (build first, all deterministic).** Schema with the provenance invariant. Three views. Participant capture for the exact Part 1 to 3 prompts. Polls. Manual tagging. Spotlight-to-Wall. Collecting/Revealing and attribution toggles. Quiet-contributor flag. CSV/JSON export. Per-participant readable handback.

**v2.** LLM proposal pass on the same rails. Verbatim and surprise nomination. No change to the provenance model.

**Later.** Beyond the Map JSON import integration. Reuse for other session types via different prompt sets. Optional delivery of the handback (email tier from the BtM access ladder), and cloud save.

---

## 11. Scope boundaries (what it is not)

- Not a video or audio tool. It rides on Zoom.
- Not a generic survey platform. It is paced to a live facilitated session.
- Not an auto-summarizer. It never writes prose about the room.
- Not a course LMS.

Holding these lines is what keeps it a scalpel.

---

## 12. Decisions (resolved 2026-06-11)

1. **Participant pacing.** Resolved: show only the live prompt, facilitator-paced, so participant screens stay in sync with the Wall.
2. **Wall attribution default.** Resolved: names hidden on the Wall, visible in the Console. The Part 1 "surprising person" prompt asks people to name a real colleague or official, so the shared screen stays anonymous and the Console keeps full attribution.
3. **Handback delivery.** Resolved: download for v1. Email delivery via the opt-in tier is a later add.
4. **Home for the code.** Resolved: a new dedicated repo, reusing the TNN schema patterns.
5. **Naming.** Resolved: **Beyond the Map: Live Session**, a member of the Beyond the Map family, sibling to the existing Workbook (the 7-step homework tool). The existing tool already uses this colon-descriptor pattern ("Beyond the Map: Network Visibility Lab"), so this joins a convention rather than inventing one. The broader scalpel family sits under C4C and Relational Intelligence, since "Beyond the Map" names the mapping-and-moves lineage specifically, not the whole house. Still open: whether to rename the Workbook's "Network Visibility Lab" descriptor to pair more cleanly (for example, "Workbook").

---

## 13. Tech and brand constants

- **Stack:** single-page app, Supabase REST plus WebSocket real-time, Netlify plus GitHub, no login. Same posture as the TNN tool.
- **Provenance invariant lives in the schema**, so it cannot be designed around later.
- **Palette:** Amber `#EB9001`, Indigo `#2825BE`, Teal `#0C7A7A`, Terra Cotta `#CF4C38`.
- **Type:** Barlow Condensed (display), IBM Plex Sans (body), IBM Plex Mono (labels and eyebrows).
- **Convention:** version history and changelog in every code file.

## 14. Visual identity — the indigo cover gradient

This tool is the live, collective sibling of Beyond the Map, so it inherits BtM's actual visual system and changes one thing: the signature gradient.

**What BtM actually is.** A warm paper base (`#f4efe6`) with navy (`#0d0f1a`) panels for the hero and the map canvas. The "dynamic" accent is a cover gradient, `--grad-cover: linear-gradient(135deg, #ff5a3c, #ff7a1a, #eb9001)`, a diagonal run from hot vermilion through orange to the brand amber. It appears as clip-text on key words, as gradient buttons, and as the hero's gradient border, with a soft amber radial behind the hero. On the paper surfaces, flat indigo `#2825BE` is already the secondary in-content accent. Type is Barlow Condensed (800, uppercase) for display, IBM Plex Sans for body, IBM Plex Mono for eyebrows. Radii are crisp, 2 to 3px.

**What this tool changes.** One line. The cover gradient becomes indigo: `linear-gradient(135deg, #4f3fe6 0%, #6d4ff0 45%, #a07bff 100%)` (locked, v0.4), an electric indigo rising to a luminous violet. Note the asymmetry between the two brand colors. BtM's amber is naturally bright, so its gradient anchors right on the flat brand amber. The brand indigo `#2825BE` is dark, so to keep the same electric quality the gradient lives in a brighter band, and `#2825BE` stays the solid accent (the Part 1 swatch, the step marker, solid buttons) rather than a gradient stop. Amber steps out of the signature so indigo owns it. Everything else, the warm paper, the navy panels, the type, the crisp radii, the mono eyebrows, the box vocabulary, stays identical, which is what makes it read as a sibling rather than a cousin.

**Surfaces.** Participant and Console take BtM's warm-paper step register. The Wall takes BtM's navy hero-and-canvas register, where the indigo gradient does its clip-text and the drops bloom in. Same family, two registers, one signature.

**The one rule.** The identity gradient is the glow. The functional part colors (Indigo, Teal, Terra Cotta) stay solid swatches. Same indigo, different treatment, so the Part 1 swatch never blurs into the tool's own gradient.
