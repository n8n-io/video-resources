---
name: maintain-video-resources
description: Maintain the n8n DevRel video-resources repo — the layout, naming, per-video folders, and the root README index. Use whenever adding resources for a new YouTube video (workflow JSON, prompts, notes), editing an existing video's folder, renaming/reorganizing anything under videos/, or updating the index. Triggers on "add a video", "new video resources", "add this workflow/prompt to the repo", "update the index/README".
---

# Maintaining video-resources

This repo is the public companion to the n8n DevRel team's YouTube videos. Instead of pasting
workflows and prompts into video descriptions, videos link here with short links — every click is
a trackable enablement signal. That purpose drives every rule below: the repo is read by viewers
who just arrived from a video and have ~5 seconds of patience.

## Required layout

```
video-resources/
├── README.md          ← the index, newest at top (edited on every PR that adds a video)
└── videos/
    ├── 2026-08-24-n8n-control-thesis/
    │   ├── README.md      ← content per video (if needed)
    │   ├── workflow.json
    │   └── prompts.md
    ├── 2026-08-10-agent-memory-demo/
    └── 2026-07-29-webhook-basics/
```

All per-video content lives under `videos/`. Nothing else goes at repo root — root stays
`README.md` + `videos/` (plus tooling dotfiles) so that GitHub renders the index immediately in
the viewport when someone lands on the repo. Never add a top-level folder for a single video's
assets, and never create category folders (`workflows/`, `prompts/`) that split one video's
resources across the tree.

## Folder naming

`videos/YYYY-MM-DD-short-slug/`

- Date is the video's **publish date** (or the intended one). Ask if it isn't known and no date is
  implied; if the user says to proceed without it, use today's date and note in the PR/summary
  that the folder should be renamed once the video is scheduled.
- Slug: lowercase, hyphenated, 2–5 words, recognizable from the video title. Not the full title.
- Never renumber or reorder folders — the date prefix already sorts them.

## Files inside a video folder

Include only what the video actually produced. An empty placeholder file is worse than a missing
one.

| File | When |
| --- | --- |
| `README.md` | Whenever the resources need any framing at all — what the video builds, what to set up before importing, gotchas. Skip only for a folder holding a single self-explanatory file. |
| `workflow.json` | The exported n8n workflow. Multiple workflows → descriptive names (`workflow-ingest.json`, `workflow-agent.json`). |
| `prompts.md` | Prompts used in the video, verbatim. |
| anything else | Sample data, `.env.example`, screenshots — only if the video used it. |

Keep prompts **verbatim**. They are the thing viewers came to copy; do not tidy wording, reformat
headings, or "improve" them. Reproduce the original text and structure exactly, and put any of your
own commentary in the video `README.md` instead.

## Credentials

Exported n8n workflows carry credential references. Before committing a `workflow.json`, check it
for anything sensitive — API keys, tokens, webhook URLs with secrets, real email addresses,
internal URLs, Drive/Sheet IDs pointing at private assets. Strip or placeholder them and say what
was stripped. Credential *names* (e.g. `"name": "Google Drive account"`) are fine; credential
*values* never are.

## The index (root `README.md`)

Every PR that adds a video folder also edits the root `README.md` in the same PR. The index is a
table, **newest at top**:

```markdown
| Date | Video | Resources |
| --- | --- | --- |
| 2026-08-24 | [n8n Control Thesis](https://youtu.be/…) | [workflow + prompts](videos/2026-08-24-n8n-control-thesis/) |
```

- Link the video to its YouTube URL. If the URL isn't known yet, add the row with the title in
  plain text and flag that the link needs filling in — do not invent or guess a URL, and do not
  fabricate a short link.
- Link resources to the folder path (relative link), not to individual files.
- Keep the intro above the table to a couple of lines: what this repo is and that it's linked from
  the videos.

## Working checklist

When adding a video, do all of these:

1. Create `videos/YYYY-MM-DD-slug/`.
2. Add the resource files, prompts verbatim, workflows scrubbed of secrets.
3. Write the folder `README.md` if the resources need framing.
4. Add a row at the **top** of the index table in root `README.md`.
5. Report anything left as a placeholder (missing date, missing YouTube URL, missing short link).

Missing information is never a reason to guess. Ship everything that's known, mark the gap
explicitly, and say what you need to fill it.
