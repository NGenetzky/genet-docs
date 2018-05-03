+++
title = "poky/oe-init-build-env"
+++

Let's have a clean environemnt, and then clone Poky.

```
└─> bash -norc
bash-4.3$ git clone git://git.yoctoproject.org/poky /tmp/poky
Cloning into 'poky'...
remote: Counting objects: 400896, done.
remote: Compressing objects: 100% (95425/95425), done.
remote: Total 400896 (delta 298916), reused 400592 (delta 298614)
Receiving objects: 100% (400896/400896), 145.04 MiB | 2.11 MiB/s, done.
Resolving deltas: 100% (298916/298916), done.
Checking connectivity... done.
bash-4.3$ 
```

Let's compare our env before and after the script.

```
bash-4.3$ env > /tmp/0 
bash-4.3$ source /tmp/poky/oe-init-build-env /tmp/build/
You had no conf/local.conf file. This configuration file has therefore been
created for you with some default values. You may wish to edit it to, for
example, select a different MACHINE (target hardware). See conf/local.conf
for more information as common configuration options are commented.

You had no conf/bblayers.conf file. This configuration file has therefore been
created for you with some default values. To add additional metadata layers
into your configuration please add entries to conf/bblayers.conf.

The Yocto Project has extensive documentation about OE including a reference
manual which can be found at:
    http://yoctoproject.org/documentation

For more information about OpenEmbedded see their website:
    http://www.openembedded.org/


### Shell environment set up for builds. ###

You can now run 'bitbake <target>'

Common targets are:
    core-image-minimal
    core-image-sato
    meta-toolchain
    meta-ide-support

You can also run generated qemu images with a command like 'runqemu qemux86'
bash-4.3$ env > /tmp/1
```

What is new in our 'env'?

```
bash-4.3$ diff /tmp/0 /tmp/1 | grep '^>'
> OLDPWD=/tmp
> PATH=/tmp/poky/scripts:/tmp/poky/bitbake/bin:/home/ngenetzky/bin:/home/ngenetzky/.local/bin:/home/ngenetzky/bin:/home/ngenetzky/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
> PWD=/tmp/build
> BBPATH=/tmp/build
> BUILDDIR=/tmp/build
> BB_ENV_EXTRAWHITE=ALL_PROXY BBPATH_EXTRA BB_NO_NETWORK BB_NUMBER_THREADS BB_SETSCENE_ENFORCE BB_SRCREV_POLICY DISTRO FTPS_PROXY FTP_PROXY GIT_PROXY_COMMAND HTTPS_PROXY HTTP_PROXY MACHINE NO_PROXY PARALLEL_MAKE SCREENDIR SDKMACHINE SOCKS5_PASSWD SOCKS5_USER SSH_AGENT_PID SSH_AUTH_SOCK STAMPS_DIR TCLIBC TCMODE all_proxy ftp_proxy ftps_proxy http_proxy https_proxy no_proxy 
> _=/usr/bin/env
```

What is new in our './build/conf'?

```
bash-4.3$ cat /tmp/build/conf/templateconf.cfg
meta-poky/conf
bash-4.3$ diff /tmp/poky/meta-poky/conf/bblayers.conf.sample  /tmp/build/conf/bblayers.conf
9,11c9,11
<   ##OEROOT##/meta \
<   ##OEROOT##/meta-poky \
<   ##OEROOT##/meta-yocto-bsp \
---
>   /tmp/poky/meta \
>   /tmp/poky/meta-poky \
>   /tmp/poky/meta-yocto-bsp \
bash-4.3$ diff /tmp/poky/meta-poky/conf/local.conf.sample  /tmp/build/conf/local.conf ; echo $?
0
```

