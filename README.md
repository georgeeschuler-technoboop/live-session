# Beyond the Map: Live Session

A live, collective sibling to **Beyond the Map: Workbook**. Where the Workbook is the solo influence-mapping you do in the two weeks after a session, **Live Session** is the in-room tool: the plenary thinks out loud, and the facilitator captures and shapes it in real time.

This repo currently holds the **front-end prototype** (`index.html`). It is a self-contained static page with **fake data and no backend**, built to feel out the three views and the visual identity before wiring the real Supabase layer described in the spec.

## The three views

Switch between them with the **Participant / Wall / Console** control in the top demo bar.

- **Participant** — what a person sees on their phone. One live prompt at a time, paced by the facilitator. Answer once, then a confirmation and a wait state. (The Prev/Next stepper here only *simulates* the facilitator advancing; on a real phone there is no Next button.)
- **Wall** — the room-facing screen the facilitator shares over Zoom. Anonymous by default. Drops bloom in as they land; the facilitator controls Collecting vs Revealing and the view.
- **Console** — the backstage cockpit. Run-of-show with step advance, an attributed firehose, deterministic tagging, quiet-contributor flags, spotlight selection, the Wall controls, a live Wall preview (presenter view), and CSV export.

## The one rule it is built around

**Synthesis is always a labeled set of sourced records.** A cluster is nothing but a set of responses; a verbatim line is a participant's own record. The tool never shows prose it wrote about the room. Tagging in the Console literally builds those sets. This is what lets an LLM assist later without becoming a black box. See the spec for the full data model.

## Visual identity

A sibling of Beyond the Map. Same warm-paper base, navy hero/canvas panels, Barlow Condensed / IBM Plex Sans / IBM Plex Mono, and crisp edges. The only change is the signature gradient, swapped from BtM's amber to an electric indigo: `linear-gradient(135deg, #4f3fe6 0%, #6d4ff0 45%, #a07bff 100%)`.

## Run it locally

It's a single static file. Open `index.html` in a browser, or serve the folder:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

No build step, no dependencies beyond the Google Fonts link.

## Deploy to Netlify

Because it is static with no build, either path works:

**Fastest (no repo):** drag this folder onto <https://app.netlify.com/drop>. Live in seconds.

**Repo-backed:** push this repo to GitHub, then in Netlify choose *Add new site → Import an existing project*, pick the repo, and leave the build command empty with the publish directory set to the repo root. It redeploys on every push.

## Status and what's next

This is a front-end feel-test. The next build is the real backend: port the three views to the Supabase schema in the spec so they share live state instead of the in-memory state they share now. That's where the provenance invariant becomes structural and where the Console actually drives real phones.

See `Live_Session_Capture_SPEC_v0.1.md` for the full architecture.
