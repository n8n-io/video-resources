# Thumbnail Designer Workflow Built with n8n Assistant

📺 **[Watch the video](https://youtu.be/grWtJae41Vs)**

Building a thumbnail designer workflow in n8n using nothing but plain words in the AI Assistant.
The whole build is one spec prompt — it's in [`prompts.md`](prompts.md) and is the main thing to
copy from this folder.

> **No `workflow.json` here.** The point is the prompt that produces the workflow, not an export to
> import. Paste the build prompt into the n8n Assistant and let it build your version.

## What it builds

A form takes a request in plain English — `Thumbnail for Leo, use reference 1. Text: "Leave your like"` —
and an AI agent resolves the person's photo and the reference thumbnail from Google Drive, generates
two 1280x720 variations with `gpt-image-2` via OpenRouter, and uploads them to a `generated` folder.
The idea is a system for iterating on thumbnails that already worked on the channel: take a previous
successful thumbnail as the reference and repurpose it with a different person and different text.

## Before you run it

**Google Drive**, with three folders — one holding a subfolder of photos per person, one of
reference thumbnails identified by name/number, and one for generated output. The names are yours
to pick; the workflow lists folders dynamically rather than using hardcoded IDs.

**Google Cloud OAuth credentials** for Drive. The video walks through this end to end:

1. [console.cloud.google.com](https://console.cloud.google.com) → new project.
2. **APIs & Services → Enable APIs and services** → enable the **Google Drive API**.
3. **Credentials → Configure consent screen** → fill in app name and support email. Internal if
   you're in an organization and don't need external users, otherwise external.
4. **Create client → Web application.** You need the redirect URI from the n8n credential modal
   first, so open n8n's Google Drive credential, copy the OAuth Redirect URL, paste it into the
   Google client, and hit create.
5. Copy the client ID and client secret back into n8n, then **Sign in with Google**.
6. **Data access → Add scopes.** Add the three Drive scopes the credential asks for (see, edit,
   create and delete files; and view photos). Watch out for the near-identical Docs scope — pick
   the Drive one. Save, then authorize.

**OpenRouter** credential with access to `gpt-image-2`. The same credential covers both OpenRouter
nodes — the Assistant detects it and offers to reuse it.

## Notes from the build

- The AI Assistant is still in preview. It stopped mid-build once; sending `Continue` picked it back
  up from where it left off.
- Model choice matters. This was built self-hosted on a local **Kimi K2**, which errored repeatedly
  until it was swapped for **GPT-5.6**. If you're on the self-hosted Assistant and hitting errors,
  try a different model before assuming the prompt is at fault.
- Run the **live test** from inside the Assistant chat rather than executing the workflow yourself.
  It sees its own errors with the original prompt as context and fixes them. The test took about
  seven minutes and applied several fixes along the way.
- After a long run the canvas can look stuck — refreshing the page and reopening the workflow shows
  the real state.

## Behavior details

- The requested text is rendered exactly as written; casing and punctuation are preserved.
- Output files land in the generated folder as `text_person_date_version`, e.g.
  `this-is-a-test_leo_2026-08-23_v1`.
- If the person, reference, or text can't be resolved, the agent stops and says what's missing
  instead of guessing.
