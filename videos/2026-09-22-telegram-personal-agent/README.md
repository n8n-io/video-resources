# Build an always-on Telegram AI agent with n8n Agents

📺 **[Watch the video](https://youtu.be/AbqPOLfLsm8)**

⬇️ **[The agent from the video: agent.json](agent.json)**. Open it, hit Download, and import it
into n8n. Or paste it into the n8n Assistant with [`setup-prompt.md`](setup-prompt.md) and let it
fill in the blanks for you.

This is the companion page for the video on building a personal AI assistant in Telegram with the
n8n Agents feature, the one n8n shipped in September 2026. There's no code and no workflow canvas.
The agent in the video reads Gmail, drafts replies, logs receipts to a Google Sheet, manages a
Todoist to-do list through its MCP server, and runs a scheduled task every morning that turns
yesterday's emails into tasks. It runs on Claude Sonnet 5 through OpenRouter.

## What's in this folder

- [`agent.json`](agent.json): the agent from the video with everything personal swapped for
  placeholders. Import it into the Agents tab of any project, fill in the blanks, publish, and
  you're most of the way there.
- [`setup-prompt.md`](setup-prompt.md): a prompt that gets the n8n Assistant or a coding agent to
  interview you and fill in the JSON, if you'd rather not edit it by hand.

Agents are still in preview, so the UI will move around. The building blocks stay the same. If
something isn't where I say it is, scroll a bit.

## What the video covers

This is the whole video in order, so you can find the part you came back for.

### The Agents tab and what an agent is made of

Every project in n8n now has an Agents tab next to workflows. An agent is a separate entity from a
workflow, and it comes with a chat that can build the agent for you from a prompt. The video builds
one by hand instead, so you see the pieces: channels (Telegram, Slack, Linear), tools, skills,
subagents, the instructions field, the model, a Sessions tab for past chats and debugging, and a
Settings tab. Settings is where you turn on web search and reasoning, and where the default of ten
parallel subagents lives. The only two things an agent needs to answer a message are a model and
some instructions, and there's a preview chat for testing before anything is connected.

### Picking a model: n8n gateway credits or OpenRouter

By default an agent runs on n8n gateway credits, which come with two dollars free. On self-hosted
n8n you may find no model selected at all. The video connects OpenRouter instead, which lets you pick nearly any model and
pay per token. When you create the OpenRouter API key, put a monthly spend limit on it. I set mine
to five dollars for a personal assistant.

I started on Muse Spark 1.3, a cheap Meta model, and switched to Claude Sonnet 5 partway through
when the agent couldn't see images. More on that below.

### Connecting Telegram with BotFather

Telegram is a native channel, so there's no trigger node and no typing-indicator workaround. You
make a bot by messaging BotFather in Telegram, sending `/newbot`, and giving it a display name and
a username that ends in `bot`. BotFather replies with an access token, and that token goes into a
Telegram credential in n8n. The credential is the normal n8n credential, so if you already have a
Telegram bot connected you can reuse it.

Set the access mode to private and add your own Telegram user ID, which you get by messaging
`@userinfobot`. Public access is for a business bot that the public is meant to message. A private
bot with your email attached shouldn't be reachable by anyone who guesses its username.

### Draft versus published

Changes to an agent autosave as a draft. The preview chat runs the draft, but Telegram only sees
the version you've published, so publish after every change you want live. It works this way on
purpose: if someone is mid-conversation with the bot, a half-finished edit shouldn't break it.

### Reading errors in Sessions

The first Telegram message in the video failed. The bot replied with an error instead of failing
silently, and the Sessions tab showed the full message from OpenRouter. In that case it was
OpenRouter refusing a model endpoint that didn't match the account's data policy, and switching
to a different endpoint of the same model fixed it. Whatever the cause, Sessions is where you read
the actual error.

### Adding tools: workflows, nodes, and MCP servers

An agent can call four kinds of tool. Existing n8n workflows, for deterministic logic the agent
triggers. Nodes from the n8n library, like Gmail, Google Sheets, data tables or Wikipedia. The MCP
catalog, which is a list of ready-made MCP connections. And the MCP client, for any MCP server by
URL.

The video connects Todoist through its MCP server, found by searching "Todoist MCP", with MCP
OAuth as the authentication. Most servers use an endpoint ending in `/mcp`, but check the server's
own docs for the URL and auth type. Once it's connected you can see every tool the server exposes and untick
the ones you don't want the agent to have, like delete or edit. MCP is the fast option: the agent
works out how to use the server on its own.

The Gmail tools are the other end of the trade-off. Each one is a node set up to do one specific
thing (search messages, get a message by ID, create a reply draft in a thread), so you control
exactly what the agent can do and which parameters it fills in. That's the right choice when a
mistake would be costly.

### Instructions, tool descriptions, and skills

The instructions field is for what the agent needs on every message: who it works for, how it
should talk, formatting rules for the channel, and rules that always apply. Keep it short. Every
word in there is billed on every turn, so add lines as you find behaviour to fix rather than
front-loading a long prompt.

Tool descriptions are for when to call a tool and how. If a tool isn't being called when it should
be, this is the first thing to change. You can edit the descriptions on node tools but not on MCP
tools. The Log Expense tool in the video gets a description that says when to call it and which
expense categories are valid: eating out, consumer spending, housing, vacation, and groceries.

Skills are prompt files the agent loads only when it decides they're relevant, so they're the
place for per-task instructions: how you like emails written, a research process, house rules for
a type of message. The agent picks a skill by its description, the same way it picks a tool. You
can write them, ask the n8n Assistant to write them, or upload a folder someone else made. The
video uploads a deep research skill and tests it by asking for the best hot dog spots in
Philadelphia.

### Scheduled tasks

A schedule is a time plus a plain-language objective, and the run uses the same agent with the
same tools. The one in the video runs daily at 9:00 AM with this objective: "Go through the emails
from the past day and add any tasks to Todoist that need action items based on them. Once
finished, send Liam a message with all the tasks you created." Put your Telegram user ID next to
your name so it knows where to send the message. The agent can message you in Telegram from a
scheduled run without any extra setup. You can also run a schedule immediately to test it instead
of waiting for the time to come round.

### Sending photos: sticky notes and receipts

Telegram lets you send the agent a photo. In the video a photo of a sticky note becomes a Todoist
task, and a photo of a receipt becomes an expense row in Google Sheets. Two lessons from that. The
model has to be multimodal, and even then the first attempt on Muse Spark through OpenRouter came
back with "I can't see the picture" until I switched to Claude Sonnet 5. And models miss things in
photos: Sonnet logged the printed total and skipped the "+15" tip I'd written by hand. Check
anything the agent read off an image.

### Fixing behaviour with one line

When the agent listed tasks without links, one line in the instructions fixed it: "Whenever you
send info about a to-do list item, link to it." A second line, "If I send you a sticky note, just
add it to the to-do list," made the sticky note flow work without explaining it each time. Most
behaviour fixes are this size.

### Traces and what it cost

Every session shows a colour-coded trace: green is a tool call, blue is you, purple is the agent,
teal is a subagent. You can click through each step, see the attachment the agent received, and
see where it retried a tool after an error. The whole conversation in the video, with several
images and mostly on Sonnet 5, came to about 70 cents.

## Fill in before it works

Every placeholder in `agent.json` is in capitals and contains either `YOUR` or `ADD`, so a
case-sensitive search for those two words finds all of them, including the ones with spaces like
`YOUR AGENT NAME HERE`. The ones that matter:

- `name` at the top: what the agent is called.
- `instructions`: who it works for, how it should talk, formatting rules for the channel, and
  standing context like timezone and working hours.
- `credential` at the top and the `credentials` block on each tool: the IDs of your OpenRouter,
  Gmail and Google Sheets credentials.
- The spreadsheet ID, the spreadsheet's display name and the sheet name in the Log Expense tool,
  plus its `toolDescription`.
- `skills`, `tasks` and `mcpServers`: these point at IDs that don't exist in your instance. Delete
  the entries you don't need. You can add skills, schedules and MCP servers from the agent's own
  tabs after import.
- The Telegram integration: your bot token credential and your user ID.

## Setting up the Telegram bot, step by step

1. In Telegram, message BotFather and send `/newbot`. Give the bot a display name, then a username
   with no spaces that ends in `bot`. It replies with an access token.
2. In n8n, open the agent's Channels, pick Telegram, and create a Telegram credential with that
   token.
3. Set access mode to private. Public means anyone who guesses your bot's username can use its
   tools, and this agent has your email.
4. Message `@userinfobot` in Telegram to get your user ID, and add it to the allowed users.
5. Publish the agent. The preview chat runs the draft, but Telegram only sees what you've
   published.
6. Find the bot in Telegram by its exact username and say hello.
