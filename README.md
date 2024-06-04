# Quick-Start Guide

## Prerequisites

The Overleaf Toolkit depends on the following programs:

- bash
- docker

We recommend that you install the most recent version of docker that is
available on your system.


## Install

First, let's clone this git repository to your machine:

```sh
$ git clone https://github.com/JamesQian1999/overleaf_server.git ./overleaf-toolkit
```

Next let's move into this directory:

```sh
$ cd ./overleaf-toolkit
```

For the rest of this guide, we will assume that you will run all subsequent commands from this directory.


## Take a Look Around

Let's take a look at the structure of the repository:

```sh
$ ls -l
```

Which will print something like this:

```
    bin
    CHANGELOG.md
    config
    data
    doc
    lib
    LICENSE
    README.md
```

The `README.md` file contains some useful information about the project, while the `doc` directory contains all of the documentation you will need to use the toolkit. The `config` directory will contain your own local configuration files (which we will create in just a moment), while the `bin` directory contains a collection of scripts that manage your overleaf instance.


## Initialise Configuration


Let's create our local configuration, by running `bin/init`:

```sh
$ bin/init
```

Now check the contents of the `config/` directory

```sh
$ ls config
overleaf.rc     variables.env     version
```

These are the three configuration files you will interact with:

- `overleaf.rc` : the main top-level configuration file
- `variables.env` : environment variables loaded into the docker container
- `version` : the version of the docker images to use


## Starting Up

The Overleaf Toolkit uses `docker compose` to manage the overleaf docker containers. The toolkit provides a set of scripts which wrap `docker compose`, and take care of most of the details for you.

Let's start the docker services:

```sh
$ bin/up
```

You should see some log output from the docker containers, indicating that the containers are running. 
If you press `CTRL-C` at the terminal, the services will shut down. You can start them up again (without attaching to the log output) by running `bin/start`. More generally, you can run `bin/docker-compose` to control the `docker compose` system directly, if you find that the convenience scripts don't cover your use-case.


## Install full TexLive and modify the upload file limit
Activate the sharelatex container
```sh
$ docker exec -it sharelatex bash
```
#### Install full TexLive

###### In the container
```
$ cd /usr/local/texlive # texlive default path
```
Update the texlive and tlmgr
```sh
$ wget http://mirror.ctan.org/systems/texlive/tlnet/update-tlmgr-latest.sh
$ sh update-tlmgr-latest.sh -- --upgrade
$ tlmgr update --self --all
```
Install full texlive
```sh!
$ tlmgr install scheme-full
```
Modify the Shell Escape

Add ```shell_escape = t``` at last line of ```/usr/local/texlive/{YEARS}/texmf.cnf```
```
% (Public domain.)
% This texmf.cnf file should contain only your personal changes from the
% original texmf.cnf (for example, as chosen in the installer).
%
% That is, if you need to make changes to texmf.cnf, put your custom
% settings in this file, which is .../texlive/YYYY/texmf.cnf, rather than
% the distributed file (which is .../texlive/YYYY/texmf-dist/web2c/texmf.cnf).
% And include *only* your changed values, not a copy of the whole thing!
%
shell_escape = t
```

#### Install font
```sh
$ apt install fonts-arphic-ukai
```

#### Modify the Upload File Limit

Modify the nginx config ```/etc/nginx/templates/nginx.conf.template```
Modify the max upload size ```client_max_body_size 50m```

Modify the overleaf config```/overleaf/services/web/config/settings.defaults.js```
Modify the max upload size ```maxUploadSize: 50 * 1024 * 1024, // 50 MB```

Exit the container
```sh
$ exit
```

###### Outside the container
Resrate the container
```sh
$ docker restart sharelatex
```


## Create the first admin account

In a browser, open <http://localhost/launchpad>. You should see a form with email and password fields.
Fill these in with the credentials you want to use as the admin account, then click "Register".

Then click the link to go to the login page (<http://localhost/login>). Enter the credentials.
Once you are logged in, you will be taken to a welcome page.

Click the green button at the bottom of the page to start using Overleaf. 


