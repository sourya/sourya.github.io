---
layout: base
title: Better Vim Usage
summary: Get the best out of Vim
posted_date: 2018-October-22
author: Sourya
---

## Vim Usage Guidelines

#### Scope
This article aims at getting the best out of the Vim editor. However, I will not focus on the shortcuts and the commands for Vim. You can find plenty of them on the internet.
Rather, I would like to focus on the way I use Vim, and how those practices have helped me.


#### 1. Disable the arrow keys

Vim has an excellent mode of moving through a file, using the h, j, k and l keys. However, as someone who has come to Vim from another editor, this seems (and, in fact, is) quite unnatural. To get the most out of Vim, disable the arrow keys for navigation. Doing so, you'll get used to the `hjkl` mapping. It will be frustrating in the beginning, but it will pay off in speed.

The code below disable the arrow keys in both insert and normal modes.
```
map <left> <nop>
map <right> <nop>
map <up> <nop>
map <down> <nop>

imap <up> <nop>
imap <down> <nop>
imap <left> <nop>
imap <right> <nop>
```
I have come to realize, though, that this is not a very good way to do things. Arrow keys are disabled even in the plugins that
might require arrow keys to navigate. Also, when using `Ctrl-n` autocomplete, you cannot use arrow keys to navigate. I
have work-arounds for all those problems, but I would suggest you enable the up and down arrow keys once you get the hang of `hjkl`.
(Because I use left and right arrow keys for another purpose, explained below).

#### 2. Use caps-lock as escape

If you intend to use Vim well, you will end up using the escape key a lot. However, the escape key is quite far from the
regularly used keys in Vim, making it a little hard to reach. I do not use the caps-lock key at all
(I tend to use shift to capitalize whatever I occassionally capitalize). So, I mapped my caps-lock to work as the escape key.
So much reachable, so much faster.

There is no Vim mapping that I know of that would map caps-lock to function as escape. So, I changed it on the OS level.
This [link](https://stackoverflow.com/questions/2176532/how-to-map-caps-lock-key-in-vim) should help you understand why.

#### 3. Using and navigating between Vim tabs and splits

Tabs can be used in Vim like in any other text editor, to open multiple files. To open a new tab, use `:tabnew /path/to/file`.
To navigate between these tabs, I  use the left and right arrow keys that we had disabled in a previous step.
This saves the effort of typing `:tabnext` and `tabprev`.

```
nnoremap <right> :tabnext<CR>
nnoremap <left> :tabprev<CR>
```

I do not use splits a lot, so I tend to use the standard mapping: `Ctrl-w` followed by `h` or `l`, depending on whether I want
to move to the left split or the right.

#### 4. Highlighting a search and un-highlighting it

When you search for something in Vim, you'd like all occurrences of the pattern to be highlighted.

```
set hlsearch
```
Add this in your .vimrc to do so.

When you're done searching, and wish the highlight to disappear,

```
nnoremap <silent> <Esc><Esc> <Esc>:nohlsearch<CR><Esc>
```
press the escape key (or is it caps-lock already) twice.


#### 5. Going back to the previous cursor location

Use `Ctrl-o` to go back to the previous cursor location. Comes in handy more often that you'd think.

#### 6. Navigating through files

I tend to specify the entire path of the file I am trying to open.

```
:tabnew /path/to/file
```
[Gary Bernhardt](https://www.destroyallsoftware.com/), however, suggests using the `Command-T` plugin,
similar to `Ctrl-P` in Sublime Text, that searches for the file through the entire directory in a better way.

I specify the entire path because it helps me remember the exact location of every file. It also helps me
remember how the code is arranged. Fuzzy search, however, gets your work done faster. Choose as you wish.

#### 7. Repeating actions

While using Vim, you shouldn't have to repeat yourself too often. There are repeat commands in Vim that will help you
redo the previous actions. Try reading up on the usage of `.`, `&`, `n`, and `N`.

#### Further help

Gary Bernhardt suggests that you take a look at all the keys on the keyboard, and figure out which keys you do not know
the usage of. Then it's a matter of typing
```
:help <key>
```
to read the documentation. Take note, however, that some keys do not make sense by themselves. `g`, for example, is always
bound to some other key to make sense. That is specified in the help docs.

Explore!
