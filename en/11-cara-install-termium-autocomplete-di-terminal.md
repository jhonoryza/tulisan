# How to Install Termium (AI Autocomplete in the Terminal)

Termium is a terminal autocomplete tool from Codeium. Unlike editor autocomplete, Termium works directly in the shell (bash/zsh) and suggests commands based on your history and terminal output. Currently Termium is still in **alpha/prototype** stage and under active development.

Available for **macOS** and **Linux**.

---

## 1. Installation & Setup

Installation requires just one command in the terminal.

### Step 1: Run the Installer Script

Open a terminal and run the following command:

```bash
curl -L https://github.com/Exafunction/codeium/releases/download/termium-v0.2.1/install.sh | bash
```

> **Note:** Older guides still use `termium-v0.2.0`. It's recommended to use the latest `v0.2.1` as it fixes several installation bugs.

### Step 2: Authentication

Once installed, connect Termium to your Codeium account:

```bash
termium auth
```

Follow the terminal instructions to log in and enter your API token.

### Step 3: Install the Shell Hook

To integrate Termium with your terminal session, run:

```bash
termium shell-hook install
```

This command will automatically add configuration to your shell file (`.bashrc` or `.zshrc`).

### Step 4: Restart the Terminal

Close and reopen your terminal for the configuration changes to take effect.

Done. Termium is ready to use.

---

## 2. How It Works

Termium runs as a layer between you and the terminal. It reads history and previous command output to provide relevant suggestions.

| Action | How |
| :--- | :--- |
| **Ghost Text** | While typing, gray ghost text appears as a full command suggestion |
| **Accept Suggestion** | Press `Tab` to accept and complete the command |
| **Dismiss Suggestion** | Keep typing or press `Esc` to dismiss |

Real-world usage examples:

* After `git status`, Termium can suggest files to `git add`
* When typing `kubectl logs`, it can auto-complete the pod name
* Very helpful for repetitive / boilerplate command patterns

---

## 3. Pro Tip: Finding New Commands with Natural Language

If you can't remember the command you need, you can leverage Termium with the `echo` trick:

```bash
echo "show the logs for my kubernetes pod"
```

After typing that natural language description, Termium will read the context and suggest the correct command, e.g.:

```bash
kubectl logs [pod-name]
```

Great for exploring new commands without Googling.

---

## 4. How Menonaktifkan / Uninstall

To disable Termium, just remove the lines Termium added to your shell config file:

* `~/.bashrc` for Bash
* `~/.zshrc` for Zsh

Remove the block containing `termium` or `shell-hook`, then restart the terminal.

---

## Download & Releases

The latest Termium binary and Language Server can be downloaded from the official Codeium releases page:

* **Download Page:** https://github.com/Exafunction/codeium/releases?page=1#release-language-server-v2.12.5

---

## Conclusion

1. Install: `curl ... install.sh | bash`
2. Auth: `termium auth`
3. Hook: `termium shell-hook install`
4. Restart terminal dan gunakan `Tab` untuk menerima saran

With Termium, terminal work becomes faster, especially for frequently repeated commands like `git`, `docker`, and `kubectl`.
