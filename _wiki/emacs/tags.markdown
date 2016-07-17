---
layout: wiki
title:  在Emacs中使用Ctags/Etags
tags: emacs,ctags,etags
---


参考
--

* [Tags - GNU Emacs Manual](https://www.gnu.org/software/emacs/manual/html_node/emacs/Tags.html)
* [Using tags in Emacs](http://zmalltalker.com/emacs/tags.html)
* [How to use ctags in Emacs effectively | Chen's blog](http://zmalltalker.com/emacs/tags.html)


基本用法
----

### create tags


* `find . -name "*.[chCH]" -print | etags -`
* `ctags -e -R folder1 folder2/subfolder # with exuberant-ctags`


### 跳转到实现(find-tag)

* `find-tag` (M-.)
* `find-tag-regexp` (C-M-.)
* `find-tag-other-window` (C-x 4 .)


同名时如何挑选: `C-u find-tag`

### 跳转的历史记录

* 回退 `pop-tag-mark`(M-*)
* 前进 `C-u - M-.`


### 用TAGS里面的符号来补全代码（不常用）

* `complete-tag`

### 搜索TAG出现的位置（不常用）


* 搜索: `tags-search`
* 搜索并替换: `tags-query-replace`

`tags-search` 只在TAGS列出的文件中搜索（其实不是搜索tags table中列出的符号，而是任意的regexp)

### 多tags table

* 添加新的tags table: `M-x visit-tags-table`
* 选择tags table: `M-x select-tags-table` (可以选中tags table set，即同时让多个生效)



第三方库
----

### 多个TAG同名时如何挑选

* [X] `anything-c-etags-select` (in anything-config.el)
* [X] `anything-etags+.el`
* [ ] `etags-select.el`
* [ ] `tags-tree.el`

>  乍一看anything-etags+比etags-stack强的是能用anything-etags+-select-at-point 能弥补M-.的不足，在出现同名 tag时有个挑选机会，但研究了一下发现anything-config自带的 anything-c-etags-select
>  已经可以根据当前光标处的文字缩小备选项，只不过需要加按C-u而已。
>
>    * anything-c-etags-select doesn't support multiple TAGS table (i.e. tags-table-list), but anything-etags+ does
>    * anything-c-etags-select doesn't honor tags-file-name, it always lookup TAGS file along the parent directories. If TAGS file lives in other folders, it fails. But anything-etags+ honors tags-file-name and anything-etags+ also has history commands, this makes tags-view not necessary any more.

**结论**: 推荐使用 _anything-etags+_的 `anything-etags+-select` 和 `anything-etags+-select-at-point`

### 列出当前buffer里面所有的类/函数

虽然大多数情况下没有什么用处，但对于某种类型的代码文件，major mode不支持imenu，而ctags支持分析它，就可以凑合一下了

* [X] `anything-ctags-current-file` (注意: 这里用的是exuberant-ctags程序对当前文件进行**即时**分析)
* [ ] `wl-anything-etags-in-currect-buffer` from [懒惰的程序员 : etags揭秘](http://alpha-blog.wanglianghome.org/2011/10/26/etags-beyond-the-manual/)


### 跳转的历史记录

Emacs内置:
  * 回退 `pop-tag-mark`(M-*)
  * 前进 `C-u - M-.`

比较: 

* [X] anything-etags+   <<https://github.com/fgeller/anything-etags-plus/>>
* [X] etags-stack
* [X] tags-view
* [ ] 新东西 history.el

> [emacs] anything-etags+有浏览历史记录功能，但要求先将M-.绑到它的anything-etags+-select-at-point,  而etags-stack 则可以利用emacs自己的`find-tag-marker-ring`
> 而tags-view似乎UI更清晰一些? etags-stack还是个stack, 会丢弃历史记录，而tags-view不会。另外，tags-view还支持gtags

   
推荐: _anything-etags+_ 提供的 `anything-etags+-history`， `anything-etags+-history-go-back` 和`anything-etags+-history-go-forward`
   



