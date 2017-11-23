---
title: "tmux"
date: 2017-11-23T14:57:35+01:00
author: "mattische"
tags: [ "tmux", "terminal" ]
---

Isn't tmux just wonderful?

Start sessions with processes and them attach to them at a later time.


See this guide; <a href="http://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/">A Quick and Easy Guide to tmux</a>


<br />


Memo;

<code>
$ tmux        #start new session

$ tmux ls     #list available sessions

$ tmux attach -t 0 #attach to session 0, listed from tmux ls

$ tumux kill-session -t 0  #kill session 0
</code>


<br />
Key bindings;

<pre>
C-b % #split pane vertically


C-b " #split pane horisontally


C-b d #detach from session


C-b &lt;arrow-key&gt;  #move between panes 
</pre>
