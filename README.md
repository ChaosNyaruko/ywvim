# ywvim

A fork of [ywvim](http://www.vim.org/scripts/script.php?script_id=2662) (mY oWn VimIM) by Yue Wu. The original script is not on GitHub, so I ported it here for easier installation and personal use.

[中文版 README](./README_zh.md)

## DISCLAIMER

- **IMPORTANT:** ywvim uses Vim's `popup` features, which are **only supported in Vim**. Neovim does not implement them, so this plugin will not work in Neovim.
- The original script was last updated on 2020-11-02 (v1.33). I have not modified its logic — it simply happens to fit my workflow.
- If you run into problems, feel free to open an issue or a pull request, but I may not review it since I don't fully understand the codebase. You are probably better off forking your own copy and letting AI (slop or not, whatever) help you with it :)

## Introduction

ywvim is an input method (IM) for Vim that works without any external IM tools — it lets you type Chinese in **all modes** of Vim (insert, normal, command-line, etc.). It is mainly targeted at shape-based (形码) IM users such as Wubi, Cangjie, Zhengma and Array30. Pinyin is supported too, but if you are a pinyin user looking for more fancy features, try [vimim](http://www.vim.org/scripts/script.php?script_id=2506) instead. Note that ywvim's key mappings conflict with vimim — you have to choose one of them.

This repo bundles the ywvim script (v1.33) together with all the code table (码表) files:

| File              | Input method                    |
|-------------------|---------------------------------|
| `wubi.ywvim`      | 五笔86 (Wubi 86)                |
| `wubi98.ywvim`    | 五笔98 (Wubi 98)                |
| `pinyin.ywvim`    | 拼音 (Pinyin)                   |
| `cj3all.ywvim`    | 倉頡三代補完計畫 (Cangjie 3+)   |
| `cj3sim.ywvim`    | 仓颉三代简体 (Cangjie 3 simp.)  |
| `cj5uni.ywvim`    | 倉頡五代 (Cangjie 5)            |
| `cj5ywu.ywvim`    | 倉頡五代 (Cangjie 5 variant)    |
| `zhengma.ywvim`   | 郑码 (Zhengma)                  |
| `zhuyin.ywvim`    | 注音 (Zhuyin)                   |
| `array30.ywvim`   | 行列30 (Array30)                |
| `g2b.ywvim`       | Simplified→Traditional table    |
| `g2u.ywvim`       | GB→Unicode table                |
| `gb2312.ywvim`    | GB2312 character list           |

## Requirements

- Vim 7.0+ (Vim 8.2+ recommended for the popup-window candidate list; Neovim is **not** supported).

## Installation

### vim-plug (recommended)

Add to your `.vimrc`:

```vim
Plug 'ChaosNyaruko/ywvim'
```

Then run `:PlugInstall`.

### Native Vim 8 packages

```sh
mkdir -p ~/.vim/pack/plugins/start
git clone https://github.com/ChaosNyaruko/ywvim.git ~/.vim/pack/plugins/start/ywvim
```

(On Windows, clone into `$VIM/vimfiles/pack/plugins/start/ywvim`.)

### Manual (classic layout, as the original script intended)

Copy the script and the code tables into your `plugin` directory:

```sh
git clone https://github.com/ChaosNyaruko/ywvim.git /tmp/ywvim
cp /tmp/ywvim/plugin/ywvim.vim ~/.vim/plugin/
cp -r /tmp/ywvim/plugin/ywvim ~/.vim/plugin/
```

(On Windows, use `$VIM/vimfiles/plugin` instead of `~/.vim/plugin`.)

ywvim automatically scans the directory containing `ywvim.vim` (and its subdirectories) for code table files, so as long as the `ywvim/` directory of tables sits next to the script, everything is found.

## Configuration

The only mandatory setting is `g:ywvim_ims`, which registers your input methods. Put something like the following into your `.vimrc` **before** the plugin is loaded:

```vim
let g:ywvim_ims = [
            \['wb',   '五笔',   'wubi.ywvim'],
            \['py',   '拼音',   'pinyin.ywvim'],
            \['cj',   '仓颉',   'cj5uni.ywvim'],
            \['wb98', '五笔98', 'wubi98.ywvim'],
            \['zm',   '郑码',   'zhengma.ywvim'],
            \['zy',   '注音',   'zhuyin.ywvim'],
            \['ar30', '行列',   'array30.ywvim'],
            \]

let g:ywvim_py = { 'helpim':'wb', 'gb':0 }
```

- Each entry is `['English abbreviation', 'Chinese name', 'code table file']`. The first entry is the default IM. Delete the lines of IMs you don't need.
- ywvim searches the directory of `ywvim.vim` and its subdirectories for the named table file, so the bundled tables work out of the box.

### Per-IM options

Per-IM settings take precedence over global ones. Format:

```vim
let g:ywvim_{abbreviation} = {'option': value}
```

Options supported per IM: `listmax`, `maxphraselength`, `helpim`, `zhpunc`, `matchexact`, `gb`. Example:

```vim
let g:ywvim_zy = {'listmax': 6, 'maxphraselength': 1, 'gb': 0}
```

### Global options (with defaults)

```vim
let g:ywvim_zhpunc = 1              " input Chinese punctuation by default
let g:ywvim_listmax = 5             " number of candidates shown
let g:ywvim_esc_autoff = 0          " 1: turn ywvim off when leaving insert mode
let g:ywvim_autoinput = 0           " 2: auto-commit any single candidate; 1: only on paging
let g:ywvim_intelligent_punc = 1    " smart punctuation after numbers
let g:ywvim_intelligent_punclist = '.,:'
let g:ywvim_circlecandidates = 1    " wrap around when paging candidates
let g:ywvim_helpim_on = 0           " 1: enable reverse-lookup (help) table (slow for big tables)
let g:ywvim_matchexact = 0          " 1: only show exact (full-code) matches
let g:ywvim_chinesecode = 1         " show Chinese alphabets (e.g. ㄨㄛˇ) instead of typed letters
let g:ywvim_gb = 0                  " 1: only input characters in the gb2312 range
let g:ywvim_preconv = 'g2b'         " preferred s2t/t2s conversion direction
let g:ywvim_conv = ''               " 'g2b': simp->trad, 'b2g': trad->simp, '': off
let g:ywvim_lockb = 1               " 0: don't lock the keyboard on empty (no-match) codes
let g:ywvim_theme = 'light'         " 'light' or 'dark'

" Vim 8.2+ popup-window candidate list:
let g:ywvim_popupwin = 1
let g:ywvim_popupwin_follow_cursor = 1
let g:ywvim_popupwin_horizontal = 1
let g:ywvim_popupwin_force_cmdline = 0
```

## Usage

- `<Ctrl-Space>`, `<Ctrl-Shift-Space>`, `<Ctrl-@>` or `<Ctrl-\>` — toggle ywvim on/off (works in all modes).
- `;` — temporary English input.
- `z` or `` ` `` — temporary pinyin input (requires a table with the abbreviation `py` in `g:ywvim_ims`).
- `<Ctrl-^>` — ywvim setup menu: switch IM, toggle Chinese/English punctuation, set candidate count, set max phrase length, toggle s2t/t2s conversion, toggle gb2312-only input, toggle the help-IM hint.
- `,` `.` `-` `=` — page up/down through candidates.
- `Space` or a number key — select a candidate; `Enter` — input the typed code as English literally; `;` — select the second candidate.
- Note for zhuyin users: double-`Space` pages down, `Enter` inputs the default character, and the temporary-English / temporary-pinyin keys are unbound by default (configurable).

## Known bugs (from the original author)

1. Reverse lookup (help mabiao) is slow on large tables. Workaround: use a smaller table or lower `listmax`.
2. The popup window cannot be closed properly in the command line (which is why popupwin is disabled there by default).
3. The IM status indicator does not update when the window layout changes (e.g. `Ctrl-w o`).

## Credits

All credit goes to the original author, **Yue Wu** (ywupub AT 163 DOT com). This repo is just a GitHub mirror with a plugin-manager-friendly layout. License: BSD (as stated in the original script).
