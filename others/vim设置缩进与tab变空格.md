#### 打开自动缩进

```shell
:set autoindent
在此之后，如果在一行的开头输入空格或制表符，那么后续的新行将会缩进到相同的位置。在命令模式下，输入 :set autoindent，然后按回车打开自动缩进。通过设置 shiftwidth 确定缩进级别。例如，:set shiftwidth=4 把每级缩进设置为四个空格
```

### 设置缩进级别

```shell
:set shiftwidth=4
在命令模式下，可以使用 >> 命令让现有的一行增加一级缩进，使用 << 命令减少一级缩进。在这些命令前面加上一个整数，即可让多行增加或减少一级缩进。例如，把游标放在第 6 行的开头，进入命令模式之后，输入 5>> 就会让下面五行增加一级缩进。显示结果。
```

可以使用 `:set noautoindent` 命令关闭自动缩进。还可以使用这个命令和 `autoindent` 命令的简写，即 `:set ai` 和 `:set noai`。还可以使用 `:set ai sw=4` 在一个命令中打开缩进并设置缩进级别。

如果希望每次启动 [vi](https://www.codebye.com/tag/vi) 会话时都启用自动缩进并把缩进级别设置为四个空格，那么在主目录中的 .exrc 文件中添加 `set ai sw=4` 行。

### 设置Tab改空格

```shell
:set ts=4
:set expandtab
```

综上一般情况下更改四个即可：

```shell
set ts=4
set sw=4
set expandtab
set autoindent
```

