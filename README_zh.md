# ywvim

[ywvim](http://www.vim.org/scripts/script.php?script_id=2662)（mY oWn VimIM，作者 Yue Wu）的 GitHub 镜像。原脚本没有托管在 GitHub 上，为了方便安装和个人使用，我把它搬到了这里。

[English README](./README.md)

## 免责声明

- **重要：** ywvim 使用了 Vim 的 `popup`（弹窗）特性，该特性**只有 Vim 支持**，Neovim 并未实现，因此本插件无法在 Neovim 中使用。
- 原脚本最后更新于 2020-11-02（v1.33）。我没有改动过它的任何逻辑——它只是恰好适合我的工作流。
- 如果你在使用中遇到问题，欢迎提 issue 或 pull request，但我可能不会去 review，因为我自己也没有完全看懂这套代码。建议你 fork 一份自己的副本，然后用 AI（是不是 slop 无所谓，随便）来帮你改 :)

## 介绍

ywvim 是一款不依赖任何外部输入法的、在 Vim 里输入中文的工具，支持在 Vim 的**所有模式**（插入、普通、命令行等）下输入中文。它主要面向形码用户（五笔、仓颉、郑码、行列等）；也支持拼音，但如果你是拼音用户并想要更多花哨功能，可以试试 [vimim](http://www.vim.org/scripts/script.php?script_id=2506)。坏消息：ywvim 和 vimim 的键绑定冲突，你必须在两者中做出选择。

本仓库打包了 ywvim 脚本（v1.33）和全部码表文件：

| 文件              | 输入法              |
|-------------------|---------------------|
| `wubi.ywvim`      | 五笔86              |
| `wubi98.ywvim`    | 五笔98              |
| `pinyin.ywvim`    | 拼音                |
| `cj3all.ywvim`    | 倉頡三代補完計畫    |
| `cj3sim.ywvim`    | 仓颉三代简体        |
| `cj5uni.ywvim`    | 倉頡五代            |
| `cj5ywu.ywvim`    | 倉頡五代（变体）    |
| `zhengma.ywvim`   | 郑码                |
| `zhuyin.ywvim`    | 注音                |
| `array30.ywvim`   | 行列30              |
| `g2b.ywvim`       | 简繁转换表          |
| `g2u.ywvim`       | GB→Unicode 表       |
| `gb2312.ywvim`    | GB2312 汉字表       |

## 环境要求

- Vim 7.0+（推荐 Vim 8.2+，可使用弹窗候选栏；**不支持** Neovim）。

## 安装

### vim-plug（推荐）

在 `.vimrc` 中加入：

```vim
Plug 'ChaosNyaruko/ywvim'
```

然后执行 `:PlugInstall`。

### Vim 8 原生 package

```sh
mkdir -p ~/.vim/pack/plugins/start
git clone https://github.com/ChaosNyaruko/ywvim.git ~/.vim/pack/plugins/start/ywvim
```

（Windows 下克隆到 `$VIM/vimfiles/pack/plugins/start/ywvim`。）

### 手动安装（原始脚本的经典装法）

把脚本和码表复制到 `plugin` 目录：

```sh
git clone https://github.com/ChaosNyaruko/ywvim.git /tmp/ywvim
cp /tmp/ywvim/plugin/ywvim.vim ~/.vim/plugin/
cp -r /tmp/ywvim/plugin/ywvim ~/.vim/plugin/
```

（Windows 下用 `$VIM/vimfiles/plugin` 代替 `~/.vim/plugin`。）

ywvim 会自动扫描 `ywvim.vim` 所在目录及其子目录查找码表文件，所以只要码表目录 `ywvim/` 和脚本放在一起，就能被找到。

## 配置

唯一必须设置的变量是 `g:ywvim_ims`，用来注册你的输入法。在插件加载**之前**，把下面几行放到你的 `.vimrc` 中：

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

- 每行格式为 `['输入法拼音简称', '输入法中文名称', '码表文件']`。第一行为默认输入法，不需要的输入法行可以删掉。
- ywvim 会扫描 `ywvim.vim` 所在目录及其子目录查找同名码表文件，所以仓库自带的码表开箱即用。

### 码表级别的单独设置

码表级别的参数优先于全局设置，格式为：

```vim
let g:ywvim_{输入法简称} = {'参数': 值}
```

支持局部设置的参数：`listmax`（候选框数目）、`maxphraselength`（最大词长）、`helpim`（反查码表简称）、`zhpunc`（中文标点开关）、`matchexact`（精确匹配开关）、`gb`（只输入 gb2312 范围汉字）。例如：

```vim
let g:ywvim_zy = {'listmax': 6, 'maxphraselength': 1, 'gb': 0}
```

### 全局选项（附默认值）

```vim
let g:ywvim_zhpunc = 1              " 默认中文标点输入开关
let g:ywvim_listmax = 5             " 候选项个数
let g:ywvim_esc_autoff = 0          " 1: 离开插入模式时自动关闭 ywvim
let g:ywvim_autoinput = 0           " 2: 重度自动上屏；1: 仅翻页时单码自动上屏
let g:ywvim_intelligent_punc = 1    " 数字后智能标点
let g:ywvim_intelligent_punclist = '.,:'
let g:ywvim_circlecandidates = 1    " 候选页循环翻页
let g:ywvim_helpim_on = 0           " 1: 打开反查码表功能（大码表会变慢）
let g:ywvim_matchexact = 0          " 1: 只显示全码匹配的字
let g:ywvim_chinesecode = 1         " 已键入字母用中文字母显示（如注音 ㄨㄛˇ）
let g:ywvim_gb = 0                  " 1: 只输入 gb2312 范围汉字
let g:ywvim_preconv = 'g2b'         " 默认简繁转换方向
let g:ywvim_conv = ''               " 'g2b': 简转繁；'b2g': 繁转简；'': 关闭
let g:ywvim_lockb = 1               " 0: 空码时不锁定键盘
let g:ywvim_theme = 'light'         " 输入栏主题：'light' 或 'dark'

" Vim 8.2+ 弹窗候选栏：
let g:ywvim_popupwin = 1
let g:ywvim_popupwin_follow_cursor = 1
let g:ywvim_popupwin_horizontal = 1
let g:ywvim_popupwin_force_cmdline = 0
```

## 使用

- `<Ctrl-Space>`、`<Ctrl-Shift-Space>`、`<Ctrl-@>`、`<Ctrl-\>` —— 输入法开关（所有模式下可用）。
- `;`（分号）—— 临时英文。
- `z`、`` ` `` —— 临时拼音（需要 `g:ywvim_ims` 里有一个简称为 `py` 的拼音码表）。
- `<Ctrl-^>` —— 设置菜单：码表切换、中英标点切换、候选项个数、最大词长、简繁转换开关、只输入 GB2312、辅助编码提示开关。
- `,` `.` `-` `=` —— 上下翻页。
- 空格或数字选字，回车输入英文原文，`;` 选择第二候选词。
- 注音用户注意：两次空格翻页，回车输入默认汉字；临时英文和临时拼音默认没有绑键（可自行配置）。

## 已知问题（来自原作者）

1. 反查码表对于大码表反应速度过慢。临时解决方案：换词汇量较小的码表，或者把 `listmax` 设小一点。
2. 命令行（cmdline）中无法正常关闭弹窗（所以默认在命令行禁用弹窗候选栏）。
3. 窗口布局变化时（如 `Ctrl-w o`）不能更新输入法状态提示。

## 致谢

一切功劳属于原作者 **Yue Wu**（ywupub AT 163 DOT com）。本仓库只是一个对插件管理器友好的 GitHub 镜像。许可证：BSD（见原脚本声明）。
