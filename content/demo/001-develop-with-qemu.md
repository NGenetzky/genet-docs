+++
title = "meta-genet/001-develop-with-qemu"
+++

Let's start by obtaining all 'meta' layers that we want to use recipes from.
Choose a directory that you have plenty of space.

### Fetch

```
git clone --recurse-submodules \
    git@github.com:NGenetzky/meta-genet.git
```

### Configure

We will set "TEMPLATECONF" to specify template for bblayers.conf and
local.conf, and then we will source the init-build-env script to initalize a
build directory.

```
source meta-genet/templates/set-templateconf meta-genet/templates/001-develop-with-qemu/
```
```
source meta-genet/init-build-env /data/workspace/build
```

Next we want to configure the build. The eaiest way to do this is editing
`build/conf/local.conf`. These are a few of the things we would like configured
for this build, some of them have been made in the TEMPLATECONF.

1. Set `MACHINE='qemux86-64'`
2. Set `PACKAGE_CLASSES='package_deb'`
3. Enable `EXTRA_IMAGE_FEATURES = "package-management`.
4. Configure mirrors for SSTATE and SOURCE.

Let's configure where our mirrors are located. Edit the the following values to
indicate where you have mirrors, such as in `/data/yocto/sstate-cache/` and
`/data/yocto/downloads`.

```
SSTATE_MIRRORS ?= "\
file://.* file:///data/yocto/sstate-cache/PATH \
"

SOURCE_MIRROR_URL ?= "file:///data/yocto/downloads/"
```

### Compile

Now we will perform the actual build, and then use qemu to run a virtual
machine with the image we just built. 'kvm' is for performance, and 'nographic'
and 'serial' causes qemu to stay in the terminal rather than launching the GUI.

```
bitbake core-image-minimal
```

### QEMU with ext image

```
runqemu \
    build/tmp/deploy/images/qemux86-64/core-image-minimal-qemux86-64.ext4 \
    kvm nographic serial
```

### QEMU with NFS

Let's make it even easier to develop on this virtual machine by running it from
a NFS mount. This allows the host computer to have access to filesystem
actively being used by the emulated machine.  For more information read the
following section of the manual:
[qemu-running-under-a-network-file-system-nfs-server](https://www.yoctoproject.org/docs/latest/mega-manual/mega-manual.html#qemu-running-under-a-network-file-system-nfs-server).

This approach depends on the following target being built. Then we will extract
and export the rootfs image via an NFS server.

```
bitbake meta-ide-support
```

```
runqemu-extract-sdk \
    build/tmp/deploy/images/qemux86-64/core-image-minimal-qemux86-64.tar.bz2 \
    nfs/core-image-minimal
runqemu-export-rootfs start \
    /data/yocto/2.4/b/nfs/core-image-minimal
```

Finally, we run qemu with nfsroot instead of the ext4 image.

```
runqemu \
    nfs/core-image-minimal \
    kvm nographic serial
```


