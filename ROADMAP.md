# Roadmap

This repo is the case-study layer: it documents the system's design with
synthetic examples. The roadmap below tracks what comes next.

## Phase 1 (done) — Case-study docs

The README, the conventions contract, and the synthetic examples. The goal of
this phase is to make the *design* legible to a reader: the decisions, the
constraints, and the structure, without exposing any real personal content.

## Phase 2 (future) — Sanitized dashboard demo

There is a private companion app: a small Next.js dashboard that renders the
system as a set of views (a Today view, Goals, Projects, Reviews, and a Skills
catalog). Right now it cannot be published, because it reads live personal data
straight out of the vault and local files. That live-data coupling is the
blocker.

The work to publish a public demo:

- **Swap the data sources for a mock-data layer.** The app reads through a small
  set of source modules (`lib/sources/*.ts`). Replace those with fixtures so the
  app renders entirely from invented data.
- **Scrub absolute paths.** Remove home-directory and cloud-storage paths baked
  into config; drive everything off env vars with safe demo defaults.
- **Add a "run with demo data" mode.** A single flag that loads fixtures instead
  of the live sources, so the demo is the default and the live wiring is opt-in.
- **Deploy a static preview.** Build and host a read-only preview that anyone can
  click through without any local setup.

The outcome would be a clickable, public version of the dashboard that shows the
same system this repo describes, running on fully synthetic data.

## Possible later

- A short walkthrough of the triage and daily-brief flows as recordings.
- A template starter pack: the `_Templates/` set and the hygiene scripts as a
  drop-in for someone wanting to run the same structure.
