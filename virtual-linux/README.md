# Changes to built version 
- Added ethernet memory to vxhci.system protection domain
- Added config files from native linux to guest dependency
-  Removed these lines from Makefile in guest
```
cd ${TMP_PATH} ; curl "https://buildroot.org/downloads/buildroot-2024.02.1.tar.gz" --output buildroot-2024.02.1.tar.gz
cd ${TMP_PATH} ; tar -xf buildroot-2024.02.1.tar.gz
```
- Added linux-tool-usbtools.mk.in to deps/guest/tmp/buildroot-2024.02.1/package/linux-tools
- Modified Config.in in deps/guest/tmp/buildroot-2024.02.1/package/linux-tools to include the usbip package 
