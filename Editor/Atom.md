## Atom

GitHub推出的一款编辑器,现代, 易用, 可定制,The hackable text editor,只关注最重要的定制，不要在无关紧要的细节上浪费生命。

## 安装

```sh
# mac
brew cask install atom
# Window
choco install atom

# 命令行启动
atom
```

## 插件安装

- 命令面板：Ctrl + Shift + P
- 设置面板:Edit->Preferences 命令面板 Settings View:Open ctrl + ,

## 初始化配置

```
⇧⌘P 输入init script
# move cursor across the ending symbols...
EndingSymbolRegex = /\s*[)}>\]/'";:=-]/
atom.commands.add 'atom-text-editor', 'custom:jump-over-symbol': (event) ->
  editor = atom.workspace.getActiveTextEditor()
  cursorMoved = false
  for cursor in editor.getCursors()
    range = cursor.getCurrentWordBufferRange(wordRegex: EndingSymbolRegex)
    unless range.isEmpty()
      cursor.setBufferPosition(range.end)
      cursorMoved = true
  event.abortKeyBinding() unless cursorMoved

⇧⌘P 输入keymap
"atom-text-editor:not([mini])":
  "enter": "custom:jump-over-symbol"

# for markdown-writer
".platform-darwin atom-text-editor:not([mini])":
  "shift-cmd-K": "markdown-writer:insert-link"
  "shift-cmd-I": "markdown-writer:insert-image"
  "cmd-i":       "markdown-writer:toggle-italic-text"
  "cmd-b":       "markdown-writer:toggle-bold-text"
  "cmd-'":       "markdown-writer:toggle-code-text"
  "cmd-k":       "markdown-writer:toggle-keystroke-text"
  "cmd-h":       "markdown-writer:toggle-strikethrough-text"
  'cmd->':       "markdown-writer:toggle-blockquote"
  'cmd-"':       "markdown-writer:toggle-codeblock-text"
  "ctrl-alt-1":  "markdown-writer:toggle-h1"
  "ctrl-alt-2":  "markdown-writer:toggle-h2"
  "ctrl-alt-3":  "markdown-writer:toggle-h3"
  "ctrl-alt-4":  "markdown-writer:toggle-h4"
  "ctrl-alt-5":  "markdown-writer:toggle-h5"
  "shift-cmd-O": "markdown-writer:toggle-ol"
  "shift-cmd-U": "markdown-writer:toggle-ul"
  "cmd-j cmd-p": "markdown-writer:jump-to-previous-heading"
  "cmd-j cmd-n": "markdown-writer:jump-to-next-heading"
  "cmd-j cmd-d": "markdown-writer:jump-to-reference-definition"
  "cmd-j cmd-t": "markdown-writer:jump-to-next-table-cell"

⇧⌘P 输入reload:重新加载配置
```

## snippets

- \n 表示的是纯文本中默认并不显示的"换行符号"；
- \t 表示的是 tab（不是 tab 键，而是文本中显示的"缩进" ---- 通常相当于两个空格，或者四个空格）；
- $1 是展开后光标所在的位置；
- $2 是再次按 ⇥ 键的时候，光标应该所在的下一个位置......
- scope：⇧⌘P 输入Log Cursor Scope

## 自定义

```
# 尽量不要直接使用别人写好的 Snippets，要自己写、自己敲；而别人写好的，尽量只用来参考。
⇧⌘P 输入Open Your Snippets,添加自定义代码片段
prefix 不要用缩写
```

## 插件

### apm

`apm` is Atom's package manager, based on Node's `npm` tool.

Command                    | Description
-------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------
`apm upgrade`              | Updates all locally installed packages
`apm upgrade --no-confirm` | Updates all locally installed packages without asking any questions
`apm stars --install`      | Installs/updates all packages that you have marked as a favorite (_starred_) in your Atom.io profile
`apm publish minor`        | If you're developing your own package, run this in the package's directory to publish a new version of the package, increasing the minor version number by one.
`apm search emmet`         |
`apm view git-grep`        |
`apm install emmet@0.1.5`  |
`apm remove emmet`         |

- activate-power-mode-master
- advanced-open-file@0.16.6
- angularjs@0.4.0
- atom-autocomplete-php@0.25.6
- atom-beautify@0.30.5 代码格式化，可以在setting中不同语言设置 保存格式化后置操作Beautify On Save
- atom-script-master
- atom-terminal@0.8.0
- atom-ternjs@0.18.3
- atom-typescript@11.0.6
- autocomplete-paths@2.8.0
- autocomplete-python
- busy-signal@1.4.3
- color-picker@2.2.5
- editorconfig@2.2.2
- emmet@2.4.3
- file-icons@2.1.11theme
- git-plus@7.9.3
- imdone-atom@2.2.6
- platformio-ide-terminal:^ + `调出命令行
- intentions@1.1.5
- javascript-snippets@1.2.1
- language-babel@2.71.0
- language-docker@1.1.8
- language-javascript-jsx@0.3.7
- language-latex-master + atom-latex-master + atom-pdf-view-master
- language-markdown@0.25.1
- language-vue@0.23.1
- linter@2.2.0
- linter-php@1.3.2
- linter-ui-default@1.6.3
- markdown-preview-plus@2.4.10 支持关键字table code
- markdown-writer@2.6.5(<http://blog.csdn.net/u010494080/article/details/53562939>)
- merge-conflicts@1.4.5
- nuclide@0.244.0 是Facebook开发的开发React Native的开发工具，基于Github的Atom开发，以Atom插件的形式存在。
- pigments@0.39.1
- project-manager@3.3.5
- react-snippets@0.9.0
- atom-pair ：结对编程共同编辑代码：atompair new（获取session） atompair add（加入），局域网中可以实现
- sync-settings@0.8.2(<https://atom.io/packages/sync-settipngs)配置> 使用command + shift + P backup 或者restore插件
- activate-power-mode：打字爆炸模式
- atom-ide-ui
- Teletype
- terminal-plus
- [facebook/nuclide](https://github.com/facebook/nuclide):An open IDE for web and native mobile development, built on top of Atom https://nuclide.io

## 快捷键 Atom Keyboard Shortcuts

- 文件切换
  - ctrl + shift + o 打开目录
  - ctrl + t或ctrl + p: 查找文件
  - ctrl + b enter或者ctrl + tab:在打开的文件之间切换
  - ctrl + shift + b:搜索一个新建的或更改过的文件(git commit后修改或者新增的文件)
  - 进入文件 atom . 用atom加载整个目录
  - open . 打开文件目录
  - Toggle command palette:Ctrl + Shift + P /shift + ⌘ + p
  - Toggle line/selection comment: Show available auto-completions
  - 设置面板:⌘ + ,
- 导航
  - Ctrl+G: 呼出光标移动窗口,填入行:列
  - ctrl + r : 在当前文档Symbols(代码中的函数名,变量名)之间跳转
  - ctrl + shift + r:: 在整个项目Symbols(代码中的函数名,变量名)之间跳转
  - alt-left 移动到单词开始
  - alt-right 移动到单词末尾
  - ctrl + right 移动到一行结束
  - ctrl + left移动到一行开始
  - ctrl + up 行上移
  - ctrl + down 行下移
- 目录树操作
  - ctrl + \ 或者 ctrl + k ctrl + b 显示(隐藏)目录树并切换焦点
    - 目录树下，通过方向键查看切换,使用a，m，delete来增加，修改和删除
  - d 复制文件(duplicate)
  - i 显示(隐藏)版本控制忽略的文件
  - alt-right 和 alt-left 展开(隐藏)所有目录
  - ctrl + al-] 和 ctrl + al-[ 同上
  - ctrl + [ 和 ctrl + ] 展开(隐藏)当前目录
- 书签
  - ctrl + F2 在本行增加书签
  - F2 跳到当前文件的下一条书签
  - shift-F2 跳到当前文件的上一条书签
  - ctrl + F2 列出当前工程所有书签
- GitHub Alt+G O 在GitHub上打开当前文件
- Alt+G B 在GitHub上用Blame方式打开当前文件
- Alt+G H 在GitHub上用History方式打开当前文件
- Alt+G C 将当前文件在GitHub上的URL复制到剪切板
- Alt+G R 在GitHub上比较分支

This page lists keyboard shortcuts for the [Atom text editor](https://atom.io) that I find valuable and use a lot. Feel free to fork the page and add your own favorites. Pull Requests welcome!

This list is by no means meant to be a complete listing of every available shortcut. It simply lists the shortcuts that I use on a regular basis. For a complete listing of all available shortcuts, consult the _Settings > Keybindings_ page in Atom.

Since I'm using a Mac, I have mainly listed the keyboard shortcuts macOS. Please feel free to add the Windows or Linux shortcuts.

Where the shortcut is provided by a package, I have added a link to the package.

## General

Some general keyboard shortcuts that I use frequently.

Command                 | macOS                   | Windows                     | Linux                       | Description
----------------------- | ----------------------- | --------------------------- | --------------------------- | ---------------------------------------------------------------------
Preferences/Settings    | `cmd-,`                 | `ctrl-,`                    | `ctrl-,`                    | Opens the Preferences/Settings view
Command Palette         | `shift-cmd-p`           | `shift-ctrl-p`              | `ctrl-shift-p`              | Opens & closes the command palette
Open File (Fuzzy)       | `cmd-p`                 | `ctrl-p` or `ctrl-t`        | `ctrl-p`                    | Opens the Fuzzy Finder palette in which you can search and open files
Browse Open Files       | `cmd-b`                 | `ctrl-b`                    | `ctrl-b`                    | Browse tabs within the window
Grammar Selector        | `ctrl-shift-l`          | `ctrl-shift-l`              | `ctrl-shift-l`              | Selects the language the file is in
Markdown Preview        | `ctrl-shift-m`          | `ctrl-shift-m`              | `ctrl-shift-m`              | Previews the file in the Markdown format
Key Binding Resolver    | `cmd-.`                 | `ctrl-.`                    | `ctrl-.`                    | Shows what keybindings the pressed key combination resolves to
Toggle Tree View        | `cmd-k cmd-b`or `cmd-\` | `ctrl-k ctrl-b` or `ctrl-\` | `ctrl-k ctrl-b` or `ctrl-\` | Toggles Atom's file Tree
View Reload Atom        | `ctrl-alt-cmd-l`        | `alt-ctrl-r`                | `alt-ctrl-r`                | Reloads the Editor
Open Link               | `ctrl-shift-o`          |                             |                             | Opens up a HTTP or HTTPS link
Toggle Developer Tools  | `alt-cmd-i`             | `ctrl-alt-i`                | `ctrl-shift-i`              | Opens up the Chrome Developer Tools/Console
Show Available Snippets | `alt-shift-s`           | `alt-shift-s`               | `alt-shift-s`               | Shows the snippets available to Atom

## Window Management

Command      | macOS                          | Windows                          | Linux                            | Description
------------ | ------------------------------ | -------------------------------- | -------------------------------- | -----------------------------------------------------------------------------------------------
New File     | `cmd-n`                        | `ctrl-n`                         | `ctrl-n`                         | Opens an empty file in a new tab
New Window   | `shift-cmd-n`                  | `ctrl-shift-n`                   | `ctrl-shift-n`                   | Opens a new editor window
Open         | `cmd-o`                        | `ctrl-o`                         | `ctrl-o`                         | Shows the _Open File_ dialog, which lets you select a file to open in the editor
Open Folder  | `cmd-shift-o`                  | `ctrl-shift-o`                   | `ctrl-shift-o`                   | Shows the _Open Folder_ dialog, which lets you select a folder to add to the editor's Tree View
Save         | `cmd-s`                        | `ctrl-s`                         | `ctrl-s`                         | Saves the currently active file
Save As      | `shift-cmd-s`                  | `ctrl-shift-s`                   | `ctrl-shift-s`                   | Saves the currently active file under a different name
Save All     | `alt-cmd-s`                    |                                  |                                  | Saves all changed files
Close Tab    | `cmd-w`                        | `ctrl-w`                         | `ctrl-w`                         | Closes the currently active tab
Close Window | `shift-cmd-w`                  | `ctrl-shift-w`                   | `ctrl-shift-w`                   | Closes the currently active editor window
Split Window | `cmd-k up/down/left/right`     | `ctrl-k up/down/left/right`      | `ctrl-k up/down/left/right`      | Split the currently active tab in one of the four directions
Focus Pane   | `cmd-k cmd-up/down/left/right` | `ctrl-k ctrl-up/down/left/right` | `ctrl-k ctrl-up/down/left/right` | Move the focus to the pane in one of the four directions

## Editing

Command                                    | macOS                         | Windows            | Linux               | Description
------------------------------------------ | ----------------------------- | ------------------ | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Duplicate Lines                            | `shift-cmd-d`                 | `ctrl-shift-d`     | `ctrl-shift-d`      | Duplicates the line of the current cursor position and creates a new line under it with the same contents
Delete Line                                | `ctrl-shift-k`                | `ctrl-shift-k`     | `ctrl-shift-k`      | Deletes the current line
Move Line Up                               | `ctrl-cmd-up`                 | `ctrl-up`          | `ctrl-up`           | Moves the contents of the current cursor position up one line. If there is a line above with content, the current lines content will swap with the one above it.
Move Line Down                             | `ctrl-cmd-down`               | `ctrl-down`        | `ctrl-down`         | Moves the contents of the current cursor position down one line. If there is a line below with content, the line's content will swap with the one below it.
Find/Replace                               | `cmd-f`                       | `ctrl-f`           | `ctrl-f`            | Opens up the Find/Replace panel
Find Next                                  | `cmd-g`                       | `F3`               | `F3`                | Toggles forward through the results of the current buffer in the file while the Find/Replace panel is active
Find Previous                              | `shift-cmd-g`                 | `shift-F3`         | `shift-F3`          | Toggles backward through the results of the current buffer in the file while the Find/Replace panel is active
Find in Project                            | `shift-cmd-f`                 | `ctrl-shift-f`     | `ctrl-shift-f`      | Opens the Find in Project Panel
Go To Line                                 | `ctrl-g`                      | `ctrl-g`           | `ctrl-g`            | Opens the Go To Line panel
Go To Matching Bracket                     | `ctrl-m`                      | `ctrl-m`           | `ctrl-m`            | The cursor goes to the matching top bracket that the cursor is ecapsulated in
Select Line                                | `cmd-l`                       | `ctrl-l`           | `ctrl-l`            | Selects the entire line the cursor's current position is in
Toggle Comment                             | `cmd-/`                       | `ctrl-/`           | `ctrl-/`            | Toggles the selected text into a comment of the current grammar
Column Selection                           | `ctrl-shift-up/down`          | `ctrl-alt-up/down` | `shift-alt-up/down` | Allows to select multiple rows, where the same edit will be applied
Select Same Words                          | `cmd-d`                       | `ctrl-d`           | `ctrl-d`            | If you select a word, and then hit the key combo for this command, Atom will select the next same word for you. Then you can either type directly (which will replace the old words) or use left or right arrow to append things.
Undo Selection                             | `cmd-u`                       | `ctrl-u`           | `ctrl-u`            | This undoes the previous selection, like from Select Same Words.
Select All The Same Words At Once          | `cmd-ctrl-g`                  | `alt-f3`           | `alt-f3`            | This shortcut is similar to `cmd-d/ctrl-d` but it selects all the matching words at once.
Select to top/bottom of document           | `shift + ⌘ + up/down`         |                    |                     |
Select to first/last character of line     | `shift + ⌘ + left/right`      |                    |                     |
Select to beginning/end of word            | `option + shift + left/right` |                    |                     |
Transpose characters either side of cursor | `ctrl + t`                    |                    |                     |
Delete text to beginning of word           | `option + backspace`          |                    |                     |
Delete text to end of word                 | `option + delete`             |                    |                     |
Indent/outdent current line                | `⌘ +]/[`                      |                    |                     |
Insert new line after current line         | `⌘ + enter`                   |                    |                     |
Insert new line before current line        | `⌘ + shift + enter`           |                    |                     |
Delete current line                        | `ctrl + shift + k`            |                    |                     |
Move current line up/down                  | `ctrl + ⌘ + up/down`          |                    |                     |
Duplicate current line                     | `shift + ⌘ + d`               |                    |                     |
Join current and next lines                | `⌘ +j`                        |                    |                     |

## Various Packages

These are some packages I find useful, and their most useful key bindings. A list of my favorite packages can be found [here](https://atom.io/users/nwinkler/stars).

Command                        | macOS                | Windows        | Linux              | Package
------------------------------ | -------------------- | -------------- | ------------------ | -------------------------------------------------------------------------------------------------------------------------------
Block Travel up/down           | `alt-up`, `alt-down` |                |                    | [Block Travel](https://atom.io/packages/block-travel)
Beautify                       | `ctrl-alt-b`         |                |                    | [Beautify](https://atom.io/packages/atom-beautify)
Expand Abbreviation            | `shift-cmd-e`        | `ctrl-e`       | `ctrl-e`           | [Emmet](https://atom.io/packages/emmet)
Incremental Search             | `cmd-i`              |                |                    | [Incremental Search](https://atom.io/packages/incremental-search)
Git Plus Menu                  | `shift-cmd-h`        | `ctrl-shift-h` | `ctrl-shift-h`     | [Git Plus](https://atom.io/packages/git-plus)
Jumpy                          | `shift-enter`        |                |                    | [Jumpy](https://atom.io/packages/jumpy)
Minimap Toggle                 | `ctrl-k ctrl-m`      |                |                    | [Minimap](https://atom.io/packages/minimap)
Open File in Browser           | `ctrl-alt-m`         |                |                    | [Open in Browser](https://atom.io/packages/open-in-browser)
Run Script                     | `ctrl-cmd-i`         |                |                    | [Script](https://atom.io/packages/script) - Keybinding remapped from original `cmd-i` to avoid conflict with Incremental Search
Open Terminal                  | `ctrl-alt-t`         | `alt-shift-t`  |                    | [Term2](https://atom.io/packages/term2)
Open Project                   | `ctrl-cmd-p`         | `alt-shift-p`  | `ctrl-alt-shift-p` | [Project Manager](https://atom.io/packages/project-manager)
Open In                        | `ctrl-alt-o`         |                |                    | [Open In](https://atom.io/packages/open-in)
Sublime Style Column Selection | `alt-mouse`          |                |                    | [Sublime Style Column Selection](https://atom.io/packages/Sublime-Style-Column-Selection)

### atom-beautify

```
Could not find 'php-cs-fixer'. The program may not be installed. //
mac：brew install homebrew/php/php-cs-fixer

Could not find 'autopep8'. The program may not be installed. //
sudo pip install --upgrade autopep8
```

## 参考

* [Atom Flight Manual](http://flight-manual.atom.io/)
* [官网](https://ide.atom.io/)
* [awesome-atom](https://github.com/mehcode/awesome-atom)

- 代码片段:~/.atom/snippets.cson <http://blog.csdn.net/u010494080/article/details/50993771>
- git <http://blog.csdn.net/u010494080/article/details/51229211>
