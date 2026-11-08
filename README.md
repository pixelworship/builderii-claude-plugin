# Builderii for Claude

Your Builderii boards, right in Claude. Ask what's on a board and get its flows, screens, mind maps and sticky notes as text. Brainstorm onto a board as sticky notes grouped by theme. Get a review of a flow or wireframes, with comments pinned to the screens and steps they're about.

## Install

- **Claude (claude.ai, the desktop app, Cowork):** Customize → Plugins → Add → Upload plugin, and choose `builderii-claude-plugin.zip` (Builderii's Settings → Connected apps → Download, or `dist/builderii-claude-plugin.zip` after `npm run build`). Then connect it from a chat, as below.
  Don't compress this folder yourself (Finder's Compress): that zip holds everything under `claude-plugin/` (with `.git`, `node_modules` and `__MACOSX` files), so `.claude-plugin/plugin.json` isn't at its root and the upload fails.
- **Claude Code:** `claude plugin install builderii --marketplace https://app.builderii.com/plugins/claude/marketplace.json`, then `/mcp` → builderii → Authenticate.
- **Just the connection:** Settings → Connectors → Add custom connector, named Builderii, with the address `https://app.builderii.com/mcp`.

Builderii's **Settings → Connected apps** has the download and these steps.

## Connect

The plugin connects to your Builderii account through Builderii's MCP server (`https://app.builderii.com/mcp`). You sign in to Builderii once and allow Claude.

- **Claude:** from a chat (claude.ai, or the Claude desktop app's Chat), open **Customize → Plugins → Builderii → Connectors** (on the web, [claude.ai/customize/plugins](https://claude.ai/customize/plugins)), select **builderii**, **Add** it, then **Connect**. Sign in to Builderii and **Allow**. Builderii is then one of your account's connectors, in chat, Cowork and Claude Code.
- **The desktop app's Code tab:** **Connect** there stays greyed out ("You're not connected to builderii yet") until a Code session that loaded the plugin is open, and then signs in only Claude Code on that computer: the plugin still says **Not added**, and chat and Cowork can't use it. Connect from a chat as above instead.
- **Claude Code in a terminal:** run `/mcp`, choose builderii and authenticate.

It acts with your access: any board you can open in Builderii, it can open. Disconnect it any time in Builderii under **Settings → Connected apps**.

## What the plugin can reach

The plugin is six skills (instructions in Markdown), two images and the address of Builderii's server, `https://app.builderii.com/mcp`, with no headers or environment values. It runs nothing on your computer: no scripts, hooks or commands, and it reads no files, environment variables or saved passwords. You sign in to Builderii in your browser and allow Claude, as with any connector; Claude keeps that connection, and the plugin never sees or sends it.

## Skills

- **builderii**: how to work in your boards: which tool for what (drawing it yourself or with Builderii's AI, changing diagrams in place), finding boards before making new ones, linking them.
- **app-screens** (`/builderii:app-screens`): design app or website screens as a clickable prototype (from a description or a screenshot).
- **diagram** (`/builderii:diagram`): draw a flowchart, mind map, sequence diagram, sitemap, roadmap or database diagram, or change one on a board.
- **brainstorm** (`/builderii:brainstorm`): put ideas on a board as sticky notes grouped by theme.
- **feedback** (`/builderii:feedback`): review a flow, wireframes or a prototype and leave comments on the right screens.
- **summarize** (`/builderii:summarize`): explain a board, or answer a question from it.

## Development

This repository is the plugin: `.claude-plugin/plugin.json`, `.mcp.json` (the remote HTTP server), `skills/` (Agent Skills: SKILL.md with name and description only) and `assets/` (icon.png 128 px, logo.png 512 px). `.claude-plugin/marketplace.json` lets Claude Code add this repository itself as a marketplace.

```bash
npm run build   # dist/builderii, dist/builderii-claude-plugin.zip, and copies into ../web/public/plugins/
npm run check   # claude plugin validate (needs Claude Code)
claude --plugin-dir dist/builderii   # try a local build
```

The build checks the manifest, the skills and the server address, then zips the plugin reproducibly (fixed timestamps, no folder entries), so an unchanged plugin always has the same SHA-256. When it's checked out at `builderii/infrastructure/claude-plugin`, it copies the zip to the web app (`web/public/plugins/builderii-claude-plugin.zip`) with the marketplace Claude Code installs from (`web/public/plugins/claude/marketplace.json`, pinned by that SHA-256). Bump `version` in plugin.json when a skill or the manifest changes, build, then deploy the web app.

Anthropic's plugin directory reads the public repository [pixelworship/builderii-claude-plugin](https://github.com/pixelworship/builderii-claude-plugin) (its `main`): after a build, copy `dist/builderii` over a checkout of it (it keeps its own `.claude-plugin/marketplace.json`) and push.

The MCP server itself is in the Builderii repository (`infrastructure/cdk/lib/lambdas/api/mcp.mjs`); it signs people in with OAuth (Builderii's `/connect` page, dynamic client registration, PKCE).
