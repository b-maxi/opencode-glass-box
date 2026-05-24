# opencode-glass-box

<img width="323" height="221" alt="image" src="https://github.com/user-attachments/assets/990157a2-274e-4f73-b26d-01db8eba6258" />


Use this if you want to test AI agent workflows with full visibility and manual approval before moving to a more autonomous setup.

## When to use this

- ✅ You're new to AI agents and want to understand what they're actually doing
- ✅ You want to control every step and inspect every command before it runs
- ✅ You want to use raw API calls for now before building custom MCP servers
- ❌ Not for autonomous workflows and production deployments

## Setup

Use both templates for the full glass-box workflow:

1. Copy `templates/opencode.jsonc` to `./opencode.jsonc` in your project root.
2. Copy `templates/AGENTS.md` to `./AGENTS.md` in your project root.
3. Replace `envchain <your_namespace>` in `AGENTS.md` with the exact secret-injection tool and namespace your project actually uses.
4. Start OpenCode in that project and expect approval prompts for reads, edits, shell commands, and web access.

What each file does:

- `opencode.jsonc` keeps only the `build` agent type and puts most actions behind approval prompts.
- `AGENTS.md` keeps responses terse and tells the agent to use raw `curl` with your secret wrapper.

## Features

- **Single agent**

  Configured via `agent` section in `opencode.jsonc`. All types are disabled except `build`.

  This keeps the workflow linear and predictable. It also disables the agent types that would otherwise let you switch modes or delegate work to subagents.

  Start here if you want to inspect one visible execution path before you add more agent types or subagents.

- **Manual approvals**

  Configured via `permission` in `opencode.jsonc`.

  Most actions require approval, including reads, edits, shell commands, and web access. Only `question` is allowed by default.
  That means the agent pauses before it acts, and you can stop it if the run is heading in the wrong direction.

  The workflow is intentionally slow.
  If you want to loosen it later, `read` is the first permission to consider changing from `ask` to `allow`.

- **API calls**

  No MCPs configured in `opencode.jsonc`.
  `AGENTS.md` tells the agent to run raw `curl` with a credential wrapper.

  The agent writes raw `curl` directly, using `envchain` to inject secrets at runtime without exposing them.
  If your project uses a different secret-injection tool, replace the `envchain` command in `AGENTS.md` with that exact tool so the agent has one concrete pattern to follow.

  This way you get transparent HTTP payloads, but it requires verbose manual error handling and external vault setup.
  Add MCP servers later, once you have repeated the same API patterns enough times that you want a narrower interface, less request boilerplate, and credentials handled in one place.

- **Token efficiency**

  Three things keep the context window small:

  - **No output repetition:** agent never repeats or summarizes CLI/API output — you can read the terminal. Configured via **Response** rule in `AGENTS.md`.
  - **Manual approvals:** only explicitly approved actions run, cutting wasted token spend on wrong paths.
  - **No subagents:** no hidden parallel work inflating the context.

## Philosophy

> Build glass box before black box

Do not configure what you have not explicitly observed. Start with 100% manual validation, then graduate to automation as you understand the underlying execution patterns.

This template came out of learning those patterns the slow way first. I kept the authoring stance personal on purpose: inspect a few runs, build confidence, then automate only what you have already seen work.

## Documentation

Refer to official OpenCode documentation:

* [All properties in opencode.jsonc](https://opencode.ai/docs/config/)
* [How to write AGENTS.md and where to place it](https://opencode.ai/docs/rules/)
* [How to configure MCP servers](https://opencode.ai/docs/mcp-servers/)

## Acknowledgments
Header image: "Cat playing with a glass of milk" (1903) by Belle Johnson. Public Domain.
