# Convex Codex Plugin

A Codex plugin for building on Convex: scaffold a running app from one sentence and build it live, add capabilities from the Convex component ecosystem, get a `convex-expert` subagent and a `convex-reviewer`, and watch for runtime errors as you go.

Use this when an app needs a backend: database schema, reactive queries, mutations, server functions, auth-aware data access, real-time features, file storage, scheduled jobs, mobile/web app backends, or production scaling guidance.

## Install (Codex CLI) — use this, it's the current build

```bash
codex plugin marketplace add get-convex/convex-codex-plugin
codex plugin add convex@convex-codex-plugin
```

This installs the full, current plugin (all skills + both MCP servers). Confirm with `codex plugin list` — you want `convex@convex-codex-plugin` at the version in [`plugins/convex/.codex-plugin/plugin.json`](plugins/convex/.codex-plugin/plugin.json).

> **Heads up on the OpenAI-curated listing.** If you install `convex` from OpenAI's curated/ChatGPT-app source instead, you may get an older published snapshot (the curated registry serves the last _submitted_ build, not this repo's HEAD — see [OPENAI-SUBMISSION.md](OPENAI-SUBMISSION.md)). The GitHub-marketplace command above always gets the current build. If a curated install ever seems to have no skills or MCP servers, remove it (`codex plugin remove convex@openai-curated`) and use the command above.

Then invoke the skills (`quickstart`, `add`, `convex-expert`, `convex-reviewer`, `check-updates`, `improve-convex-plugin`). The plugin also registers two MCP servers: the official Convex MCP (live-deployment introspection) and an error-watcher.

To update later: `codex plugin marketplace upgrade` then re-run `codex plugin add convex@convex-codex-plugin`.

## ChatGPT app

This plugin points Codex at the reviewed Convex app snapshot:

```text
asdk_app_6a0faef988b48191b843bac5cd170a9e
```

App URL: https://chatgpt.com/apps/convex/asdk_app_6a0faef988b48191b843bac5cd170a9e

The app exposes tools for starting Convex apps, adding Convex to existing JavaScript and TypeScript projects, and getting Convex scaling guidance.

## Example asks

```text
I want to make an app where my friends can vote on movie nights.
Build a real-time chat backend with rooms and message history.
Add sign-in and user-owned tasks to my Next.js app.
Design a multi-tenant schema for a SaaS with workspaces and roles.
What is the simplest way to add real-time updates to my app?
Review my app architecture before launch.
```

## Repo contents

- `.agents/plugins/marketplace.json` - marketplace manifest (makes the plugin installable)
- `plugins/convex/` - the Codex CLI plugin (skills + MCP servers)
- `.codex-plugin/plugin.json` + `.app.json` - the reviewed Convex ChatGPT app (a lighter, coexisting target)
- `assets/` - Convex brand assets

## Privacy & data

This plugin connects to Convex services and collects usage data. See the [Convex privacy policy](https://convex.dev/legal/privacy) for full details and your rights. Three kinds of data can leave your machine, each governed by a rule that holds no matter which command triggers it:

### 1. Usage telemetry (on by default, opt-out)

A session-start hook sends telemetry to Convex's PostHog project: a random device id, the plugin version, your OS and Node.js version, which agent harness emitted the event (always `codex` for this plugin), whether the working directory looks like a Convex project (a yes/no flag — the directory path itself is never sent), and how the session began. If the working directory has a `.env.local` or `.env` file with the `CONVEX_DEPLOYMENT` line the Convex CLI writes, the event also carries the Convex deployment name and team name from that line. Convex can use them to link plugin usage to your Convex team and account. Nothing else is read from those files. Never your code, file paths, prompts, or other values from your env files. Codex asks you to review the hook before it runs for the first time, and nothing is sent unless you trust it. Opt out with `CONVEX_PLUGIN_TELEMETRY=0` or `DO_NOT_TRACK=1`.

### 2. Building your app (only when you invoke a scaffolding flow)

Flows that scaffold or extend an app (such as `quickstart` and `/add`) send the inputs you give them to the Convex scaffolding service so it can build for you — for example, the one-sentence idea you type is sent to the scaffolding endpoint and logged as a run start. These flows also download and run setup scripts from that service. This happens only when you invoke such a flow. The labs quickstart builds locally with legacy gateway publishing disabled. If you request frontend hosting, the chosen hosting workflow uploads the build to that hosting service.

### 3. Sharing a session to improve the tools (one-time, explicit opt-in)

If you run `$improve-convex-plugin`, the plugin sends a **redacted** copy of your current session to the Convex team. The agent does not offer this on its own. Sharing is opt-in. The first time a session would be sent, you are asked to choose **Always**, **Just this once**, or **Never**.

- **Always** and **Never** are remembered per user in `~/.convex/improve-consent`, so you are not asked again.
- **Just this once** shares only that session and is not stored, so a later session asks again.

Nothing is sent until a choice is recorded. The helper stops and tells the agent to ask you. The send itself runs as a normal agent action under your agent's usual tool approval.

For a hard guarantee that nothing is ever sent, set `CONVEX_IMPROVE_CONSENT=never`. It overrides any stored choice. Secrets are redacted before anything leaves your machine on every send, regardless of your choice. Change your mind anytime by deleting `~/.convex/improve-consent`.

If you don't invoke these flows, nothing beyond the telemetry above leaves your machine.
