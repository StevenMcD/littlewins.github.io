---
layout: email
title: "Send me a note as SOON AS YOU GET HERE"
from: rob@redfour.io
date: "November 22, 2015"
category: email
---

Take your time why don't you! It's **only your first day** and all :p. OK seriously here's the deal: *we have 10 days before all hell breaks loose*. I need to get you up and running as quickly as I can.

I'm sure you've [installed Elixir](http://elixir-lang.org/install.html) already. If you haven't do that now - I don't have to hold your hand through that do I?

Once that's done, check to be sure you have it setup right:

```sh
iex
```

You should see something that looks like this:

```
Erlang/OTP 18 [erts-7.1] [source] [64-bit] [smp:8:8] [async-threads:10] [hipe] [kernel-poll:false] [dtrace]

Interactive Elixir (1.1.1) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)>
```

Don't worry if you don't know what it means. If this doesn't work for you [read up on the docs](http://elixir-lang.org/install.html).

## Editing Code

You probably have your favorite code editor; rock on then. If you're on Windows you're welcome to try out Visual Studio but good luck with that. If you're on a Mac/Linux/Whatever I've found the following setups work really well. I've tried all of them - here they are in order.

### Atom

[The Atom text editor](http://atom.io) is really, really well done and what you'll find many Elixir heads working in. That's what I use on an almost daily basis but lately I've been favoring Vim (more on that below).

You'll probably want to install a few packages too. Here's what I went with:

![Dark Theme](../../img/mail/atom_theme.png)

*Theme: Solarized Dark with Dark Theme*

I only have one package install in support of Elixir (the language-elixir package):

![language-elixir](../../img/mail/language-elixir.png)

*The language-elixir package*

I have a few snippets I use, but when I got started I forced myself to write the code out so my fingers got used to typing the syntax. I'll talk more about that later but for now here's a snippet I think you'll use a lot:

```
'.source.elixir':
  'IO Log':
    'prefix': 'cl'
    'body': "IO.inspect $1"
```

Don't worry if you're not familiar with Atom snippets - they work like any code editor. Go to `Atom/Open Your Snippets` in the menu and you'll see a page to enter them. Just paste the above. It's basically `console.log` for Elixir - very valuable for troubleshooting.

### Vim

I love Vim. Seriously I've never moved faster in *any* editor - but when working with a new language (unless you're a Vim-head that is) it can be daunting.

You probably have Vim setup the way you like - but I'll share my setup with you just for fun as I've found it to be pretty damn effective working with Elixir:

![language-elixir](../../img/mail/vim.png)

*My Vim setup*

 - Solarized for the colors
 - [command-t](https://github.com/wincent/Command-T) for finding files (invaluable)
 - [vim-airline](https://github.com/bling/vim-airline) for letting me know what's going on (git branch, etc)
 - [vim-elixir](https://github.com/elixir-lang/vim-elixir) for syntax etc

I find `vim-elixir` to be pretty good, but it also looks a bit weird in the solarized color scheme. I also had to make sure that I updated command-t to ignore various Mix-related files (I'll tell you what that means later).

Finally I mapped a command in `.vimrc` to run some tests (again: more on that later). Anyway - here are all my `.vimrc` mods:

```
"VIM Airline
let g:airline#extensions#tabline#enabled = 1
let g:airline_powerline_fonts = 1
if !exists('g:airline_symbols')
  let g:airline_symbols = {}
endif
" unicode symbols
let g:airline_left_sep = '»'
let g:airline_left_sep = '▶'
let g:airline_right_sep = '«'
let g:airline_right_sep = '◀'
let g:airline_symbols.linenr = '␊'
let g:airline_symbols.linenr = '␤'
let g:airline_symbols.linenr = '¶'
let g:airline_symbols.branch = '⎇'
let g:airline_symbols.paste = 'ρ'
let g:airline_symbols.paste = 'Þ'
let g:airline_symbols.paste = '∥'
let g:airline_symbols.whitespace = 'Ξ'

set guifont=Inconsolata:h14.00 "best font ever
"ignored directories which command-t will use too. Make sure _build is in here
set wildignore=*.o,*.obj,.git,node_modules/**,bower_components/**,**/node_modules/**,_build/**,deps/**

"run tests right from vim - super helpful
map <leader><space> :!mix test<CR>
```

## That's a Win! Now Get Moving!

Sorry to be pushy but there's a lot going on here right now - we have a new CTO and she's ready to can the entire effort. Thinks Elixir is a fad. I'm in meetings all day but I'll send you a draft of the Getting Started Manual that should get you off the ground.

The important thing for you right now is to *get your eyes familiar* with the syntax as quickly as I can. I need you **writing code yesterday**. No pressure.

{% include next_page.html link="../../manual/letting-elixir-soak-in" text="Letting Elixir Soak In" %}
