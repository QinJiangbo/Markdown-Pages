---
title: VIM文件头设置
date: 2016-10-30 11:07:15
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/vim-thumbnail.jpg
tags:
	- VIM
	- Linux
categories:
	- 架构师
	- Linux
keywords:
	- VIM
	- Linux
---
![](https://obrxbqjbi.qnssl.com/blog/image/vim-thumbnail.jpg)

很多编辑器都支持在源代码中自动添加作者信息的功能，据我所知Eclipse就支持，虽然我们的Vim(gvim)默认没有这个功能，但是只需要几行代码自己配置一下，我们一样可以让Vim(gvim)支持自动添加作者信息！

这里面需要使用VIM LANGUAGE的语法，不过不知道也没关系，看我的配置然后对应改一下就OK了！

``` vim
" SET COMMENT START
autocmd BufNewFile *.py,*.sh,*.c exec ":call SetComment()"

let $author_name = "Richard"
let $author_email = "qinjiangbo1994@163.com"

func SetComment()
    if expand("%:e") == 'py'
        call setline(1, '#coding=utf-8')
    elseif expand("%:e") == 'c'
        call setline(1, '// Linux C file')
    elseif expand("%:e") == 'sh'
        call setline(1, '#!/bin/bash')
    endif

    call append(1, '#*************************************************')
    call append(2, '#')
    call append(3, '#       Filename: '.expand("%"))
    call append(4, '#         Author: '.$author_name)
    call append(5, '#          Email: '.$author_email)
    call append(6, '#         Create: '.strftime("%Y-%m-%d %H:%M:%S"))
    call append(7, '#    Description: -')
    call append(8, '#')
    call append(9, '#*************************************************')
    normal G
    normal o
endfunc

" SET COMMENT END
```

大家对应上面的去改就行了，一般是在家目录~/.vimrc文件里面添加就行了。