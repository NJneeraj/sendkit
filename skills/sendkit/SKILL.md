---
name: sendkit
description: Use sendkit to send Telegram messages from agents through the sendkit MCP tool or CLI fallback. Use when a user asks to send a Telegram message, use sendkit, interact with the sendkit toolset, verify Sendkit manually, or choose between Sendkit MCP and CLI workflows.
---

# SendKit

A unified toolset to send Telegram messages from AI agents or command-line interfaces. SendKit offers two primary modes of operation:

1. **MCP (Model Context Protocol) Server**: Interactive tool-based execution directly within an agent's context.
2. **CLI (Command Line Interface)**: A standalone fallback executable.

---

## Workspace Architecture

Key packages within the SendKit repository:

- Core Logic: [packages/core](../../packages/core)
- Command Line Interface: [packages/cli](../../packages/cli)
- Local MCP Server: [packages/local-mcp](../../packages/local-mcp)
- Remote MCP Server: [apps/remote-mcp](../../apps/remote-mcp)

---

## 1. MCP Server Workflow (Default & Preferred)

When running inside an MCP-compatible environment (such as Gemini/Antigravity or Claude Code), use the native MCP tool.

### Configuration

The MCP server requires the `TELEGRAM_BOT_TOKEN` environment variable. In your client's MCP configuration, register the package `@neeraj_jangir/sendkit-mcp`:

```json
{
  "mcpServers": {
    "sendKit": {
      "command": "npx",
      "args": ["-y", "@neeraj_jangir/sendkit-mcp"],
      "env": {
        "TELEGRAM_BOT_TOKEN": "<your-telegram-bot-token>"
      }
    }
  }
}
```

### Using the Tool

Call the `telegram` tool registered on the `sendKit` MCP server:

- **Server Name**: `sendKit`
- **Tool Name**: `telegram`
- **Arguments**:
  - `chatId` (string): The target Telegram chat or channel ID.
  - `message` (string): The text content to send.

---

## 2. CLI Workflow (Fallback)

If the MCP server is not available or if you need to script messages externally, fall back to the `@neeraj_jangir/sendkit` CLI.

### Initialization & Configuration

Before sending messages via CLI, initialize configuration:

```bash
npx @neeraj_jangir/sendkit init --telegram-bot-token <botToken>
```

This stores the token locally under `~/.config/sendkit/config.json`.

### Sending Messages

Send a message using the following CLI arguments:

```bash
npx @neeraj_jangir/sendkit telegram <chatId> "<message>"
```

---

## 3. Choosing Between Workflows

Use the following guidelines to select the appropriate workflow:

| Workflow                   | When to Use                                                                              | Advantages                                                                                         |
| :------------------------- | :--------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------- |
| **MCP (sendKit/telegram)** | Default for agents; when interactive tool invocation is possible.                        | Automatic integration with agent planning; state is managed cleanly in environment configurations. |
| **CLI Fallback**           | When MCP fails; when running custom background scripts; or during local terminal checks. | Decoupled from agent context; simple and command-driven.                                           |

---

## 4. Manual Verification

To manually verify that SendKit is correctly set up and configured:

1. **Verify Token Existence**:
   - For MCP: Ensure `TELEGRAM_BOT_TOKEN` is present in the environment variables or client configuration.
   - For CLI: Check if `~/.config/sendkit/config.json` exists and contains a valid token.
2. **Send a Test Message**:
   - Run a test command to send a simple message to a known chatId:
     ```bash
     npx @neeraj_jangir/sendkit telegram "<chatId>" "Test message from SendKit setup verification"
     ```
   - Check if the command outputs a successful response JSON:
     ```json
     { "ok": true, "messageId": 123, "chatId": "..." }
     ```
