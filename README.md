# Roblox Manager

A native **Windows & macOS** Roblox account & multi-instance manager built with **Tauri v2 + Vite + React**.

Run multiple Roblox sessions simultaneously with different accounts — no conflicts, no session overlap.

---

## ✨ Features

### 🧩 Multi Instance
Launch **3+** separate Roblox windows at the same time, each logged into a different account. Each instance runs from its own copied `RobloxPlayerBeta.exe` so they never interfere.

### 👤 Multi Account
Store unlimited accounts with encrypted cookies on disk. Add accounts via:
- **Cookie Paste** — paste a `.ROBLOSECURITY` cookie
- **Browser Login** — authenticates through the official Roblox WebView flow
- **File Import** — drop `.roblocookie`, `.txt`, or `.json` files
- **RobloSecurity Generation** — the app can generate a session cookie from browser login

### 🎨 Fully Themeable UI *(Windows only)*
Every surface — sidebar, cards, modals, buttons, the launch window — is styled with CSS custom properties. Override colors in the built-in **App Theme** editor.

Includes **4 example themes** (Dark Red, Deep Purple, Neon Pulse, Void Drift) and support for **animated CSS + JavaScript** for dynamic effects like mouse-reactive gradients, time-based palettes, and particle overlays.

### 📦 Roblox Mods *(Windows only)*
Inject custom mods (`.js` scripts) into Roblox at launch. The app manages a local mods folder, lets you enable/disable mods per instance, and injects them after Roblox loads.

### 🚀 One-Click Place Joining
Launch instances directly into a specific game by **Place ID**, **Job ID**, or **Private Server link**. No need to navigate Roblox's UI.

### 📜 Launch History
Every instance launch is logged with timestamp, account, place ID, and server info. Browse, filter, and re-launch past sessions.

### 📌 Permanent (Pinned) Instances
Save frequently-used instance configurations (account + place + server) as pinned presets for one-click re-launch.

### 🕹️ Controls
Each running instance shows:
- **Stop** — kills the Roblox process gracefully
- **Pin** — saves as a permanent instance
- **Remove** — removes from the list (process continues running)

### ⚙️ Engine Switcher *(Windows only)*
Set custom Roblox version channels (e.g., `integration`, `preproduction`) via FFlags. The app manages version folders and applies them per instance.

### 🔧 FFlags Editor *(Windows only)*
Browse, edit, and apply Roblox FFlags (feature flags) from a built-in JSON editor with grouped categories and presets.

### 🖼️ Background Image *(Windows only)*
Set a custom wallpaper behind the entire app window. All surfaces turn translucent with backdrop-filter blur so the image shows through.

### 🔄 Auto-Rejoin *(Windows only)*
Automatically re-join a game if you get disconnected or crash — configurable per instance.

### 🎮 vJoy Virtual Controller *(Windows only)*
Emulate a virtual Xbox controller for Roblox, useful for automation or accessibility.

### 🎵 Discord Rich Presence *(Windows only)*
Shows what game/account you're playing on your Discord profile.

---

## 🖥️ Platforms

| Feature | Windows | macOS |
|---------|---------|-------|
| Multi-Instance | ✅ Copies `RobloxPlayerBeta.exe` per instance | ✅ Uses `open -n -a` |
| Account Management | ✅ | ✅ |
| Mod Injection | ✅ Via `RobloxMods` folder | ❌ |
| FFlags Editor | ✅ | ❌ |
| Engine Switcher | ✅ | ❌ |
| Cookie Login | ✅ Paste / Browser / File | ✅ Paste / Browser / File |
| App Theme Engine | ✅ Full CSS + JS sandbox | ❌ |
| Background Image | ✅ Custom wallpaper + backdrop-filter | ❌ |
| Auto-Update | ✅ Direct download via Rust (no popups) | ❌ |
| Discord RPC | ✅ | ❌ |
| Auto-Rejoin | ✅ | ❌ |
| vJoy (controller) | ✅ | N/A |

---

## 📦 Installation

### Windows
1. Download the latest `.exe` from [Releases](https://github.com/robloxmanagermacos/robloxmanager/releases)
2. Run the installer — admin required
3. Updates are handled in-app (click "Update Now" when notified)

### macOS
1. Download the latest `.dmg` from [Releases](https://github.com/robloxmanagermacos/robloxmanager/releases)
2. Open `RobloxManager.dmg` and drag to Applications

---

## 🎨 Theming

### Quick Start
Go to **Settings → App Theme** to open the theme editor. Use the color pickers or write custom CSS:

```css
:root {
  --accent: #7c3aed;
  --bg: #07070d;
  --bg-card: rgba(12, 12, 22, 0.92);
  --text: #eae8f4;
}
```

### Key Variables

| Variable | Purpose |
|----------|---------|
| `--accent` | Primary accent (buttons, progress) |
| `--accent-soft` | Subtle accent backgrounds |
| `--accent-glow` | Glow behind accent elements |
| `--accent-border` | Button borders |
| `--hover` | Hover background for buttons/cards |
| `--hover-border` | Hover border for buttons |
| `--bg` | Deepest background |
| `--bg-secondary` | Sidebar / nav |
| `--bg-tertiary` | Dropdowns / inset surfaces |
| `--bg-card` | Card background |
| `--bg-hover` | Hover highlight |
| `--overlay-bg` | Modal backdrop |
| `--border` | Subtle borders |
| `--border-bright` | Active borders |
| `--text` | Primary text |
| `--text-secondary` | Secondary text |
| `--text-muted` | Dimmed text |
| `--green` / `--green-soft` | Success colors |
| `--yellow` / `--yellow-soft` | Warning colors |
| `--radius` / `--radius-sm` | Border radii |
| `--font` / `--mono` | Font families |
| `--logo-bg` | Sidebar logo badge |
| `--logo-filter` | Logo PNG filter chain |

### JavaScript Sandbox

Embed dynamic JavaScript between `/* js */` and `/* endjs */` markers in the CSS editor:

```css
/* js */
theme.div("glow");
function tick() {
  theme.set("--spot-x", ((theme.mx + 1) * 50).toFixed(1) + "%");
  requestAnimationFrame(tick);
}
tick();
/* endjs */

.rm-el-glow {
  position: fixed;
  border-radius: 50%;
  width: 300px; height: 300px;
  top: calc(var(--spot-x, 50%) - 150px);
  left: calc(var(--spot-y, 50%) - 150px);
  background: radial-gradient(circle, var(--accent-soft), transparent 70%);
  pointer-events: none;
  z-index: 0;
}
```

**Sandbox API:**
- `theme.set(name, value)` — set CSS variable on `:root`
- `theme.setMultiple({...})` — batch set
- `theme.addDiv(name)` — create `<div class="rm-el-{name}">` in `.app-shell`
- `theme.removeDiv(name)` — remove that div
- `theme.clearDivs()` — remove all theme divs
- `theme.mx` / `theme.my` — mouse position (-1 to 1)
- `theme.log(msg)` — console log

> ⚠️ The JS runs in a sandboxed iframe with no access to the app's DOM, `fetch`, `localStorage`, or `document`.

---

## 🔐 Security

- **Cookies are encrypted** on disk using a session-scoped key stored in the OS temp directory with a hidden backup
- **No data is sent to external servers** — all auth happens client-side
- **The updater downloads directly** in Rust (no PowerShell/cmd windows), verifies the download, then launches a UAC/elevated replacement script
- **Mods are user-controlled** — the app never downloads mods automatically

---

## 📜 Terms of Use

By using Roblox Manager, you agree that:

- This software is provided **as-is** with no warranty
- Authentication data (cookies) is stored **locally and encrypted**, but no local storage is immune if your device is compromised
- Roblox Manager is **not affiliated with, endorsed by, or associated with Roblox Corporation**
- The developer is **not responsible** for account compromise due to malware, unauthorized system access, or third-party software
- Using multiple accounts or automation tools may be subject to Roblox's Terms of Service — use responsibly

---

## 💾 Data Locations

**Windows:**
```
Accounts + settings: %APPDATA%\RobloxManager\
Theme CSS:          %APPDATA%\RobloxManager\theme.css
Mods:               %APPDATA%\RobloxManager\mods\
```

**macOS:**
```
Accounts + settings: ~/Library/Application Support/RobloxManager/
Theme CSS:          ~/Library/Application Support/RobloxManager/theme.css
Mods:               ~/Library/Application Support/RobloxManager/mods/
```

---

## 🆘 Getting Your `.ROBLOSECURITY` Cookie

1. Open **roblox.com** in a browser and sign in
2. Press **F12** → **Application** tab → **Cookies** → `https://www.roblox.com`
3. Find `.ROBLOSECURITY`, copy the value
4. Paste in **Add Account → Paste Cookie**

Or use the **Browser Login** button for an automated flow.

---

## 🏗️ Tech Stack

- **Frontend**: React 18 + TypeScript + Vite
- **Backend**: Rust (Tauri v2)
- **State**: Zustand
- **CSS**: CSS Modules + CSS custom properties for theming
- **Editor**: CodeMirror 6 (CSS + JS editor)
- **Animations**: Framer Motion
