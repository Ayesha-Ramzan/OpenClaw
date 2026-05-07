# OpenClaw Complete Installation Guide (Windows WSL)

---


# PART 1 — Install WSL and Ubuntu (Only Once)

## Step 1 — Open PowerShell in Administrator Mode

**Why Administrator mode?** Installing WSL requires special permissions from Windows. It does not work in normal mode — you will get a permission denied error.

**How to open:**
- Press the Windows key
- Type "powershell"
- **Right click** on Windows PowerShell in the results
- Click **"Run as administrator"**
- If Windows asks "Allow this app to make changes?" — click **Yes**

A blue terminal window is now open. All work starts from here.

---

## Step 2 — Install WSL and Ubuntu

In PowerShell, type this command and press Enter:

```
wsl --install -d Ubuntu
```

**What this command does:**
- Enables the WSL feature in Windows
- Downloads and installs the WSL2 kernel
- Downloads and installs Ubuntu

**How long will it take:** Depends on your internet speed — 5 to 15 minutes.

**What you will see on screen:** Download progress and installation messages. Everything happens automatically.

---

## Step 3 — Restart Your Computer

After installation, Windows will ask for a restart. Use this command:

```
restart-computer
```

Or restart manually from the Start menu. **Restart is mandatory** — WSL will not work properly without it.

---

## Step 4 — Set Up Ubuntu (After Restart)

After restarting, **Ubuntu will open automatically** and ask you to create a username and password.

**For the username:**
- Type any short name — like `ali` or `ahmed`
- No spaces allowed
- Use lowercase

**For the password:**
- Set any password you will remember
- **Important:** When you type the password, nothing shows on screen — this is normal. Linux does not show dots either while typing a password
- Press Enter when done
- Type the same password again to confirm

**This account is completely separate from your Windows login.** This is your Linux account — you will need this password for `sudo` commands later.

---

## Step 5 — Verify Ubuntu Installed Correctly

In PowerShell (not Ubuntu) run this command:

```
wsl -l -v
```

**Correct output looks like this:**
```
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

- STATE: Running — means Ubuntu is running
- VERSION: 2 — means WSL2, which is what we need

If you see this, Ubuntu is perfectly installed.

---

## Step 6 — Go Into the Ubuntu Terminal

From now on, **all work happens inside the Ubuntu terminal** — not PowerShell.

Two ways to open Ubuntu:
1. Type "Ubuntu" in the Start menu and press Enter
2. Or type `wsl` in any PowerShell window and press Enter

The Ubuntu terminal starts with a green prompt or dollar sign (`$`), like:
```
ali@DESKTOP:~$
```

**From today, every command goes here — not in PowerShell.**

---

# PART 2 — Install OpenClaw

## Step 7 — Install OpenClaw (Inside Ubuntu Terminal)

In the Ubuntu terminal, type this command:

```
curl -fsSL https://openclaw.ai/install.sh | bash
```

**What this command does:**
- Downloads the OpenClaw installer from the internet
- Automatically detects the OS (recognizes Linux)
- Checks for Node.js — installs it automatically if not found
- Installs the OpenClaw npm package
- Creates the `~/.openclaw/` configuration folder

**This will take a few minutes.** Many lines will appear on screen — that is normal.

**After successful installation, you will see this line:**
```
OpenClaw vX.X.X (latest)
```

---

## Step 8 — Fix PATH (If Needed)

After installation, check:

```
openclaw --version
```

**If you see:** `OpenClaw vX.X.X` — everything is fine, go to Step 9.

**If you see:** `command not found` — fix the PATH:

```
echo 'export PATH="$HOME/.openclaw/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

**Then check again:**
```
openclaw --version
```

Now `OpenClaw vX.X.X` should appear.

**What is PATH?** When you type a command, Ubuntu searches for that program in a specific list of folders. PATH is the name of that list. OpenClaw's folder needs to be added to that list — otherwise Ubuntu cannot find it.

---

## Step 9 — Direct npm Install (If the Above Fails)

If the curl command fails, use this backup method:

```
npm install -g openclaw@latest
```

`-g` means install globally — not just in one folder, but for the entire system.

---

# PART 3 — Setup Wizard (First Time Configuration)

## Step 10 — Start the Wizard

```
openclaw onboard --install-daemon
```

**What does `--install-daemon` do?**
This makes the gateway a permanent service in Ubuntu. Meaning every time you open Ubuntu, the gateway automatically starts — you do not have to manually start it every time.

**Must remember:**
- Do not close the terminal
- Do not skip the wizard
- Read whatever it asks carefully

**If the wizard ever closes, restart it:**
```
openclaw setup --wizard
```

---

## Step 11 — Security Warning — Read It

The **first screen** of the wizard is a security warning. This is not a formality — please read it.

OpenClaw itself says:
- This is a **hobby project**, still in beta
- A **bad prompt can make the agent do unsafe things**
- Follow the security baseline

You must acknowledge:
**"I understand this is powerful and inherently risky. Continue?"**

**Why is this important?** The agent can have access to your email, calendar, and files. This is a high-value target. The habit of reading security warnings starts here.

---

## Step 12 — Choose LLM Provider (The AI Brain)

The wizard will show 25+ providers. **Choose Google Gemini** — it is free, no credit card needed, and has the most free requests.

**How to create an API Key:**
1. Go to browser: `aistudio.google.com/app/api-keys`
2. Log in with your Google account
3. Click "Create API Key"
4. Copy the key
5. Paste it in the wizard

**Completely free — no credit card needed.**

---

## Step 13 — Select a Model

The wizard will ask you to choose a model.

**Choose:** `google/gemini-3.1-flash-lite-preview`

**Why this one?** This model has the most free daily requests — enough for this entire chapter.

**Free tier limits:**

| Model | Requests/minute | Requests/day |
|-------|----------------|--------------|
| gemini-3.1-flash-lite-preview | 15 | 500 |
| gemini-2.5-flash | 10 | 250 |
| gemini-2.5-pro | 5 | 50 |

**If quota runs out:**
```
openclaw configure --section model
```
Run this command and choose `gemini-2.5-flash` — it has a separate quota.

---

## Step 14 — Changing Model or API Key Later

### Change model:
```
openclaw config get agents.defaults.model
openclaw config set agents.defaults.model.primary "google/gemini-2.5-flash"
openclaw gateway restart
```

### 4 ways to change API Key:

**Option 1 — Safest (recommended for most users):**
```
openclaw configure --section model
```
The wizard reruns — paste the new key.

**Option 2 — Edit config file directly:**
Open `~/.openclaw/openclaw.json` and update:
```json
{
  "models": {
    "providers": {
      "google": { "apiKey": "paste-new-key-here" }
    }
  }
}
```
Then:
```
openclaw gateway restart
```

**Option 3 — Environment variable (advanced):**
```
export GOOGLE_API_KEY="your-key"
```
In config, reference it as `${GOOGLE_API_KEY}`. Keeps secrets out of the config file.

**Option 4 — Switch to browser login instead of key:**
```
openclaw models auth login --provider google-gemini-cli --set-default
```
A Google sign-in browser window will open.

---

### ⚠️ Auth Cache Issue — Very Important

OpenClaw saves credentials here:
```
~/.openclaw/agents/main/agent/auth-profiles.json
```

**Problem:** This cache file takes higher priority than environment variables. Meaning even if you set a fresh key, the old expired cached key will still be used.

**Fix — whenever you get an auth error:**
```
rm ~/.openclaw/agents/main/agent/auth-profiles.json
```
Then reconfigure using Option 1.

**This is a cache file, not your main config.** Deleting it keeps `~/.openclaw/openclaw.json` safe.

---

# PART 4 — Connect WhatsApp Channel

## Step 15 — First Decide: Which Number to Use?

Before connecting WhatsApp, you need to make one decision.

### Option A — Dedicated Number (Recommended ✅)

This means getting a **separate second number** where only OpenClaw runs. Your personal WhatsApp stays completely safe.

**Where to get one:**
- A spare SIM lying around at home
- Any old number that is not being used
- Google Voice (free in the US)
- Any prepaid SIM

**Setup (~5 minutes):**
1. Download **WhatsApp Business** on your phone (from App Store or Play Store — completely free)
2. Register WhatsApp Business with that second number
3. Confirm the SMS code

Now you have two WhatsApps on your phone — regular WhatsApp on your personal number, WhatsApp Business on the second number. Both run side by side.

**Android users (late 2023+):** No need for WhatsApp Business — regular WhatsApp has a built-in "dual account" feature.

---

### Option B — Personal Number ⚠️

Use your existing personal WhatsApp directly. No setup — scan the QR code directly.

**But there are risks — please read:**

**Risk 1 — Account Ban:** The WhatsApp plugin uses the Baileys library which reverse-engineers the WhatsApp Web protocol. This is not Meta's official API and violates their Terms of Service. Meta can permanently ban accounts running unofficial automation without any warning — no appeal process.

**Risk 2 — Privacy:** When a stranger messages OpenClaw, the auto-reply contains your bound phone number. With Option B, that is your real personal number exposed to anyone who messages the agent.

**For learning:** The risk is lower (hidden behind Pairing mode). But never use Option B for production.

---

### Not Ready to Decide?

Choose **Telegram or Discord** in the wizard instead. To add WhatsApp later:
```
openclaw configure --section channels
```

---

## Step 16 — Scan the QR Code

In the wizard, choose **"WhatsApp (QR link)"** — a QR code will appear in the terminal.

**On your phone:**

**Option A users:**
- Open WhatsApp Business
- Settings → Linked Devices → Link a Device
- Scan the QR code

**Option B users:**
- Open regular WhatsApp
- Settings → Linked Devices → Link a Device
- Scan the QR code

**Be careful:** Do not scan from the wrong app — the wrong account will get linked.

**After a successful scan, the terminal will show:**
```
Linked after restart; web session ready
```

Now whoever messages that WhatsApp number — OpenClaw will receive and reply.

---

## Step 17 — Wizard's Three Questions (After WhatsApp)

### Question 1 — Phone Setup:
- Option A users: choose **"Separate phone just for OpenClaw"**
- Option B users: choose **"This is my Personal Number"**

---

### Question 2 — DM Policy (Most Important Decision)

This decides who can talk to OpenClaw.

| Policy | How It Works | When to Use |
|--------|-------------|-------------|
| **Pairing** | Stranger DMs → OpenClaw sends them a code → You approve → Then they can chat | Learning, personal use |
| **Allowlist** | Only numbers you pre-added in the list can message. Everyone else ignored | Small team, known users |
| **Open** | Anyone can message — no approval needed | Public bots — risky |
| **Disabled** | All DMs completely blocked | Temporary lockdown |

**For now: Choose Pairing.**

In Pairing mode, your own number is automatically approved — so your first test works immediately without any approval step.

---

### Understanding the Pairing System in Detail:

When a new person messages for the first time:
1. OpenClaw sends them a **one-time code**
2. That code comes to you
3. You approve it
4. Only then can that user chat normally

**How to approve:**
```
openclaw pairing list whatsapp
openclaw pairing approve whatsapp <CODE>
```

**To reject someone:**
```
openclaw pairing reject whatsapp <CODE>
```

**To see pending approvals:**
```
openclaw pairing list
```

**Remember:**
- Codes expire after **1 hour**
- Maximum **3 pending requests** at a time per channel

---

### Question 3 — allowFrom:
Choose **"Unset allowFrom (default)"** — this is correct for Pairing mode.

allowFrom is the list that holds pre-approved numbers. Not needed in Pairing mode.

---

## Step 18 — Remaining Wizard Steps

| Wizard Step | What to Select | Why |
|-------------|---------------|-----|
| Web search provider | **DuckDuckGo (experimental)** | Free, no API key needed |
| Configure skills now? | **Yes** → then **Skip** all | Skills covered in Lesson 6 |
| Enable hooks? | **Skip** | Hooks covered in Lesson 14 |
| Optional apps | **Skip** | Not needed right now |
| How to hatch? | **Hatch in TUI** | Terminal chat will open |

---

## Step 19 — First Conversation in TUI

**TUI (Terminal User Interface)** will open and automatically send "Wake up, my friend!" to the bot.

The agent will respond and ask for your preferences.

**This conversation is very important — do not ignore it.**

Tell it:
- Your name
- What you do for work
- How you want the agent to behave

**This is not just an introduction** — it gets saved in the agent's **persistent memory**. This will be useful in later lessons.

Control UI is also available in the browser:
```
openclaw dashboard
```
Or directly: `http://127.0.0.1:18789/`

---

# PART 5 — Send Your First Message

## Step 20 — Test It

From your main WhatsApp, send this to the bound number:

```
Hello. What can you help me with?
```

- **WhatsApp:** Send from your personal WhatsApp to the bound number
- **Telegram:** Search the username you gave BotFather, DM it
- **Discord:** Click the bot's name → DM it — do not message in a channel

**A reply should come within 30 seconds.** 🎉

---

### A Friend Wants to Test It?

Your own number is auto-approved. But when a **different number** messages for the first time:
1. They will receive a code from OpenClaw
2. You need to approve:

```
openclaw pairing list whatsapp
openclaw pairing approve whatsapp <CODE>
```

After approval they can chat normally.

---

# PART 6 — Set Up Groups (For Different Topics)

You have one agent but different topic conversations should not mix — coding questions, personal schedule, research should all be separate. Groups solve this.

**Each group has:**
- Its own conversation history
- Its own context
- Commands in one group do not affect other groups
- Personal DM is completely separate

**Suggested groups:**

| Group Name | Purpose |
|------------|---------|
| AI Employee - Work | Daily tasks, email, scheduling |
| AI Employee - Code | Code reviews, technical questions |
| AI Employee - Learn | Research, summaries |

---

## Step 21 — Enable Group Messaging

Send this to the agent in a DM:

**For WhatsApp:**
```
Set my WhatsApp group policy to "open" in the config and restart
the gateway. Do not use allowlist mode. Confirm when done and tell
me the current group policy setting.
```

The agent will edit the config itself and restart.

**If you want to do it manually:**
```
openclaw config set channels.whatsapp.groupPolicy "open"
openclaw config set channels.telegram.groupPolicy "open"
openclaw config set channels.discord.groupPolicy "open"
openclaw gateway stop && openclaw gateway start
openclaw doctor
```

---

### ⚠️ Do Not Let It Switch to Allowlist

The agent sometimes tries to "improve" things by setting **allowlist mode** with exact group IDs — this breaks. Messages stop arriving but the agent keeps reporting success.

**If groups stop working:**
```
What is my current group policy?
```
If it shows allowlist:
```
Change it back to open and restart the gateway.
```

---

### @mention Is Required in Groups

In a group, the agent only replies when you **@mention** it. Otherwise it would reply to every single message — which would be very annoying.

---

### What Is Shared Across Groups, What Is Not:

| Thing | In all groups? |
|-------|---------------|
| SOUL.md, USER.md, IDENTITY.md (personality) | ✅ Yes — personality stays consistent |
| MEMORY.md (long-term memory) | ❌ Only in main private DM session |
| Conversation history | ❌ Completely separate per group |

---

# PART 7 — DM Policies Complete Guide

This controls who can talk to OpenClaw.

## Open Policy — Allow Everyone

Anyone who messages gets a reply. No restrictions.

```
openclaw config set channels.whatsapp.dmPolicy open
openclaw config set channels.whatsapp.allowFrom '["*"]'
```

When to use: Public bots, testing. There is risk — think before doing this.

---

## Allowlist Policy — Only Specific Numbers

Only numbers you have added to the list can message. Everyone else is ignored.

```
openclaw config set channels.whatsapp.dmPolicy allowlist
openclaw config set channels.whatsapp.allowFrom '["+923001234567","+923009876543"]'
```

Write your real numbers with country code (Pakistan is +92).

When to use: Small team, known users only.

---

## Disabled/Closed Policy — Everything Off

To temporarily turn everything off:

```
openclaw config set channels.whatsapp.dmPolicy closed
```

To turn it back on:
```
openclaw config set channels.whatsapp.dmPolicy open
```
Or set allowlist.

---

# PART 8 — When Things Go Wrong

**For any problem — first thing always:**

```
openclaw doctor
```

This automatically checks:
- Node.js version is correct or not
- Network connectivity
- Configuration paths are correct or not
- Service status

**Also verify gateway mode:**

```
openclaw config get gateway.mode
```

Should return `local`. If something else comes or there is an error — read the Crash Loop section below.

---

## Problem 1 — Gateway Crash Loop (Most Common Error)

### What you see in the log:
```
Gateway start blocked, gateway.mode not configured
Gateway start blocked, gateway.mode not configured
Gateway start blocked, gateway.mode not configured
```

### What happens:
The background service (systemd on Linux) gets registered before the config is complete. Gateway starts, crashes, system restarts it, crashes again — 18 restarts in 10 minutes.

### Why it happens:
This is a real bug in OpenClaw — the service registers before its dependency exists.

### Fix:
```
openclaw config set gateway.mode local
openclaw gateway restart
openclaw channels status --probe
```

### If normal commands also do not work:
```
launchctl unload ~/Library/LaunchAgents/ai.openclaw.gateway.plist
```
Then set gateway.mode and do a fresh start.

---

## Problem 2 — Auth Cache Issue

Set a new key but still getting auth errors?

Delete the cache:
```
rm ~/.openclaw/agents/main/agent/auth-profiles.json
```
Then:
```
openclaw configure --section model
```

---

## Problem 3 — No Reply (After 30 Seconds)

Check one by one:

```
openclaw channels status --probe
```
Is the channel connected?

```
openclaw doctor
```
Is the gateway healthy?

```
openclaw logs --follow
```
Watch the live log — every error shows up here.

**Log file location:**
```
/tmp/openclaw/openclaw-YYYY-MM-DD.log
```
A new file is created every day.

**The dashboard shows summaries. The log shows everything. Whenever anything breaks — open the log.**

---

## Problem 4 — command not found

```
npm install -g openclaw
```

---

## Problem 5 — gateway closed

```
openclaw gateway start
```

---

## Problem 6 — WhatsApp plugin not available

```
openclaw plugins install @openclaw/whatsapp
openclaw gateway restart
```

---

## Problem 7 — channel login failed

First reconfigure the channel section:
```
openclaw configure --section channels
```
If the problem persists:
```
openclaw channels login
```

---

## Problem 8 — 503 API Error

This is a temporary Google server error — not your fault. Wait a moment then:
```
openclaw gateway restart
```

---

## Problem 9 — WhatsApp Disconnected

```
openclaw channels login
```
Scan the QR code again.

---

# PART 9 — What to Do Every Time You Open Your Laptop

Every time you close and reopen your laptop — the gateway stops. Just do this:

**Step 1:** Open Ubuntu from Start menu (or type `wsl` in PowerShell)

**Step 2:**
```
openclaw gateway start
```

Done — OpenClaw is ready.

---

# PART 10 — One Important Pattern to Remember

### The Activation Dance

**Every** OpenClaw feature follows these same 4 steps:

1. Plugin exists → check: `openclaw plugins list`
2. **Disabled** by default — for security
3. Enable it: `openclaw config set plugins.entries.<id>.enabled true`
4. **Restart the gateway**

**Remember this pattern.** Lesson 6 skills, Lesson 10 voice, Lesson 14 custom plugins — all follow these same 4 steps. Until you know this pattern — every new feature seems "broken" on the first try.

---

# PART 11 — Exercises

### Exercise 1 — Give It a Real Task

Send on WhatsApp:
```
Write a short summary of what OpenClaw is and why someone 
would use it. Keep it under 100 words.
```

**What you learn:** The agent generates text and delivers it through WhatsApp.

---

### Exercise 2 — Test Its Memory

First send:
```
My name is [your name]. I work on [your project]. Remember this.
```

A few minutes later send:
```
What is my name and what do I work on?
```

**What you learn:** The agent remembers messages within a session. This is not a stateless chatbot — it knows who you are.

In Lesson 4 you will learn about **persistent memory** (MEMORY.md) that survives even after sessions end.

---

# PART 12 — Quick Reference (All Important Commands in One Place)

| Task | Command |
|------|---------|
| Every time you open laptop | `openclaw gateway start` |
| When something is not working | `openclaw gateway restart` |
| Health check | `openclaw doctor` |
| WhatsApp reconnect | `openclaw channels login` |
| Open dashboard in browser | `openclaw dashboard` |
| Watch live log | `openclaw logs --follow` |
| Reset channel settings | `openclaw configure --section channels` |
| Change model/key | `openclaw configure --section model` |
| Delete auth cache | `rm ~/.openclaw/agents/main/agent/auth-profiles.json` |
| See all commands | `openclaw --help` |
| Approve pairing request | `openclaw pairing approve whatsapp <CODE>` |
| See pending approvals | `openclaw pairing list` |

---

# Universal Setup Pattern — Same in Every Agent Framework

No matter which agent framework you use — OpenClaw or any other — these 5 steps are always the same:

1. **Install the runtime** — OpenClaw + Node.js
2. **Configure intelligence** — LLM provider + model (Google Gemini)
3. **Connect I/O** — messaging channel (WhatsApp/Telegram/Discord)
4. **Verify the connection** — end-to-end test
5. **Secure the deployment** — pairing mode, localhost binding
