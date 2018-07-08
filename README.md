# manifests
This is manifest(s) for rockchip rk3399 based board(s) for yocto project.

Work still in progress.
Current version is sumo.

# How-To

> rootfs buld:
```
> repo init -u https://github.com/SiennaStellar/manifests.git -b yocto-ibox3399-xen-devel
> repo sync -j4
> source poky/oe-init-build-env
> cp ../layers/meta-rk3399-xen/conf/bblayers.conf conf/
> cp ../layers/meta-rk3399-xen/conf/local.conf conf/
> bitbake rk3399-basic-image
> sudo dd if=tmp/deploy/images/ibox3399/rk3399-basic-image-ibox3399.ext2 of=/dev/sdX2; sync
```
# Known issues

meta-selinux: fix policycoreutils_2.7.bb error
```
 recipes-security/selinux/policycoreutils.inc | 4 ++--
 1 file changed, 2 insertions(+), 2 deletions(-)

diff --git a/recipes-security/selinux/policycoreutils.inc b/recipes-security/selinux/policycoreutils.inc
index 442b086..a9d0e48 100644
--- a/recipes-security/selinux/policycoreutils.inc
+++ b/recipes-security/selinux/policycoreutils.inc
@@ -64,8 +64,8 @@ RDEPENDS_${BPN}-setsebool += "\
 "
 RDEPENDS_${BPN} += "selinux-python"
 
-WARN_QA := "${@oe_filter_out('unsafe-references-in-scripts', '${WARN_QA}', d)}"
-ERROR_QA := "${@oe_filter_out('unsafe-references-in-scripts', '${ERROR_QA}', d)}"
+WARN_QA := "${@oe.utils.str_filter_out('unsafe-references-in-scripts', '${WARN_QA}', d)}"
+ERROR_QA := "${@oe.utils.str_filter_out('unsafe-references-in-scripts', '${ERROR_QA}', d)}"
 
 
 PACKAGES =+ "\
-- 
2.7.4
```
layers/meta-linaro/meta-linaro-toolchain/recipes-devtools/gcc/libgcc_linaro-5.2.bb:do_compile fail
```
build/tmp/work-shared/gcc-linaro-5.2-r2015.11-2/gcc-linaro-5.2-2015.11-2/libgcc/config/aarch64/linux-unwind.h
```
at line 55:
edit:
```
struct ucontext uc;
```
to:
```
ucontext_t uc;
```
(poky) libgcc-linaro-5.2-r2015.11-2 do_package_qa: QA Issue: No GNU_HASH in the elf binary
```
diff --git a/meta/recipes-devtools/gcc/libgcc.inc b/meta/recipes-devtools/gcc/libgcc.inc
index 5f1dff6..b5d88ab 100644
--- a/meta/recipes-devtools/gcc/libgcc.inc
+++ b/meta/recipes-devtools/gcc/libgcc.inc
@@ -38,5 +38,6 @@ do_package_write_ipk[depends] += "virtual/${MLPREFIX}libc:do_packagedata"
 do_package_write_deb[depends] += "virtual/${MLPREFIX}libc:do_packagedata"
 do_package_write_rpm[depends] += "virtual/${MLPREFIX}libc:do_packagedata"
 
-INSANE_SKIP_${PN}-dev = "staticdev"
+INSANE_SKIP_${PN}-dev = "staticdev ldflags"
+INSANE_SKIP_${PN} = "ldflags"
```
