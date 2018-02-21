---
title: "pipenv"
date: 2018-02-21T09:32:21+01:00
author: "mattische"
tags: [ "pipenv", "python", "pip" ]
---


**pipenv** is a tool like npm - it creates, per project, an isolated virtual env.

<pre>
Pipenv is a tool that aims to bring the best of all packaging worlds (bundler, composer, npm, cargo, yarn, etc.) 
to the Python world. 
Windows is a first–class citizen, in our world.


It automatically creates and manages a virtualenv for your projects, as well as adds/removes packages from your 
Pipfile as you install/uninstall packages. 

It also generates the ever–important Pipfile.lock, which is used to produce deterministic builds.
</pre>

__source__: <a href="https://docs.pipenv.org/">https://docs.pipenv.org/</a>

<br><br><br>


### install

Install pipenv;


```
$ pip install pipenv
```


No pip?
```
$ sudo apt-get install python-pip python-dev build-essential 
```

### using 
```
$ cd my_python_project

$ pipenv install jupyter requests

$ pipenv run main.py
```

Voila. 
You've got an virtual env and a Pipfile (containing installed packages - like a package.json /w node/npm)
