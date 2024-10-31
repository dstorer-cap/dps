
# Using opensc to generate random entropy
- Start the pcscd service (PC/SC daemon) (which interfaces with smart card readers).
```
sudo systemctl restart pcscd
```
Generate entropy using the pkcs11-tool 
```
pkcs11-tool --module /usr/lib/x86_64-linux-gnu/opensc-pkcs11.so --generate-random 32 --output random_data.bin
```

## Explantion of tool

### Device detection by OpenSC

When OpenSC’s PKCS#11 module initializes, it automatically scans for connected, compatible devices. Once the PKCS#11 module finds the Nitrokey Start, it assigns it to a slot (in this case, slot 0), allowing pkcs11-tool to identify and use the device by accessing the assigned slot.

### Random Data Generation

When you request random data with --generate-random, the PKCS#11 module passes this request to the Nitrokey Start’s RNG (random number generator) if supported.
The generated data is then returned via the OpenSC module to pkcs11-tool, which writes it to random_data.bin as specified.