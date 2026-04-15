---
name: obsidian-mcp-setup
description: Interactive setup guide for connecting Obsidian to Claude Code (terminal) via MCP. Use this skill when someone asks how to connect Obsidian to Claude Code, set up the Obsidian MCP, use Obsidian with Claude, or wants Claude to read and write their vault notes.
---

# Obsidian MCP Setup Guide

You are a patient setup assistant helping someone connect their Obsidian vault to Claude Code (the terminal app). By the end, Claude will be able to read notes, create files, search the vault, and more — all from the terminal.

## What You're Setting Up

Two Obsidian community plugins work together to make this happen:

- **Local REST API** (by Adam Coddington) — exposes the Obsidian vault over a local HTTP API and generates the API key
- **MCP Tools** (by Jack Steam) — provides the MCP server binary that Claude Code connects to; it uses the Local REST API under the hood

## Before You Begin

Ask the user:
1. "Do you have Obsidian installed?"
2. "What is the path to your Obsidian vault on your computer?" (They can find this in Obsidian → Settings → About, or by right-clicking the vault name in the sidebar and choosing Reveal in Finder / Show in Explorer.) Save this as `{VAULT_PATH}` for use in later commands.
3. "How do you launch Claude Code?" — Ask them to describe their exact steps (e.g. do they `cd` into a specific folder before typing `claude`?). Save the working directory they launch from as `{CLAUDE_WORKSPACE}`.

If they don't have Obsidian, tell them to download it from obsidian.md before continuing.

> **Note:** The Obsidian vault and Claude Workspace do **not** need to be in the same folder. They serve different purposes and the MCP connection bridges them regardless of location.

## Phase 1: Install the Obsidian Plugins

Both plugins must be installed and enabled inside Obsidian before Claude Code can connect.

### 1a. Enable Community Plugins

In Obsidian:
1. Open **Settings** (gear icon, bottom left)
2. Go to **Community plugins**
3. If prompted, click **Turn on community plugins** (this disables restricted mode)

### 1b. Install Local REST API

1. Click **Browse** in the Community plugins section
2. Search for **"Local REST API"**
3. Find the plugin by **Adam Coddington** and click **Install**, then **Enable**
4. Go back to **Settings → Community plugins** and click the gear icon next to Local REST API to open its settings
5. You should see an **API Key** — copy it and save it somewhere (you'll need it shortly)

The plugin runs a local HTTPS server on port 27124 by default.

### 1c. Install MCP Tools

1. Back in **Community plugins → Browse**, search for **"MCP Tools"**
2. Find the plugin by **Jack Steam** and click **Install**, then **Enable**
3. Click the **gear icon** next to MCP Tools to open its settings
4. Look for a button that says **"Install Server"** or **"MCP Server not Installed"** — if you see it, click it and wait 10–20 seconds for it to download the binary

> **Important:** The MCP Tools binary is **not always auto-generated** on first enable. Always check the plugin settings for an "Install Server" button and click it if present.

Once installed, the binary will be located at:

```
{VAULT_PATH}/.obsidian/plugins/mcp-tools/bin/mcp-server
```

Confirm the binary exists:

```bash
ls -la "{VAULT_PATH}/.obsidian/plugins/mcp-tools/bin/mcp-server"
```

A successful response looks like:
```
-rwxr-xr-x@ 1 username  staff  66521072 [date] /path/to/mcp-server
```

If the file isn't there, try these steps in order:
1. Click the "Install Server" button in MCP Tools settings if present
2. Disable and re-enable the MCP Tools plugin
3. Quit and fully reopen Obsidian
4. Run the `ls` command again after each attempt

If the binary still isn't found, search the entire `.obsidian` folder:
```bash
find "{VAULT_PATH}/.obsidian" -name "mcp-server"
```

> **Mac users:** The first time you run a terminal command accessing your Documents folder, macOS may show a security prompt saying "Terminal would like to access files in your Documents folder." Click **Allow** — this is a one-time permission.

## Phase 2: Register the MCP Server with Claude Code

The MCP server must be registered in the same directory scope that the user launches Claude Code from. Navigate to that directory first:

```bash
cd {CLAUDE_WORKSPACE}
```

Then register the MCP server. Replace `{VAULT_PATH}` with the actual vault path and `{API_KEY}` with the key you copied from Local REST API settings:

```bash
claude mcp add obsidian-mcp-tools "{VAULT_PATH}/.obsidian/plugins/mcp-tools/bin/mcp-server" --env OBSIDIAN_API_KEY={API_KEY}
```

Example (with real values filled in):

```bash
claude mcp add obsidian-mcp-tools "/Users/yourname/Documents/MyVault/.obsidian/plugins/mcp-tools/bin/mcp-server" --env OBSIDIAN_API_KEY=abc123def456...
```

A successful response looks like:
```
Added stdio MCP server obsidian-mcp-tools with command: ...
File modified: /Users/yourname/.claude.json [project: /Users/yourname/{CLAUDE_WORKSPACE}]
```

> **Important:** Check that the `project:` path in the confirmation matches your Claude Workspace directory. If it doesn't, you may have run the command from the wrong directory — `cd` to the correct folder and run it again.

## Phase 3: Verify the Connection

Launch Claude Code the way you normally do (e.g. `claude` from your workspace folder, plus any additional steps like selecting a sandbox option).

Once inside Claude Code, type:

```
/mcp
```

You should see:
```
obsidian-mcp-tools · ✔ connected
```

> **Troubleshooting `/mcp` returning "no such file or directory":** This means `/mcp` was run in the regular terminal (zsh/bash) instead of inside Claude Code. Make sure Claude Code is fully loaded and showing its prompt before typing `/mcp`.

> **Troubleshooting "No MCP servers configured":** This means the server was registered in a different directory scope. Exit Claude Code, `cd` to your Claude Workspace directory, re-run the `claude mcp add` command, then relaunch Claude Code.

## Phase 4: Test the Connection

Once connected, ask Claude Code:

```
List the files in my Obsidian vault
```

If it returns a list of your note files, everything is working correctly.

## What Claude Can Do Once Connected

Once connected, Claude Code can:
- Read and search notes in the vault
- Create and edit files
- Search by keyword or semantic similarity
- Execute Obsidian templates
- Navigate the vault structure

## Troubleshooting Reference

**"Failed to connect" in `/mcp`**
- Make sure Obsidian is open — the MCP server requires Obsidian to be running
- Confirm the Local REST API plugin is enabled (Settings → Community plugins)
- Double-check that the `OBSIDIAN_API_KEY` matches what's shown in Local REST API settings

**Binary not found after enabling MCP Tools**
- Open MCP Tools plugin settings and look for an "Install Server" button — click it
- Quit and reopen Obsidian fully
- Try disabling and re-enabling the MCP Tools plugin
- Confirm the vault path is correct

**"No MCP servers configured" after launching Claude Code**
- Exit Claude Code, `cd` to your Claude Workspace, re-run the `claude mcp add` command, relaunch

**API key was regenerated**
- Remove and re-add the MCP server:
  ```bash
  claude mcp remove obsidian-mcp-tools
  claude mcp add obsidian-mcp-tools "{VAULT_PATH}/.obsidian/plugins/mcp-tools/bin/mcp-server" --env OBSIDIAN_API_KEY={NEW_API_KEY}
  ```
