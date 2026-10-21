# Thinkube Tandem Chat

Tandem chats in VS Code's chat panel, in Thinkube IDE: the coding agent Pi with Thinkube's models, MCP servers and operating rules. Start one with **New Tandem (powered by Pi) Session** in the chat panel's New menu (the arrow beside +). The agent answers with Thinkube's local models through the LLM Gateway and uses the Thinkube MCP servers, as configured for it in Thinkube IDE. No GitHub account is involved.

## What it does

- Pi is a chat session type (`@tandem-pi`): VS Code lists its chats in the Sessions list, and what you type in one of its chats goes to Pi, with no `@`.
- The chat panel's model picker lists the models Pi can call: the models of the LLM Gateway marked `agent` in the model catalog that are loaded, and the catalog's agent default even when it is not. A new chat starts with the default; the model picked reaches Pi with `set_model`, and Pi keeps it in the chat's session. The list is read from the gateway every 30 seconds.
- **Pi** runs `pi --mode rpc` per chat. Pi's text streams into the chat, its tool calls show as progress, and its questions (such as the confirmation before a tool that changes the platform) are asked in the chat.
- Pi's file edits are tracked as agent edits: they show as changes you can keep or undo. Pi waits before each edit until VS Code has recorded the file (`pi/before-edit.ts`, loaded only into the Pi processes of this extension).
- At startup the chat panel shows a new Tandem (powered by Pi) chat in place of a chat of VS Code's own agent, which has no model in Thinkube IDE. The replaced chat stays in the Sessions list.
- Each chat keeps its agent session: the next message continues it, also after a reload, and a reopened chat shows its earlier messages, read back from the agent.

Pi uses its own configuration (`~/.pi/agent/`), written by Thinkube's `roles/agent_clients`; its `thinkube-models` extension reads the same models from the gateway.

## Limits

- VS Code records a file before an edit from its open copy and sees the result through its file watcher, so Keep and Undo work for files in the workspace folders. An edit to an existing file outside them shows no change.

## Requirements

- `pi` on the PATH (the Thinkube IDE image carries it), and `LLM_GATEWAY_URL` and `THINKUBE_API_TOKEN` in the environment (Thinkube IDE sets both).
- Proposed APIs enabled for `thinkube.thinkube-tandem-chat`: code-server's `enable-proposed-api` option (Thinkube IDE sets it in code-server's `config.yaml`). The extension uses `chatSessionsProvider`, `chatProvider`, `chatParticipantAdditions` and `chatParticipantPrivate`.

## Deploy

```bash
scripts/deploy.sh
```

Builds the extension, installs it into this code-server and records the release (see the script). `scripts/deploy.sh` is the same file in every Thinkube extension.

## License

Apache License 2.0, see [LICENSE](LICENSE). The `src/vscode.proposed.*.d.ts` files are VS Code's API definitions, MIT, Copyright (c) Microsoft Corporation.
