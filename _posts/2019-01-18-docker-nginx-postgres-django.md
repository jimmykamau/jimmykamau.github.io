---
layout: post
title: "Using Djocker"
description: "Getting started with Djocker to dockerize a Django app with Nginx and Postgres."
tags: [python, docker, nginx, djocker, django]
---

[Djocker](https://github.com/jimmykamau/djocker) is a barebones app that can help with quickly setting up a dockerized Django application, with Nginx as a reverse proxy and PostgreSQL as the database. This guide will help you get started with it.

Ensure you have [docker-machine](https://docs.docker.com/machine/install-machine/) and [docker-compose](https://docs.docker.com/compose/install/) installed.

Create your app's repo, cd into it and checkout a working branch
```console
foo@bar:~$ mkdir my_django_app && cd my_django_app
foo@bar:~$ git init
foo@bar:~$ git checkout -b develop
```

Add the [Djocker](https://github.com/jimmykamau/djocker.git) repo as a fork
```console
foo@bar:~$ git remote add fork https://github.com/jimmykamau/djocker.git
```

Pull Djocker
```console
foo@bar:~$ git pull fork master
```

Change the name of the Django app's directory in the `web` folder from `djocker` to the name of your app
```console
foo@bar:~$ cd web
foo@bar:~$ mv djocker ./django_api
```

Update the following files:

{% highlight yaml %}
# docker-compose.yml

command: gunicorn django_api.wsgi:application -w 2 -b :8000
{% endhighlight %}

{% highlight python %}
# web/django_api/settings/base.py

ROOT_URLCONF = 'django_api.urls'
WSGI_APPLICATION = 'django_api.wsgi.application'
{% endhighlight %}

{% highlight python %}
# web/django_api/settings/__init__.py

if current_environment == "PRODUCTION":
    from django_api.settings.production import *
elif current_environment == "DEVELOPMENT":
    from django_api.settings.development import *
{% endhighlight %}

{% highlight python %}
# web/django_api/settings/development.py

from django_api.settings.base import *
{% endhighlight %}

{% highlight python %}
# web/django_api/wsgi.py

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'django_api.settings')
{% endhighlight %}

{% highlight python %}
# web/manage.py

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'django_api.settings')
{% endhighlight %}

cd into the app's root directory
```console
foo@bar:~$ cd ../
```

Create an `.env` file with the following variables:
{% highlight bash %}
ENVIRONMENT_SETTINGS=DEVELOPMENT
SECRET_KEY=my_super_secret_key
DATABASE_URL=postgres://postgres:@db:5432/django_api
{% endhighlight %}

Build the images
```console
foo@bar:~$ docker-compose build
```

Start the `db` service
```console
foo@bar:~$ docker-compose up -d db
```

Grab a `psql` shell from the `db` service
```console
foo@bar:~$ docker-compose exec db psql -U postgres -h db -p 5432
psql (11.1 (Debian 11.1-1.pgdg90+1))
Type "help" for help.

postgres=#
```

Create a database with the name you specified in the `.env` file, then exit the shell
```console
postgres=# CREATE DATABASE django_api;
CREATE DATABASE
postgres=# \q
```

Start the remaining services
```console
foo@bar:~$ docker-compose up -d
```

Ensure the services have been started successfully
```console
foo@bar:~$ docker-compose ps
         Name                       Command               State                    Ports
----------------------------------------------------------------------------------------------------------
my_django_app_db_1      docker-entrypoint.sh postgres    Up      0.0.0.0:5432->5432/tcp
my_django_app_nginx_1   nginx -g daemon off;             Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp
my_django_app_web_1     gunicorn django_api.wsgi:a ...   Up      8000/tcp
```

Visit [this](http://localhost/){:target="_blank"} url and you should be welcomed with the default Django page.