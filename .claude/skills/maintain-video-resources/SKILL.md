---
name: maintain-video-resources
description: Maintain the n8n DevRel video-resources repo: the layout, naming, per-video folders, and the root README index. Use whenever adding resources for a new YouTube video (workflow JSON, prompts, notes), editing an existing video's folder, renaming or reorganizing anything under videos/, or updating the index. Triggers on "add a video", "new video resources", "add this workflow/prompt to the repo", "update the index/README".
---

# Maintaining video-resources

This repo is the public companion to the n8n DevRel team's YouTube videos. Rather than pasting
workflows and prompts into a video description, the videos link here with short links, so every
click is a trackable enablement signal.

That shapes the rules below. Someone reading this repo just clicked a link in a video and wants the
thing they saw on screen, so the index has to be the first thing they see and the resources have to
be one click from it.

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

All per-video content lives under `videos/`. Root stays as `README.md` plus `videos/` and any
tooling dotfiles, so GitHub renders the index in the viewport as soon as someone lands. Don't add a
top-level folder for one video's assets, and don't create category folders like `workflows/` or
`prompts/` that scatter a single video's resources across the tree.

## Folder naming

`videos/YYYY-MM-DD-short-slug/`

- The date is the video's publish date, or the intended one. Ask if you don't know it and nothing
  in the request implies it. If told to go ahead without it, use today's date and say in the summary
  that the folder needs renaming once the video is scheduled.
- The slug is lowercase and hyphenated, two to five words, recognizable from the video title. Not
  the full title.
- Never renumber or reorder folders. The date prefix sorts them.

## Files inside a video folder

Only include what the video actually produced. An empty placeholder file costs the reader more than
a missing one.

| File | When |
| --- | --- |
| `README.md` | Whenever the resources need framing: what the video builds, what to set up before importing, what to watch out for. Skip it only for a folder holding one self-explanatory file. |
| `workflow.json` | The exported n8n workflow. For several workflows, use descriptive names like `workflow-ingest.json` and `workflow-agent.json`. |
| `prompts.md` | The prompts used in the video, verbatim. |
| anything else | Sample data, `.env.example`, screenshots, but only if the video used it. |

Keep prompts verbatim. They're what people came to copy, so don't tidy the wording, reformat the
headings or improve the phrasing. Reproduce the original text and structure, and put any commentary
of your own in the video `README.md`.

## Writing the README prose

Follow the `human-writing` skill for every README in this repo. These pages are read by people who
just watched a video, and copy that reads as machine-written undercuts the channel.

## Credentials

An exported n8n workflow carries credential references. Before committing a `workflow.json`, read
it for anything sensitive: API keys, tokens, webhook URLs with secrets in them, real email
addresses, internal URLs, Drive or Sheet IDs pointing at private assets. Strip or placeholder them
and say what you stripped. Credential names like `"name": "Google Drive account"` are fine to keep.
Credential values never are.

## The index (root `README.md`)

Every PR that adds a video folder edits the root `README.md` in the same PR. The index is a table,
newest at the top:

```markdown
| Date | Video | Resources |
| --- | --- | --- |
| 2026-08-24 | [n8n Control Thesis](https://youtu.be/…) | [workflow + prompts](videos/2026-08-24-n8n-control-thesis/) |
```

- Link the video to its YouTube URL. If you don't have the URL yet, add the row with the title as
  plain text and flag that it needs filling in. Don't guess a URL and don't invent a short link.
- Link the resources to the folder, not to individual files inside it.
- Keep the intro above the table to a couple of lines: what the repo is and that the videos link to
  it.

## Checklist for adding a video

1. Create `videos/YYYY-MM-DD-slug/`.
2. Add the resource files. Prompts verbatim, workflows scrubbed.
3. Write the folder `README.md` if the resources need framing.
4. Add a row at the top of the index table in the root `README.md`.
5. Report anything left as a placeholder: a missing date, a missing YouTube URL, a missing short
   link.

If something's missing, say so and say what you need. Guessing a URL or a date puts a wrong link in
front of everyone who clicks through from the video.
