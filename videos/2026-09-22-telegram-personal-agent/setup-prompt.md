# Prompt to fill in the template

Paste this into the n8n Assistant or a coding agent along with the contents of
[`agent.json`](agent.json). It asks you what it needs, then hands back a finished agent.

---

I have a template for an n8n agent in JSON. Help me turn it into my own personal assistant and give
me back the finished JSON.

The template has placeholders written in capitals, like YOUR_GMAIL_CREDENTIAL_ID and ADD AGENT
PERSONALITY AND TONE HERE. Every one of them has to be filled in or removed before we're done.

Work like this:

1. Read the whole template first and list every placeholder you find.
2. Ask me questions to fill them in, a few at a time, in plain language. Start with who the agent
   works for, what I want it to do, and which chat channel it lives in. Then ask about tone,
   formatting rules for that channel, my timezone and working hours, and anything it should never do
   without checking with me.
3. For the Log Expense tool, ask what categories I use and write its tool description: when to call
   it, and the valid categories.
4. For anything that needs an ID from my n8n instance (credentials, skills, scheduled tasks, MCP
   servers, the spreadsheet, my Telegram user ID), ask me for the real value. Never make one up.
5. If I don't have or don't want a skill, task, MCP server or tool, delete that entry instead of
   leaving a placeholder in it.
6. When there's nothing left to ask, give me the full JSON. If you can create the agent in n8n
   directly, do that instead and show me what you set.

Rules for the result:

- Keep the instructions short. Only put in what the agent needs on every message: who it works for,
  how it should talk, formatting rules, standing context. Rules about when to use one specific tool
  go in that tool's description, not in the instructions.
- Leave every expression that starts with `={{` exactly as it is. That's how the agent fills in tool
  parameters at run time.
- Keep the Telegram channel on private access with my user ID in the allowed list unless I say
  otherwise.
- The output has to be valid JSON with no placeholders left in it. Check it before you give it to
  me.
