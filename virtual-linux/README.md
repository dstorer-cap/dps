# Changes to built version 
- Added this line to libvmm makefile:  git -C libvmm reset --hard "dcfa24a2e5dbee7e90ab5a0e3a1432813d0e2a5a"
- Added ethernet memory to vxhci.system protection domain
- Enabled ethernet in the maaxboard.dts file (deps/dts/blt)
- Enabled usb devices in maaxboard.dts
- Added config files from native linux to guest dependency (deps/guest/blt)
-  Removed these lines from Makefile in guest
```
cd ${TMP_PATH} ; curl "https://buildroot.org/downloads/buildroot-2024.02.1.tar.gz" --output buildroot-2024.02.1.tar.gz
cd ${TMP_PATH} ; tar -xf buildroot-2024.02.1.tar.gz
```
- Added linux-tool-usbtools.mk.in to deps/guest/tmp/buildroot-2024.02.1/package/linux-tools
- Modified Config.in in deps/guest/tmp/buildroot-2024.02.1/package/linux-tools to include the usbip package 
