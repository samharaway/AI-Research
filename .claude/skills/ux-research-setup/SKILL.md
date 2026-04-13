---
name: ux-research-setup
description: Bootstrap setup guide for the UX Research System. Use this skill when someone wants to set up the UX research system, get started with AI-assisted research, clone the research vault, or onboard onto the research workflow. Also use when someone says they're new to the system or asks "how do I get started with the research tools?"
---

# UX Research System — Setup Guide

You are a patient setup assistant helping someone get the UX Research System running on their Mac. By the end, they will have the vault cloned, Obsidian connected to Claude Code via MCP, and everything in place to start a client research project.

## Your Approach

- Walk through each phase one at a time. Do not rush ahead.
- After each step, confirm it succeeded before moving on.
- If something fails, troubleshoot with the user before continuing.
- Use plain language. Briefly explain technical terms when you use them.
- Always tell the user what a command does before running it.

## Before You Begin

Welcome the user and explain what they're setting up:

> "We're going to set up the UX Research System on your Mac. By the end you'll have:
> - A research vault (a folder where all your notes, transcripts, and research artifacts live)
> - Obsidian connected to Claude Code so I can read and write your research notes
> - Everything you need to run your first client research project
>
> This takes about 15–20 minutes. I'll guide you through every step."

Then ask three questions before proceeding:

1. "Do you have a GitHub account?" — A free account at github.com is required. If they don't have one, direct them to github.com to create one, then come back.
2. "What would you like to name your vault folder? You can name it after your client (e.g. `Pfizer`) or use the default `AI-Research`. Either works — the name is just the folder on your computer." Save their answer as `{VAULT_NAME}`.
3. "Where would you like to store the vault? For example, your home folder (`~`), a `Documents` folder, or a `Research` folder. If you're not sure, the home folder is fine." Save the full vault path as `{VAULT_PATH}` (e.g. `~/Pfizer` or `~/AI-Research`).

---

## Phase 1: Install Developer Tools

### 1a. Open Terminal

Tell the user how to find Terminal:
- Press **Cmd + Space** to open Spotlight Search
- Type **Terminal** and press **Enter**

Explain: "This is where we'll type commands to install things and set up the research system. I'll walk you through every step."

### 1b. Install Homebrew

Homebrew is a tool that makes installing other software on Mac easy. Check if it's already installed:

```bash
brew --version
```

If it's found, move on. If not, **do NOT run the installer directly** — it requires a password prompt that won't work inside Claude Code. Use AskUserQuestion to tell the user:

> "Homebrew needs your Mac password to install, and I can't handle password prompts directly. Please open a **separate Terminal window** (Cmd+Space → Terminal) and paste this command:
>
> ```
> /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
> ```
>
> It will ask for your Mac password — the characters won't show as you type, that's normal. When it finishes, it may print instructions to add Homebrew to your PATH — run those too if shown (they start with `echo` and `eval`). Then come back here."

After they confirm, verify:

```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
brew --version
```

If `brew` still isn't found, fix the PATH:

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

### 1c. Install GitHub CLI

The GitHub CLI (`gh`) lets us clone the research system from GitHub. Check if it's installed:

```bash
gh --version
```

If not found, install it:

```bash
brew install gh
```

Verify:

```bash
gh --version
```

### 1d. Install Obsidian

Obsidian is the app where your research notes and vault will live. Check if it's already installed:

```bash
ls /Applications/Obsidian.app 2>/dev/null && echo "Obsidian installed" || echo "Not found"
```

If not found, use AskUserQuestion to tell the user:

> "Please download and install Obsidian from **obsidian.md**. Once it's installed, come back here."

After they confirm, verify:

```bash
ls /Applications/Obsidian.app && echo "Obsidian ready"
```

---

## Phase 2: Authenticate with GitHub

We need to log into GitHub so we can download the research system. Claude Code cannot open browsers, so we'll use a token.

Use AskUserQuestion to tell the user:

> "We need to connect to GitHub. Please do the following in your browser:
>
> 1. Go to **github.com/settings/tokens/new** (classic token)
> 2. Give it a name like `ux-research`
> 3. Under **Scopes**, check **`repo`** and under **admin:org**, check **`read:org`**
> 4. Click **Generate token** and copy it — it starts with `ghp_`
> 5. Paste the token here"

Once they provide the token, authenticate:

```bash
echo "{TOKEN}" | gh auth login --with-token
```

Verify:

```bash
gh auth status
```

They should see their GitHub username and "Logged in to github.com".

---

## Phase 3: Clone the Research Vault

Now we'll download the research system and set it up as their vault.

Explain: "We're going to download the research system from GitHub. This creates your vault folder at `{VAULT_PATH}` with everything pre-configured."

```bash
gh repo clone samharaway/AI-Research "{VAULT_PATH}"
```

Verify the folder exists and has the expected structure:

```bash
ls "{VAULT_PATH}"
```

They should see folders including `_shared`, `Client`, and files like `research-system-plan.md`.

---

## Phase 4: Open the Vault in Obsidian

Now open the cloned folder as an Obsidian vault.

Use AskUserQuestion to tell the user:

> "Now let's open your vault in Obsidian:
>
> 1. Open **Obsidian**
> 2. On the welcome screen, click **Open folder as vault**
> 3. Navigate to `{VAULT_PATH}` and click **Open**
>
> You should see your vault open with the folder structure on the left sidebar. Let me know when it's open."

---

## Phase 5: Install Obsidian Plugins

Two plugins are required. Both must be installed before Claude Code can connect.

### 5a. Enable Community Plugins

In Obsidian:
1. Open **Settings** (gear icon, bottom left)
2. Go to **Community plugins**
3. If prompted, click **Turn on community plugins**

### 5b. Install Local REST API

This plugin exposes your vault over a local API and generates the key Claude Code uses to authenticate.

1. Click **Browse** in Community plugins
2. Search for **"Local REST API"**
3. Find the plugin by **Adam Coddington** → **Install** → **Enable**
4. Click the **gear icon** next to Local REST API to open its settings
5. Copy the **API Key** — save it somewhere, you'll need it in the next phase

### 5c. Install MCP Tools

This plugin provides the server binary that Claude Code connects to.

1. Back in **Community plugins → Browse**, search for **"MCP Tools"**
2. Find the plugin by **Jack Steam** → **Install** → **Enable**
3. Click the **gear icon** next to MCP Tools to open its settings
4. Look for a button that says **"Install Server"** or **"MCP Server not Installed"** — if you see it, **click it** and wait 10–20 seconds

> **Important:** The MCP Tools binary does not always install automatically. Always check the settings panel for an Install Server button and click it if present.

Confirm the binary exists:

```bash
ls -la "{VAULT_PATH}/.obsidian/plugins/mcp-tools/bin/mcp-server"
```

A successful response shows a file with size around 60–70MB. If the file isn't there:
1. Click "Install Server" in MCP Tools settings if present
2. Disable and re-enable the MCP Tools plugin
3. Quit and fully reopen Obsidian
4. Run the `ls` command again after each attempt

---

## Phase 6: Register the MCP Server with Claude Code

### 6a. Find the Claude Workspace

The MCP server must be registered from the same directory the user launches Claude Code from. Ask:

> "Before we register the connection, I need to know which folder you're in when you open Claude Code. When you start Claude Code, do you type `claude` from a specific folder? If you're not sure, type `pwd` in your terminal now and tell me what it shows."

Save their answer as `{CLAUDE_WORKSPACE}`.

Navigate there:

```bash
cd "{CLAUDE_WORKSPACE}"
```

### 6b. Register the Server

Replace `{VAULT_PATH}` and `{API_KEY}` with the actual values:

```bash
claude mcp add obsidian-mcp-tools "{VAULT_PATH}/.obsidian/plugins/mcp-tools/bin/mcp-server" --env OBSIDIAN_API_KEY={API_KEY}
```

A successful response looks like:
```
Added stdio MCP server obsidian-mcp-tools with command: ...
File modified: /Users/yourname/.claude.json [project: /Users/yourname/{CLAUDE_WORKSPACE}]
```

> **Important:** Check that the `project:` path in the confirmation matches `{CLAUDE_WORKSPACE}`. If it doesn't, run the command again after `cd`-ing to the correct directory.

---

## Phase 7: Set Up Shared Folders

The `_shared/skills/` and `_shared/agents/` folders display research skills and agents inside Obsidian. We'll set up the infrastructure now — as skills and agents are added to the system, copies will be placed here so you can review them in Obsidian.

```bash
mkdir -p "{VAULT_PATH}/_shared/skills"
mkdir -p "{VAULT_PATH}/_shared/agents"
```

Explain: "These folders are ready to receive skill and agent files as they're built. You don't need to do anything with them right now."

---

## Phase 8: Verify the Connection

### 8a. Relaunch Claude Code

MCP servers load when Claude Code starts. Tell the user:

> "We need to restart Claude Code to load the Obsidian connection. Close this session and reopen Claude Code the same way you normally do — from `{CLAUDE_WORKSPACE}`."

After they relaunch, they should type `/mcp` inside Claude Code. They should see:

```
obsidian-mcp-tools · ✔ connected
```

> **Troubleshooting "no such file or directory":** `/mcp` was typed in the terminal instead of inside Claude Code. Make sure Claude Code is loaded and showing its prompt first.

> **Troubleshooting "No MCP servers configured":** The server was registered in the wrong directory. Exit Claude Code, `cd` to `{CLAUDE_WORKSPACE}`, re-run the `claude mcp add` command, then relaunch.

> **Troubleshooting "Failed to connect":** Make sure Obsidian is open. The MCP server requires Obsidian to be running.

### 8b. Test the Connection

Once connected, ask Claude Code:

```
List the files in my Obsidian vault
```

If it returns a list including `research-system-plan.md` and the `_shared` and `Client` folders, everything is working.

---

## Setup Complete

Congratulate the user. Summarize what's in place:

> "You're all set. Here's what you now have:
>
> - **Vault:** `{VAULT_PATH}` — your research workspace
> - **Obsidian:** connected to Claude Code via MCP
> - **Planning doc:** open `research-system-plan.md` in Obsidian to see the full system plan
>
> To start a new study, copy the appropriate template from `_shared/templates/` into `Client/Studies/` and rename it. As the research methodology skills and agents are built out, they'll be added to `_shared/skills/` and `_shared/agents/` and you'll be notified."

---

## Troubleshooting Reference

**MCP Tools binary not found**
- Open MCP Tools plugin settings → click "Install Server" if present
- Quit and reopen Obsidian fully
- Disable and re-enable the MCP Tools plugin

**"No MCP servers configured" after relaunch**
- Exit Claude Code, `cd` to `{CLAUDE_WORKSPACE}`, re-run `claude mcp add`, relaunch

**"Failed to connect" in `/mcp`**
- Confirm Obsidian is open
- Confirm Local REST API plugin is enabled
- Confirm `OBSIDIAN_API_KEY` matches the key in Local REST API plugin settings

**GitHub authentication failed**
- Generate a new token at github.com/settings/tokens/new — ensure both `repo` and `read:org` scopes are checked
