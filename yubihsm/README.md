# YubiHSM

- Passphrase: 0001password

## Downloading and extracting packages

- Installed this sdk, yubihsm2-sdk-2024-09-debian11-amd64.tar.gz (sig) (2024-09-09),  from: https://developers.yubico.com/YubiHSM2/Releases/

- Download tarbell and extract tarbell
```
wget https://developers.yubico.com/YubiHSM2/Releases/yubihsm2-sdk-2024-09-debian11-amd64.tar.gz
tar -xzf yubihsm2-sdk-2024-09-debian11-amd64.tar.gz
```

- Installed required packages for pkcs11
```
# 1. Install base libraries first
sudo dpkg -i libyubihsm1_2.6.0_amd64.deb
sudo dpkg -i libykhsmauth1_2.6.0_amd64.deb
sudo dpkg -i libyubihsm-usb1_2.6.0_amd64.deb
sudo dpkg -i libyubihsm-http1_2.6.0_amd64.deb

# 2. Install development packages
sudo dpkg -i libyubihsm-dev_2.6.0_amd64.deb
sudo dpkg -i libykhsmauth-dev_2.6.0_amd64.deb

# 3. Install tools and applications
sudo dpkg -i yubihsm-connector_3.0.5-1_amd64.deb
sudo dpkg -i yubihsm-setup_2.3.2-1_amd64.deb
sudo dpkg -i yubihsm-auth_2.6.0_amd64.deb
sudo dpkg -i yubihsm-pkcs11_2.6.0_amd64.deb
sudo dpkg -i yubihsm-shell_2.6.0_amd64.deb
sudo dpkg -i yubihsm-wrap_2.6.0_amd64.deb
```

## Connect to yubihsm over network
```
sudo yubihsm-connector &
yubihsm-shell
yubihsm> connect
```

## Using pkcs11-tool
- Create a yubihsm_pkcs11.conf file, .conf for lithium provided below

```
# URL of the connector to use. This can be a comma-separated list
# serial number is serial of yubihsm device
connector = yhusb://serial=0030735176
```

- Info on using a usb connector (from https://docs.yubico.com/hardware/yubihsm-2/hsm-2-user-guide/hsm2-sdk-tools-libraries.html#libyubihsm)
```
USB Connector
This kind of Connector is a direct-access USB backend that talks directly with a YubiHSM device. The USB Connector is built into libyubihsm. This renders it unnecessary to run an additional component (i.e., the external Connector) at the cost of requiring exclusive access to a YubiHSM device.

To select this type of backend the connector URL should use the yhusb scheme. For example, to use a local device with serial number 123456 use yhusb://serial=123456.
```

- List slots
```
sudo pkcs11-tool --module /usr/lib/x86_64-linux-gnu/pkcs11/yubihsm_pkcs11.so --list-slots
```

- Generate random data 
```
sudo pkcs11-tool --module /usr/lib/x86_64-linux-gnu/pkcs11/yubihsm_pkcs11.so -l -p 0001password --generate-random 32 --output random_data.bin
```

# Linux guest with yubi 

- Add the packages into buildroot
- Cross compile the yubihsm binaries for the maaxboard

## Instructions

- git clone git@github.com:Yubico/yubihsm-shell.git

- pip install pre-commit

- pre-commit install

- mkdir build && cd build

- export PATH=/home/dstorer/yubihsm/arm-gnu-toolchain-12.2.rel1-x86_64-aarch64-none-elf/bin:$PATH

- cmake .. -D CMAKE_C_COMPILER=aarch64-none-elf-gcc -D CMAKE_C_COMPILER_WORKS=TRUE

- cmake .. CMAKE_DISABLE_FIND_PACKAGE_gengetopt

## To do
- Build the pkcs11-module of the yubihsm-shell
- Minimal build of yubihsm-shell on lithium to see what the minimal dependencies are



## Helpful function
- Locate .so packages
```
apt-file search yubihsm_pkcs11.so
```

- Kill connector 
```
 ps aux | grep yubihsm-connector
 sudo kill -9 <pid>
 ```