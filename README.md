# manifests
This is manifest(s) for rockchip rk3399 based board(s) for yocto project.

Work still in progress.
Current version is sumo.

# How-To

> rootfs buld:
```
> repo init -u https://github.com/SiennaStellar/manifests.git -b yocto-ibox3399-xen-devel
> source poky/oe-init-build-env
> cp ../layers/meta-rk3399-xen/conf/bblayers.conf conf/
> cp ../layers/meta-rk3399-xen/conf/local.conf conf/
> bitbake rk3399-basic-image
> sudo dd if=tmp/deploy/images/ibox3399/rk3399-basic-image-ibox3399.ext2 of=/dev/sdX2; sync
```
