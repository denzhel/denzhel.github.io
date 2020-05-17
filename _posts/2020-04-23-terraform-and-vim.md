---
layout: post
title: Terraform and Vim
date:   2020-04-23
categories: Terraform
---

Personally, I use Vim regularly instead of modern IDEs. I Love it !

I wanted to share a plugin that I use daily: vim-terraform.
Other than highlighting it provides auto formatting(indenting) of your code instead of running fmt manually.

I use Vundle as a Vim Plugin Manager, I'll show how to install it and the plugin itself.

First, install the plugin manager:
```shell
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

Add the following block to ~/.vimrc (this will configure Vim to use Vundle and install vim-terraform):
```shell
set nocompatible              " be iMproved, required
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'

" extra plugins go here
Plugin 'hashivim/vim-terraform'

call vundle#end()            " required
filetype plugin indent on    " required
```

In addition, add the following block to ~/.vimrc:
```shell
" ## Add Terraform Configurations ##
let g:terraform_align=1
let g:terraform_fmt_on_save=1
```

Open Vim and run:
```shell
:PluginInstall
```

Now, as soon as you edit .tf and save them with :w, Vim will automatically format your file.
