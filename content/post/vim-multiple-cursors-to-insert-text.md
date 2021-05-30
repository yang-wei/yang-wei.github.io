+++
title = "Vim Multiple Cursors to Insert Text"
date = "2014-09-24"
slug = "2014/09/24/vim-multiple-cursors-to-insert-text"
categories = ["vim"," multiple cursors"]
description = ""
+++

Quick blog for multiple cursor in Vim. For example we have the html below.
```html
<div id="id1">1</div>
<div id="id2">2</div>
<div id="id3">3</div>
<div id="id4">4</div>
```
And we want to insert same class after id for every div element. Here is what we do in Vim.
<!-- more -->
First place cursor after `id="id1"`.

Use `Ctrl-v`, and `jjj`(move 3 lines below) to select visual block.

Press `I` to switch to insert mode and start typing our class eg:`class="my-class"`. Do not worry if there is only 1 line moving.

After finish typing, press `Esc` and we will get the result we want.
```
<div id="id1" class="myClass">1</div>
<div id="id2" class="myClass">2</div>
<div id="id3" class="myClass">3</div>
<div id="id4" class="myClass">4</div>
```
Deleting the class also works same way.

Place cursor on before `class=myClass`, use `Ctrl-v` and move the visual mode 3 lines below by `jjj`.
Use `l` to highlight `class=myClass` and press `d`/`x` to delete the word.

```
<div id="id1">1</div>
<div id="id2">2</div>
<div id="id3">3</div>
<div id="id4">4</div>
```

That's it for this post. I just want to do this without the `vim-multiple-curson` plugin.



