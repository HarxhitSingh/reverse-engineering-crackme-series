# Reverse Engineering Practice: Crackme Series

My first steps in reverse engineering, written up as I learned. I had no prior experience, so this repo records the setup, the three practice challenges I solved, and the mistakes I made along the way.

> **Note:** All three programs are practice crackmes made for learning (built with AI assistance), not anyone's commercial software. Passwords and solution data are intentionally left out of this repo so you can try the challenges yourself.

## What's in here

| Level | Technique used to hide the answer | What I practiced |
|---|---|---|
| 1 | Plain string | Reading strings, then confirming the logic instead of trusting a guess |
| 2 | Single-byte XOR | Finding the data and key in decompiled code, decoding by hand |
| 3 | Repeating multi-byte XOR, stripped function names | Identifying functions by behavior, verifying an AI's answer |

## Toolchain

The chain is: **me → Copilot → REA → Ghidra → the .exe**

- **[Ghidra](https://github.com/NationalSecurityAgency/ghidra)** (12.1.x): decompiles machine code into readable C-like code.
- **Java JDK 21** (Temurin): required to run Ghidra.
- **[REA](https://github.com/morluto/rea)**: lets an AI agent drive Ghidra (open a file, list functions, decompile, search strings).
- **VS Code + GitHub Copilot Free** (Agent mode): the chat front end.
- **Node.js** (LTS): REA runs on it.
- **Python**: small scripts for decoding results.

Environment: Windows x64.

## Setup notes

Short version of what worked for me:

1. Install Node.js (official LTS installer) and VS Code.
2. Enable GitHub Copilot Free in VS Code.
3. Register REA with VS Code (via `npx rea-agents setup`, or manually in the user `mcp.json`).
4. Install JDK 21 and Ghidra, then set `GHIDRA_INSTALL_DIR` and `JAVA_HOME`.
5. Fully restart VS Code, restart the `rea` server, and open a **new** Agent-mode chat.
6. Verify by asking Copilot to call `binary_session` and report the Ghidra provider status.

### Problems I hit

- **PowerShell blocked `npm`.** Fixed with `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned`.
- **The setup menu showed nothing selected.** Items aren't checked until you press Space.
- **`doctor` failed** because I left a placeholder in the command. The VS Code client name is `vscode`.
- **Copilot said no REA tools were available.** The server needed to be started, and an old chat doesn't pick it up.
- **REA couldn't see Ghidra.** The `doctor` command reads the shell environment, not the `env` block in `mcp.json`. The live server needed the variables, then a full restart.

## The three levels

### Level 1: plain string
The password was visible in a strings search. A string like that is a clue, not proof, so I followed the logic: `main` reads input, calls a check function, which compares it against a fixed string.

**Lesson:** confirm a candidate by reading the code before trusting it.

### Level 2: single-byte XOR
No password in the strings. A decode function loops over an embedded byte array and XORs each byte with a constant. I found the array and the key in the decompiled code and decoded it by hand, one hex digit at a time.

**Lesson:** when strings show nothing, read the code that builds the secret.

### Level 3: repeating key, no function names
Function names were stripped, so Ghidra showed names like `FUN_...`. I started from the success message and worked backwards: the function that decides between the two messages is the check, and whatever it calls to get the expected answer is the decoder. The key repeated every four bytes.

**Lesson:** identify functions by what they do and which strings they touch.

**The AI mistake:** Copilot's decoded answer came out one character short. The loop ran 15 times, but the AI's answer had 14 characters. I caught it by comparing the length to the loop count, then decoded it myself with a short Python script.

## Key lessons

1. Strings are clues, not proof.
2. Find the decision point: the comparison and the branch after it.
3. Real software is usually stripped, so name functions yourself from their behavior.
4. AI tools can summarize their own output incorrectly. Verify the final step yourself.
5. Keep chats short and specific to stretch a free quota, and start a new chat for each phase.

## Safety and ethics

- Only analyze software you own or are authorized to examine.
- Don't run unknown programs casually. Windows SmartScreen warns about unsigned downloads for a reason.
- No cracking of commercial licenses, and no publishing of proprietary code.
- If you try these challenges, keep your solutions out of public view so others can enjoy them too.

## Repo structure

```
.
├── README.md
├── writeups/
│   ├── level1.md
│   ├── level2.md
│   └── level3.md
└── screenshots/
```

## What's next

- Build my own Windows Calculator-style clone (standard mode, then scientific mode and history), using the open-source Windows Calculator repo as a reference and my own branding.
- Add more write-ups as I learn.

## About

I'm a beginner learning in public. Feedback and corrections are very welcome.

- **Author:** [Your name]
- **LinkedIn:** [Your LinkedIn link]
