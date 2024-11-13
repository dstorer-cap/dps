# Linux guest images

- Built with opensc and ccid packages installed

- Output stored in /home/dstorer/maaxboard-linux/nitrokey/tmp/config/images


# Using opensc to generate random entropy
- Start the pcscd service (PC/SC daemon) (which interfaces with smart card readers).
```
sudo systemctl restart pcscd
```
Generate entropy using the pkcs11-tool 
```
pkcs11-tool --slot 2 --module /usr/lib/x86_64-linux-gnu/opensc-pkcs11.so --generate-random 32 --output random_data.bin
```

## Explantion of tool

### Device detection by OpenSC

When OpenSC’s PKCS#11 module initializes, it automatically scans for connected, compatible devices. Once the PKCS#11 module finds the Nitrokey Start, it assigns it to a slot (in this case, slot 0), allowing pkcs11-tool to identify and use the device by accessing the assigned slot.

### Random Data Generation

When you request random data with --generate-random, the PKCS#11 module passes this request to the Nitrokey Start’s RNG (random number generator) if supported.
The generated data is then returned via the OpenSC module to pkcs11-tool, which writes it to random_data.bin as specified.

### List the slots 
```
pkcs11-tool --list-slots
```

## To do 
Make notes on the debian tools to see how the pcks11 tool uses the .so module to generate the random data.
- Will be using the .so module as it is -> need to know how the tool calls into it
- Look through the opensc code to determine how it generates random data and interfaces with the ntirokey
- Generate random function generates the random data

## Notes on pkcs11-tool.c 
- The generate_random function creates random data
- The generate_random function calls the C_GenerateRandom which is defined in the opensc-pkcs11.so module 

The command sequence involves multiple PKCS#11 function calls to set up the session, communicate with the token, and properly finalize the operation. Here’s a more comprehensive breakdown of the typical sequence of PKCS#11 API calls used in this process:

1. C_Initialize:

This function initializes the PKCS#11 library. It sets up the internal state and prepares the library for subsequent calls. Without this, no other PKCS#11 functions would work.

2. C_GetSlotList:

After initialization, pkcs11-tool needs to find available slots (i.e., connected tokens or devices). C_GetSlotList is called to retrieve a list of available slots, which represent potential devices (like the Nitrokey Start) where cryptographic operations can be performed.

3. C_OpenSession:

Once a slot has been identified, pkcs11-tool opens a session with it using C_OpenSession. This session is needed to interact with the token (Nitrokey Start) in that slot.

4. C_GenerateRandom:

With an open session, pkcs11-tool then calls C_GenerateRandom to request 32 bytes of random data from the token. The module (opensc-pkcs11.so) executes this function, which generates and returns the random data.

5. C_CloseSession:

After the random data is retrieved, pkcs11-tool closes the session with C_CloseSession, which releases any resources associated with the session.

6. C_Finalize:

Finally, C_Finalize is called to clean up and release any resources allocated by the PKCS#11 module during the process.

### To do
- Look at the build root documentation to determine how to add custom code to the build 
- Test building buildroot with the opensc module installed 
- Tom did it by unpacking the filesystem and adding his code and repacking the filesystem 

### Building buildroot with opensc 
- Opensc library enabled in: Target packages -> Libraries -> Hardware handling -> opensc
- ccid library enabled in: Target packages -> Libraries -> Hardware handling -> ccid
- Only need to enable these two libraries to make it work natively 

opensc

### Linux guest build
- Change the values so the initram is big enough (0x45000000 is the address that works for the opensc and ccid linux guest)

- Start the pcsc deamon
```
pcscd
```

- Command to generate entropy on maaxboard
```
pkcs11-tool --slot 1 --module /usr/lib/opensc-pkcs11.so --generate-random 32 --output random_data.bin
```

- Check the device tree physical addresses in the system file
- Add usb irq interrupt into system file (32 + interrupt from device tree)
- View output of log on native to see what happens with usb device initialisation 

11-11-2024
- Change dtx hours to apricot code
- See how the opensc tool indexes the nitrokeys 
- Strip out tooling code into wrapper code 

13-11-2024
- Find the device that is causing the spurious interrupts and remove the passthrough
- Follow toms instructions for virtio

Wrapper program for opensc tool
- User level program which gets to output file from the opensc tool and writes it to a memory region
- Need to use the UIO stuff (Tom) to define a memory region in the linux guest where the program can write to