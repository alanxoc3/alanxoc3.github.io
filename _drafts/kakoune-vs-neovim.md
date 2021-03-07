---
layout: post
title:  "Kakoune vs Neovim"
categories: blog
tags: [code]
---
Yesterday, I mentioned that I started using Kakoune a few months ago in a
youtube thread. Someone then asked me what the differences are between Kakoune
and Neovim. I ended up writing a consideral amount of thoughts of the pros and
cons of Neovim vs Kakoune. So I'll share that in a blog post too!

Kakoune is not for everyone, but here are some of the pros and cons for me:

# Pros

In my opinion, Kakoune follows the unix philosophy more closely than neovim does. For example, kakoune doesn't have multiple windows or floating windows. Instead it encourages you to make use of your terminal multiplexer or window manager. That can be seen as a drawback, but I see it as a benefit, since it encourages more thought & development towards the editor experience.

Kakoune is built with shell integration in mind. One way to demonstrate this is that it doesn't have much of a configuration language. To create any complex logic or plugins for kakoune, you have to write it in another language and kakoune will comunicate with it via environment variables and stdout. This encourages people to write programs that can server both as useful unix scripting utilities and also kakoune plugins. I wish other editors followed a similar pattern, because that promotes plugin authors to write programs that could be useful to other editors or useful for scripting.

Kakoune does multiple cursors instead of visual selection or global search and replace. In my experience so far, multiple cursors are much more fun than global search and replace. I'm pretty sure they can do everything visual selection can do. Plus they can do other cool things, like rotating all text multiple cursor selections, which is something I don't know if other IDE multiple cursor implementations can handle. You can also filter through your selections, which isn't really a concept in vim. 

Plus there are a bunch of nice small things like:
- Easy shell integration with the pipe character, instead of the ":%!" thing in vim.
- Macros default to one register, so just type "q" to playback your macro, instead of "@q".
- Modes outside of normal & insert have a menu that shows and describes keybindings. This makes it easy to add documentation for user and plugin keybindings.
- There is a built in keybinding for inserting a blank line (alt+o). That's something I had to create myself in neovim because I noticed I was typing "o<esc>k" too much.
- Kakoune uses standard regex, instead of vim's specific regex format that I always forget.
- Generally, the default editor keybindings in kakoune are somewhat similar to neovim's, but much more thoughtout. Neovim can't do this, because they aim to be fully compatible with vi/vim out of the box.

# Cons: 

There are a lot less plugins, because there are not as many users. Also plugins tend to be written in a wider variety of languages, which I imagine could make it harder to manage a lot of plugins.

Kakoune doesn't handly formatting or indentation well, at least not out of the box. The idea is that you should let an external command help you format things that are specific to your file. But that doesn't apply to every file format. Neovim sometimes guesses what tab should do based on context or the file format, which sometimes seems magic to me, but it's usually nice. 

Neovim is starting to support treesitter, which kakoune doesn't support at all. Kakoune's regex highlighting also doesn't feel as mature as neovim's.

I also have a macbook and I was having problems getting terminal support for some of Kakoune's alt keybindings to work.
