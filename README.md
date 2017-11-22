```
$ hugo new post/title_of_post.md                      # create new post (document/page)
$ hugo undraft content/post/title_of_post.md          # mark it for publishing. Not necessary if draft: true removed from page slug, see below
$ hugo                                                # publish
```


The page slug (heading) should be something like;

```
---
title: "Vagrant"
date: 2017-11-21T10:12:07+01:00
tags: [ "vagrant", "virtual-box", "dev"]
author: "mattische"
---
```

(remove the draft: true)



Use Git to add, commit and push the new content.

```
$ git status       # which files to add
$ git add files    
$ git commit -m "msg"
$ git push
```
