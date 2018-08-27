# manifests
This is manifest(s) for rockchip rk3399 based board(s) for yocto project.

Work still in progress.
Current yocto version: rocko.

# How-To

> Build:
```
repo init -u https://github.com/ihormatushchak/manifests.git -b yocto-ibox3399-xen-devel
repo sync -j4
source poky/oe-init-build-env
cp ../layers/meta-rk3399-xen/conf/bblayers.conf conf/
cp ../layers/meta-rk3399-xen/conf/local.conf conf/
bitbake rk3399-basic-image
```

# Known build issues

> ./md-unwind-support.h:55:21: error: field 'uc' has incomplete type

to fix, edit file:
```
tmp/work-shared/gcc-linaro-5.2-r2015.11-2/gcc-linaro-5.2-2015.11-2/libgcc/config/aarch64/linux-unwind.h
```
line 55:
```
    struct ucontext uc;
```
change to:
```
    ucontext_t uc;
```

> ERROR: libgcc-linaro-5.2-r2015.11-2 do_package_qa: QA Issue: No GNU_HASH in the elf binary: '<path-to-yocto>/build/tmp/work/aarch64-poky-linux/libgcc/linaro-5.2-r2015.11-2/packages-split/libgcc/lib/libgcc_s.so.1' [ldflags]

fix:
```
diff --git a/meta/recipes-devtools/gcc/libgcc.inc b/meta/recipes-devtools/gcc/libgcc.inc
index 1500fb5ace..1d35d51779 100644
--- a/meta/recipes-devtools/gcc/libgcc.inc
+++ b/meta/recipes-devtools/gcc/libgcc.inc
@@ -38,5 +38,6 @@ do_package_write_ipk[depends] += "virtual/${MLPREFIX}libc:do_packagedata"
 do_package_write_deb[depends] += "virtual/${MLPREFIX}libc:do_packagedata"
 do_package_write_rpm[depends] += "virtual/${MLPREFIX}libc:do_packagedata"
 
-INSANE_SKIP_${PN}-dev = "staticdev"
+INSANE_SKIP_${PN}-dev = "staticdev ldflags"
+INSANE_SKIP_${PN} = "ldflags"
```
# Deploy on SD card

```
Partition layout:

+-------------+-------------+-------------+-------------+
| partition 1 | partition 2 | partition 3 |             |
+-------------+-------------+-------------+-------------+
|             |             |             |             |
| 100M (vfat) | 300M (ext2) | 300M (ext2) | (Unmapped)  |
|             |             |             |             |
+-------------+-------------+-------------+-------------+
| rk3399-boot | dom0-roots  |   reserved  |             |
+-------------+-------------+-------------+-------------+
```
> copy xen image:
```
cp tmp/deploy/images/ibox3399/xen-ibox3399.uImage /media/<username>/boot/rk3399-boot/xen4.10-uImage
```
> copy dom0 kernel:
```
cp tmp/deploy/images/ibox3399/Image /media/<username>/boot/rk3399-boot/dom0-Image
```
> copy dom0-dtb:
```
cp tmp/deploy/images/ibox3399/Image-x3399-dom0-development-board.dtb /media/<username>/boot/rk3399-boot/x3399-dom0-development-board.dtb
```
> dd dom0 rootfs:
```
dd if=tmp/deploy/images/ibox3399/rk3399-basic-image-ibox3399.ext2 of=/dev/sdX2; sync
```
# Boot
> u-boot bootcmd:
```
fatload mmc 1:1 0x02000000 rk3399-boot/xen4.10-uImage; fatload mmc 1:1 0x01f00000 rk3399-boot/x3399-dom0-development-board.dtb; fatload mmc 1:1 0x03F80000 rk3399-boot/dom0-Image bootm 0x02000000 - 0x01f00000
