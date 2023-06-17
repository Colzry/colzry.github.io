---
title: "Sublime Text配置Eclipse快捷键"
description: "Sublime Text配置Eclipse快捷键"
keywords: "Sublime Text"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Other
tags:
  - Sublime Text
---

```bash

    {
        "keys": ["alt+/"],
        "command": "auto_complete"
    }, {
        "keys": ["ctrl+i"],
        "command": "reindent"
    },
    // 当前行和下面一行交互位置
    {
        "keys": ["alt+up"],
        "command": "swap_line_up"
    }, {
        "keys": ["alt+down"],
        "command": "swap_line_down"
    },
    // 复制当前行到上一行
    {
        "keys": ["ctrl+alt+up"],
        "command": "duplicate_line"
    },
    // 复制当前行到下一行
    {
        "keys": ["ctrl+alt+down"],
        "command": "duplicate_line"
    },
    // 删除整行
    {
        "keys": ["ctrl+d"],
        "command": "run_macro_file",
        "args": {
            "file": "Packages/Default/Delete Line.sublime-macro"
        }
    },
    // 光标移动到指定行
    {
        "keys": ["ctrl+l"],
        "command": "show_overlay",
        "args": {
            "overlay": "goto",
            "text": ":"
        }
    },
    // 快速定位到选中的文字
    {
        "keys": ["ctrl+k"],
        "command": "find_under_expand_skip"
    },
    // { "keys": ["ctrl+shift+x"], "command": "swap_case" },
    {
        "keys": ["ctrl+shift+x"],
        "command": "upper_case"
    }, {
        "keys": ["ctrl+shift+y"],
        "command": "lower_case"
    },
    // 在当前行的下一行插入空行(这时鼠标可以在当前行的任一位置, 不一定是最后)
    {
        "keys": ["shift+enter"],
        "command": "run_macro_file",
        "args": {
            "file": "Packages/Default/Add Line.sublime-macro"
        }
    },
    // 定位到对于的匹配符(譬如{})(从前面定位后面时,光标要在匹配符里面,后面到前面,则反之)
    {
        "keys": ["ctrl+shift+p"],
        "command": "move_to",
        "args": {
            "to": "brackets"
        }
    },
    // 这个命令默认使用的是ctrl+shift+p
    {
        "keys": ["ctrl+p"],
        "command": "show_overlay",
        "args": {
            "overlay": "command_palette"
        }
    },
    // outline
    {
        "keys": ["ctrl+o"],
        "command": "show_overlay",
        "args": {
            "overlay": "goto",
            "text": "@"
        }
    },
    // 当前文件中的关键字(方便快速查找内容)
    {
        "keys": ["ctrl+alt+o"],
        "command": "show_overlay",
        "args": {
            "overlay": "goto",
            "text": "#"
        }
    },
    // open resource
    {
        "keys": ["ctrl+shift+r"],
        "command": "show_overlay",
        "args": {
            "overlay": "goto",
            "show_files": true
        }
    },
    // 文件内查找/替换
    {
        "keys": ["ctrl+f"],
        "command": "show_panel",
        "args": {
            "panel": "replace"
        }
    },
    // 全局查找/替换, 在查询结果中双击跳转到匹配位置
    {
        "keys": ["ctrl+shift+u"],
        "command": "show_panel",
        "args": {
            "panel": "find_in_files"
        }
    }, {
        "keys": ["ctrl+j"],
        "command": "show_panel",
        "args": {
            "panel": "incremental_find",
            "reverse": false
        }
    }, {
        "keys": ["ctrl+shift+j"],
        "command": "show_panel",
        "args": {
            "panel": "incremental_find",
            "reverse": true
        }
    },

    // Select text between brackets
    {
        "keys": ["alt+backspace"],
        "command": "bh_key",
        "args": {
            "lines": true,
            "plugin": {
                "type": ["__all__"],
                "command": "bh_modules.bracketselect"
            }
        }
    },

    // // Remove brackets
    // {
    //     "keys": ["alt+backspace"],
    //     "command": "bh_remove_brackets"
    // },

    // Convert single quote string to double quoted string and vice versa
    // Will handle escaping or unescaping quotes within the string
    {
        "keys": ["alt+'"],
        "command": "bh_key",
        "args": {
            "lines": true,
            "plugin": {
                "type": ["single_quote", "double_quote", "py_single_quote", "py_double_quote"],
                "command": "bh_modules.swapquotes"
            }
        }
    },
    // Swap brackets with another type
    {
        "keys": ["alt+["],
        "command": "swap_brackets"
    },
    // Surround selection with brackets from quick panel
    {
        "keys": ["alt+]"],
        "command": "wrap_brackets"
    },

    // plugin配置
    {
        "keys": ["alt+a"],
        "command": "alignment"
    }, {
        "keys": ["ctrl+shift+f"],
        "command": "js_format"
    },

    //Andy Edits plugin
    {
        "keys": ["ctrl+alt+d"],
        "command": "delete_edit"
    }, {
        "keys": ["ctrl+alt+h"],
        "command": "toggle_edits"
    }, {
        "keys": ["ctrl+alt+j"],
        "command": "quick_edits"
    }, {
        "keys": ["ctrl+alt+k"],
        "command": "prev_edit_line"
    }, {
        "keys": ["ctrl+alt+l"],
        "command": "next_edit_line"
    }, {
        "keys": ["ctrl+alt+m"],
        "command": "list_all_edits"
    }, {
        "keys": ["ctrl+alt+c"],
        "command": "create_edit"
    },
```
