# Installing NextSteps for VS Code Copilot

Step-by-step guide to get `cindula/nextsteps` working in your VS Code workspace so Copilot appends smart next-step suggestions after every response.

---

## Prerequisites

- **VS Code** (latest stable)
- **GitHub Copilot** extension installed and signed in
- **Node.js** 18+ (for tessl CLI)

---

## Step 1 — Install tessl CLI

```bash
curl -fsSL https://tessl.io/install.sh | sh
```

After installation, add tessl to your PATH:

```bash
# For zsh (macOS default):
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# For bash:
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Verify:

```bash
tessl --version
```

---

## Step 2 — Log in to tessl

```bash
tessl login
```

Follow the browser prompt to authenticate with your tessl.io account.

---

## Step 3 — Initialize tessl in your project

Navigate to your VS Code workspace root and run:

```bash
cd /path/to/your/project
tessl init --agent copilot-vscode
```

This creates:
- `tessl.json` — project dependency manifest
- `.vscode/mcp.json` — MCP server config for Copilot
- `.github/skills/` — synced skill files that Copilot reads
- `.vscode/skills/` — synced skill files (VS Code local)

---

## Step 4 — Install cindula/nextsteps

```bash
tessl install cindula/nextsteps
```

Expected output:

```
✔ Installed 1 tile:
  cindula/nextsteps@0.3.3

✔ Quality   94%
✔ Uplift    ↑1.30x
✔ Security  Passed · No known issues
```

This automatically syncs to `.github/skills/tessl__nextsteps/SKILL.md`.

---

## Step 5 — Reload VS Code

Press `⌘+Shift+P` (macOS) or `Ctrl+Shift+P` (Windows/Linux), then type:

```
Developer: Reload Window
```

This forces Copilot to pick up the new skill files.

---

## Step 6 — Verify installation

### Check tessl list

```bash
tessl list
```

You should see:

```
cindula/nextsteps  0.3.3  0.3.3  Synced
```

### Check skill files exist

```bash
ls .github/skills/tessl__nextsteps/
```

Should show: `SKILL.md`, `assets/`, `references/`

### Check preferences

```bash
cat .nextsteps/PREFERENCES.md
```

If this file doesn't exist yet, NextSteps will create it automatically on first activation (cold-start protocol).

---

## Step 7 — Test it

Open VS Code Copilot Chat and send any prompt, for example:

> "How do I add authentication to my Express app?"

After the answer, you should see appended:

```
## ⚡ Next Steps

1. ⚡ **Add refresh token rotation** — Prevents token theft
2. 🔧 **Create auth middleware for protected routes** — One file, all routes covered
3. 🔍 **Deep-dive: Compare bcrypt vs Argon2 for password hashing** — ...
4. 💡 **Consider: Add audit logging to auth events** — ...
5. ✅ **Quick win: Add helmet.js security headers** — 2 lines of code
```

---

## Customization

Once active, just say it naturally in chat:

| What you say | What happens |
|---|---|
| "show me 3 next steps" | Changes suggestion count to 3 |
| "compact format" | Switches to one-liner style |
| "disable next steps" | Turns off suggestions |
| "enable next steps" | Turns them back on |
| "hide the footer" | Removes the learning tip |
| "reset next steps settings" | Restores all defaults |

---

## Troubleshooting

### NextSteps not appearing after responses

1. **Check skill sync**: Run `tessl list` — is `cindula/nextsteps` showing as "Synced"?
2. **Check skill files**: Does `.github/skills/tessl__nextsteps/SKILL.md` exist?
3. **Reload VS Code**: `⌘+Shift+P` → "Developer: Reload Window"
4. **Re-init for Copilot**: `tessl init --agent copilot-vscode --project-dependencies skip`

### Preferences file missing

NextSteps creates `.nextsteps/PREFERENCES.md` on first activation. If it doesn't exist, the skill will cold-start automatically.

### Want to add .nextsteps/ to .gitignore

```bash
echo '.nextsteps/' >> .gitignore
```

This keeps personal preference data out of version control.

---

## Uninstall

```bash
tessl uninstall cindula/nextsteps
```

---

## Links

- **Registry**: https://tessl.io/registry/cindula/nextsteps
- **GitHub**: https://github.com/cindulasai/nextsteps
- **tessl docs**: https://tessl.io/docs
