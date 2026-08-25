# Thumbnail Designer Workflow Built with n8n Assistant

📺 **[Watch the video](https://youtu.be/grWtJae41Vs)**

This one builds a thumbnail designer in n8n using nothing but plain words in the AI Assistant. The
whole build is a single spec prompt, and that prompt is in [`prompts.md`](prompts.md). It's the
thing worth copying from this folder.

There's no `workflow.json` here. The prompt is what produces the workflow, so paste it into the n8n
Assistant and let it build your copy.

## What it does

You fill in a form with a request in plain English, something like
`Thumbnail for Leo, use reference 1. Text: "Leave your like"`. An agent finds that person's photo
and the reference thumbnail in Google Drive, generates two 1280x720 variations with `gpt-image-2`
through OpenRouter, and uploads them to a `generated` folder.

The reason to build it: you can point it at a thumbnail that already did well on the channel and
repurpose it with a different person and different text.

## What you need first

**A Google Drive** with three folders. One holds a subfolder of photos per person, one holds
reference thumbnails identified by name or number, and one collects the output. Pick whatever names
you like, since the workflow lists folders as it goes rather than using hardcoded IDs.

**Google Cloud OAuth credentials** for Drive. The video walks through all of it:

1. Go to [console.cloud.google.com](https://console.cloud.google.com) and create a project.
2. Under **APIs & Services → Enable APIs and services**, enable the **Google Drive API**.
3. Go to **Credentials → Configure consent screen** and fill in the app name and support email. If
   you're inside an organization and don't need outside users, pick internal. Otherwise external is
   fine.
4. **Create client → Web application.** You need the redirect URI from n8n before this will save, so
   open the Google Drive credential in n8n, copy the OAuth Redirect URL, paste it in, then create.
5. Copy the client ID and client secret back into n8n and hit **Sign in with Google**.
6. Go to **Data access → Add scopes** and add the three Drive scopes the credential asks for: see
   and edit files, create and delete files, and view photos. One of the scopes in that list is for
   Docs and reads almost identically to the Drive one, so check the column before you tick it. Save,
   then authorize.

**An OpenRouter credential** with access to `gpt-image-2`. One credential covers both OpenRouter
nodes, and the Assistant spots that and offers to reuse it.

## Notes from the build

The AI Assistant is still in preview. It stopped partway through the build once. Sending `Continue`
picked it up from where it left off.

The model you run it on matters more than you'd expect. This was built self-hosted on a local
Kimi K2, which kept erroring until it got swapped for GPT-5.6. If you're on the self-hosted
Assistant and hitting errors, try a different model before you assume the prompt is wrong.

Run the live test from inside the Assistant chat rather than executing the workflow yourself. It
sees its own errors and still has the original prompt in context, so it fixes them without being
told what went wrong. This run took about seven minutes and applied a few fixes along the way.

After a long run the canvas can look like it's still going when it isn't. Refresh the page and
reopen the workflow to see the real state.

## How it behaves

The text you ask for is rendered exactly as written, casing and punctuation included. Files land in
the generated folder as `text_person_date_version`, so `this-is-a-test_leo_2026-08-23_v1`. If the
agent can't work out the person, the reference or the text, it stops and tells you what's missing
rather than guessing.
