---
layout: post
title: "From Virtualenv to Pipenv"
description: "Migrate your Python project from Virtualenv to Pipenv."
tags: [python]
---

Install Pipenv as explained in [the docs](https://pipenv.readthedocs.io/en/latest/install/#installing-pipenv).

`cd` into your project's root directory (you hopefully have your `env/` folder, `.env` and `requirements.txt` files in the root directory) and optionally switch to a branch other than `master`:

```console
foo@bar:~$ cd my_project
foo@bar:~$ git checkout -b migrate_to_pipenv
```
(Sidenote: Check out [Oh My Zsh](https://ohmyz.sh/) and [wd](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#wd) for a supercharged terminal)

Spawn a shell. This will generate a `Pipfile` from your `requirements.txt`
```console
foo@bar:~$ pipenv shell
requirements.txt found, instead of Pipfile! Converting…
Warning: Your Pipfile now contains pinned versions, if your requirements.txt did.
We recommend updating your Pipfile to specify the "*" version, instead.
Spawning environment shell (/bin/zsh). Use 'exit' to leave.
```

The generated Pipfile will probably have pinned versions. It's recommended that you convert these to '*' versions (anyone knows of an automated way to do this?).
{% highlight toml %}
certifi = "==2017.11.5"     # convert this to
certifi = "*"
{% endhighlight %}

Install the packages to the new environment (this will update your packages). This will also generate a `Pipfile.lock`:
```console
foo@bar:~$ pipenv install
Pipfile.lock not found, creating…
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
Updated Pipfile.lock (bd2078)!
Installing dependencies from Pipfile.lock (bd2078)…
  🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 5/5 — 00:00:01
```

Update your `requirements.txt`:
```console
foo@bar:~$ pipenv lock -r > requirements.txt
```
