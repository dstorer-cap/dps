# Yubi buildroot

## Package location 

- /home/dstorer/svn_apricot/apricot/development/wip/daniel/maaxboard-linux-nitrokey/yubi/buildroot-2024.02.1/package/yubihsm

## Configure buildroot

### Configure yubihsm

- Add config.in in package/yubihsm/

```
config BR2_PACKAGE_YUBIHSM
	bool "yubihsm"
	depends on BR2_PACKAGE_OPENSSL
	select BR2_PACKAGE_LIBOPENSSL if BR2_PACKAGE_OPENSSL
	depends on BR2_PACKAGE_LIBCURL
	depends on BR2_PACKAGE_LIBEDIT
	depends on BR2_PACKAGE_LIBUSB
	depends on BR2_PACKAGE_PCSC_LITE
```

- Add the location of the yubi Config.in to the Config.in in package under "Hardware Handling":
```
source "package/yubihsm/Config.in"
```

- Add yubihsm.mk file in package/yubihsm/
```
config BR2_PACKAGE_YUBIHSM
	bool "yubihsm"
	depends on BR2_PACKAGE_OPENSSL
	select BR2_PACKAGE_LIBOPENSSL if BR2_PACKAGE_OPENSSL
	depends on BR2_PACKAGE_LIBCURL
	depends on BR2_PACKAGE_LIBEDIT
	depends on BR2_PACKAGE_LIBUSB
	depends on BR2_PACKAGE_PCSC_LITE
```

- Add the patch file to modify the cmake file (0001-build-pkcs11.patch):
```
--- yubihsm-shell/CMakeLists.txt	2025-02-14 11:50:51.433950497 +0000
+++ yubi-shell-mod/yubihsm-shell/CMakeLists.txt	2025-02-14 11:50:18.289950495 +0000
@@ -192,8 +192,9 @@
 
 add_subdirectory (lib)
 
+add_subdirectory (pkcs11)
+
 if(NOT BUILD_ONLY_LIB)
-  add_subdirectory (pkcs11)
 
   if(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
     pkg_search_module (LIBPCSC REQUIRED libpcsclite)
```

### Configure and build (Inside yubi folder)
- Configure

'''
curl "https://buildroot.org/downloads/buildroot-2024.02.1.tar.gz" --output buildroot-2024.02.1.tar.gz
tar -xf buildroot-2024.02.1.tar.gz
rm -f buildroot-2024.02.1.tar.gz
mkdir config
make -C ./buildroot-2024.02.1 O="${PWD}/config" menuconfig
'''
'''
Note that Build Root creates content in "config", including a Makefile. Be aware that, somewhat annoyingly, this Makefile is hard coded to this specific Build Root instance. A sensible starting point is to select the most minimum default configuration:

```
Target Options::Target Architecture (AArch64 (little endian))
Target Architecture Variant (cortex-A3)
Toolchain::Toolchain type (External toolchain)
Build Options:: Buffer-overflow Detection (FORTIFY_SOURCE) :: None
System configuration::/dev management (Dynamic using devtmpfs + eudev)::Dynamic using devtmpfs + eudev
Kernel::Linux Kernel
Kernel::Linux Kernel::Kernel configuration (Use the architecture default configuration)
Target Packages::Libraries::Crypto::openssl support
Target Packages::Libraries::Networking::libcurl
Target Packages::Libraries::Text and terminal handling::libedit
Target Packages::Libraries::Hardware Handling::libusb
Target Packages::Libraries::Hardware Handling::pcsc-lite
Target Packages::Libraries::Hardware Handling::opensc
Target Packages::Libraries::Hardware Handling::ccid
Target Packages::Hardware Handling::yubihsm
Filesystem images::cpio the root filesystem (for use as an initial RAM filesystem)
Filesystem images::cpio the root filesystem::Compression method (gzip)
```

- Build
```
make -C ./buildroot-2024.02.1 O="${PWD}/config"
```

Retain the result:
```
cp config/images/Image ./src/Image
cp config/images/rootfs.cpio.gz ./src/rootfs.cpio.gz
```

Convert filesystem to uImage
```
mkimage -A arm -O linux -T ramdisk -a 0x44000000 -C gzip -n "Build Root File System" -d rootfs.cpio.gz initramfs.uImage
```

## Run on maaxboard
```
tftpboot 0x40480000 daniel_linux.img
tftpboot 0x44000000 bjemaaxboard.dtb
tftpboot 0x46000000 daniel_initramfs.uImage
booti 0x40480000 0x46000000 0x44000000
```

- Clean yubi
```
make -C ./buildroot-2024.02.1 O="${PWD}/config" yubihsm-dirclean
```

## Package dependencies 

