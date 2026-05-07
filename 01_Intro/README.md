

## Before You Start — What Is All This?

### What is WSL?
WSL stands for **Windows Subsystem for Linux**. OpenClaw does not run directly on Windows. So we install Linux inside Windows. Think of it as a small Linux computer running inside your Windows laptop.

### What is Ubuntu?
Ubuntu is a version of Linux. Just like Windows has different versions (Windows 10, Windows 11), Linux also has different versions. Ubuntu is the most popular and easiest one. We will use this.

### What is Node.js?
This is the engine that runs OpenClaw. Just like a car needs an engine to run, OpenClaw needs Node.js. Without Node.js, OpenClaw will not install at all.

### What is OpenClaw?
An AI agent that works for you. It automatically replies on WhatsApp — you are asleep and someone messages, OpenClaw replies. It is not just a text-generating chatbot — it can also take actions on your behalf.

### What is the Gateway?
The gateway runs everything — it is the connection between WhatsApp and the AI. Until the gateway is running, OpenClaw will not work at all. Think of it as a bridge — WhatsApp on one side, AI brain on the other.

### What is npm?
npm is a package manager that comes automatically with Node.js. Its only job is to download and install software. Just like you install apps from the Play Store, software gets installed through npm.

### What is a Daemon?
A daemon is just a name — not an app, not a file, not something you install separately.

Any program that runs **silently in the background, without you opening it** is called a daemon. Your phone right now has dozens of them — battery monitor, Bluetooth service, notification listener. You never opened them. They are just there, doing their job quietly.

Think of a security guard at a building. You never called him, you never see him working, but he is always there — checking, watching, responding when something happens. A daemon is exactly that.

When OpenClaw registers a daemon with `--install-daemon`, it is simply saying: **"keep the gateway running in the background automatically, even when I am not looking at it."** Without it, you would have to manually start the gateway every single time you open your laptop.

### How Does OpenClaw Actually Work? (The Big Picture)

Before touching a single command, understand this — OpenClaw has four moving parts and they all work together:

Think of it like a Robot Assistant:

**Gateway = The Robot's Brain**
Every message first passes through the gateway. It decides what to do, where to send it, and what to reply. Nothing happens without the gateway. This is why we start it first every time.

**Plugins = The Robot's Hands, Eyes, and Ears**
By default the robot can only think. Plugins give it new abilities. Add a WhatsApp plugin — now it can send and receive messages. Add a browser plugin — now it can search the internet. Each plugin = one new capability.

**Sessions = Separate Memory Per Person**
When you talk to OpenClaw, it remembers your conversation. When your friend talks to it, their conversation is stored separately. The two never mix. This is a session — one isolated memory per user.

**Workspace Files = The Robot's Personality and Rules**
Files like `SOUL.md`, `USER.md`, and `IDENTITY.md` contain written instructions — "be helpful, reply formally, remember the user's preferences." These rules apply to every single message, always.

**The flow in one line:**
> Your message → Gateway (brain) → checks Workspace Files (rules) → uses a Plugin (ability) → remembers in your Session (memory) → replies

### OpenClaw = Think of It Like an Operating System

You already know what an OS does — Windows manages your whole computer. Apps do not talk to hardware directly. Everything goes through Windows. OpenClaw works the same way inside your AI setup.

| OpenClaw | OS Equivalent | What It Means |
|----------|--------------|---------------|
| Gateway | Kernel | The center — every message passes through here, nothing bypasses it |
| Workspace Files | Firmware | Permanent instructions — loaded on every single message, always |
| Plugins | Device Drivers | Each one adds a new capability — voice, tools, new channels |
| Sessions | Process Memory | Every user gets isolated memory — one never leaks into another |

Just like your OS runs silently and manages everything without you thinking about it — OpenClaw's gateway runs silently and manages every message, every tool, every reply.

**You do not need this analogy to be perfect. You just need it to help you think.** When something breaks, this mental model tells you where to look — gateway problem, plugin problem, session problem, or workspace file problem.

---

