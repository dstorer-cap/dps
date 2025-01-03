# Building picolibc for none-elf compiler

## Create a cross-compilation configuration file (cross-aarch64-none-elf.txt):

```
[binaries]
# Meson 0.53.2 doesn't use any cflags when doing basic compiler tests,
# so we have to add -nostdlib to the compiler configuration itself or
# early compiler tests will fail. This can be removed when picolibc
# requires at least version 0.54.2 of meson.
c = ['ccache', 'aarch64-none-elf-gcc', '-nostdlib']
ar = 'aarch64-none-elf-ar'
as = 'aarch64-none-elf-as'
ld = 'aarch64-none-elf-ld'
nm = 'aarch64-none-elf-nm'
strip = 'aarch64-none-elf-strip'
# only needed to run tests
exe_wrapper = ['sh', '-c', 'test -z "$PICOLIBC_TEST" || run-aarch64 "$@"', 'run-aarch64']

[host_machine]
system = 'baremetal'
cpu_family = 'aarch64'
cpu = 'aarch64'
endian = 'little'

[properties]
skip_sanity_check = true
link_spec = '--build-id=none'
specs_extra = ['*libgcc:', '-lgcc']
default_flash_addr = '0x40000000'
default_flash_size = '0x00400000'
default_ram_addr   = '0x40400000'
default_ram_size   = '0x00200000'
```

## Run the meson setup command to configure the build:

```
meson setup build-aarch64 \
    --cross-file scripts/cross-aarch64-none-elf.txt \
    --prefix=${PWD}/../../picolibc_build \
    -Dnewlib-global-errno=true \
    -Dnewlib-reent-small=true \
    -Dnewlib-io-long-long=false 
```

## Build library with ninja

```
ninja -C build-aarch64
```

## Install directory

```
ninja -C build-aarch64 install
```