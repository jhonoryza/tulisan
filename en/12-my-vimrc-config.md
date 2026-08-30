# My Vimrc Config

Minimal `~/.vimrc` I use daily (updated Aug 31, 2026 — now using `windsurf.vim`). Focus: comfortable coding, consistent indentation, and Windsurf/Codeium integration for AI autocomplete.

## Complete File

File location: `~/.vimrc`

```vim
call plug#begin()
" Add Codeium plugin
Plug 'Exafunction/windsurf.vim', { 'branch': 'main' }
call plug#end()

syntax on           " Enable syntax highlighting
set number          " Show line number
set relativenumber  " Show relative number
set tabstop=4       " Set tab width to 4 spaces
set shiftwidth=4    " Set indentation width to 4 spaces
set expandtab       " Use spaces instead of tabs
set cursorline      " Highlight the current line

imap <script><silent><nowait><expr> <C-g> codeium#Accept()
imap <script><silent><nowait><expr> <C-h> codeium#AcceptNextWord()
imap <script><silent><nowait><expr> <C-j> codeium#AcceptNextLine()
imap <C-;>   <Cmd>call codeium#CycleCompletions(1)<CR>
imap <C-,>   <Cmd>call codeium#CycleCompletions(-1)<CR>
imap <C-x>   <Cmd>call codeium#Clear()<CR>
" Trigger manual (biar tidak auto tiap ketik, uncomment jika mau manual only)
" let g:codeium_manual = v:true
imap <C-Space> <Cmd>call codeium#Complete()<CR>
imap <M-Bslash> <Cmd>call codeium#Complete()<CR>
```

---

## Basic Settings Explained

| Config | Function |
| :--- | :--- |
| `syntax on` | Enables syntax highlighting for better readability |
| `set number` | Shows absolute line numbers in the left gutter |
| `set relativenumber` | Shows relative line numbers (distance to cursor). The combination of `number` + `relativenumber` makes `5j` / `3k` navigation much faster |
| `set tabstop=4` | Visual Tab character width = 4 spaces |
| `set shiftwidth=4` | Indentation width on auto-indent (`>>`, `<<`, `=`) = 4 spaces |
| `set expandtab` | When pressing Tab, Vim inserts spaces instead of `\t`. Required for Git consistency and team collaboration |
| `set cursorline` | Highlights the line where the cursor is, making it easier to track position |

> The combination `tabstop=4` + `shiftwidth=4` + `expandtab` is standard for Python, Go, and most modern style guides.

---

## Codeium Keymaps Explained

I don't use Codeium's default `Tab` to avoid conflicts with other completions. I moved all Codeium keymaps to `Ctrl`:

| Keymap | Function Codeium |
| :--- | :--- |
| `Ctrl+g` `<C-g>` | `codeium#Accept()` — Accept **all** ghost text suggestions |
| `Ctrl+h` `<C-h>` | `codeium#AcceptNextWord()` — Accept **one word** from the suggestion |
| `Ctrl+j` `<C-j>` | `codeium#AcceptNextLine()` — Accept **one line** from the suggestion |
| `Ctrl+;` `<C-;>` | `codeium#CycleCompletions(1)` — Cycle to the **next** suggestion |
| `Ctrl+,` `<C-,>` | `codeium#CycleCompletions(-1)` — Cycle to the **previous** suggestion |
| `Ctrl+x` `<C-x>` | `codeium#Clear()` — Clear / cancel the current suggestion |
| `Ctrl+Space` `<C-Space>` | `codeium#Complete()` — Manual trigger (if `g:codeium_manual` is enabled) |
| `Alt+\` `<M-Bslash>` | `codeium#Complete()` — Fallback trigger manual |

The ` <script><silent><nowait><expr>` attributes on `imap` ensure the mapping runs without delay and without echo in the command line.

### Why Separate Accept Word / Line?

Sometimes Codeium suggestions are too long. With `AcceptNextWord` and `AcceptNextLine` we can accept only part of it instead of the whole block. More control than straight `Tab`.

---

## How to Use

1. Copy the config above to `~/.vimrc`
2. Restart Vim or reload with:

```vim
:source ~/.vimrc
```

3. Make sure Codeium is installed and authenticated (`:Codeium Auth`). See the full guide in [How to Install Codeium in Vim](./10-cara-install-codeium-di-vim.md).

---

## Additional Tips

* If `Ctrl+;` is not detected in certain terminals (e.g., macOS Terminal), switch to `Alt` or `Leader` as preferred.
* To completely disable Codeium's default Tab mapping, add above all `imap` entries:

```vim
let g:codeium_no_map_tab = 1
```
