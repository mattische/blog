---
title: "ssh"
date: 2018-02-20T10:02:58+01:00
author: "mattische"
tags: [ "ssh" ]
---






### generate ssh key

Generate your key;

<pre>

$ ssh-keygen -t rsa

</pre>

Follow the prompts - choose default location where keys will be saved.

A __~/.ssh__ directory hase been created with your keys in it. The **.pub** is your public key an the other file is your private key.



References

<a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2">Digital Ocean</a>, <a href="https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/">Github</a>


<br><br>


### copy your public key to the server

This assumes there is no **.ssh** directory on the server. Otherwise, remove mkdir-part;



```
$ cat ~/.ssh/id_rsa.pub | ssh usero@<IP_ADDRESS> "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >>  ~/.ssh/authorized_keys"

```





or:




```
$ ssh-copy-id user@<IP_ADDRESS>
```



### ssh config file

Place a file in **~/.ssh** named config.

Example config;

<pre>
Host myServer
  HostName <IP_ADDRESS>
  User userName

Host dev
  HostName 123.45.67.890
  UserName dev


ServerAliveInterval 30
</pre>


Connect using Host-alias;

```
$ ssh dev
```

**ServerAliveInterval** sends dummy packages every x seconds to prevent tunnels/connection drop.

