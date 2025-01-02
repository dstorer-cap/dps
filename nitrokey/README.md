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

## Kill pcscd thread

Find the Process ID (PID): Run the following command to find the PID of pcscd:

bash
Copy code
ps aux | grep pcscd
This will list all processes with pcscd in their name. Note the PID (first number) of the process.

Kill the Process: Use the kill command with the PID to terminate it:

bash
Copy code
sudo kill -9 <PID>
Replace <PID> with the actual PID you found in the previous step.

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

Wrapper program for opensc tool
- User level program which gets to output file from the opensc tool and writes it to a memory region
- Need to use the UIO stuff (Tom) to define a memory region in the linux guest where the program can write to

### Indexing smartcards
```
pkcs11-tool --list-slots
```
Output:
Available slots:                                                                                                                                                                                                         14:27:41 [155/19911]
Slot 0 (0x0): Nitrokey Nitrokey Start (FSIJ-1.2.19-A42D8256) 00 00
  token label        : OpenPGP card (User PIN)
  token manufacturer : OpenPGP project
  token model        : PKCS#15 emulated
  token flags        : login required, rng, token initialized, PIN initialized
  hardware version   : 2.0
  firmware version   : 2.0
  serial num         : fffea42d8256
  pin min/max        : 6/127
Slot 1 (0x1): Nitrokey Nitrokey Start (FSIJ-1.2.19-A42D8256) 00 00
  token label        : OpenPGP card (User PIN (sig))
  token manufacturer : OpenPGP project
  token model        : PKCS#15 emulated
  token flags        : login required, rng, token initialized, PIN initialized
  hardware version   : 2.0
  firmware version   : 2.0
  serial num         : fffea42d8256
  pin min/max        : 6/127
Slot 2 (0x4): Nitrokey Nitrokey Start (FSIJ-1.2.19-7AF10C50) 01 00
  token label        : OpenPGP card (User PIN)
  token manufacturer : OpenPGP project
  token model        : PKCS#15 emulated
  token flags        : login required, rng, token initialized, PIN initialized
  hardware version   : 2.0
  firmware version   : 2.0
  serial num         : fffe7af10c50
  pin min/max        : 6/127
Slot 3 (0x5): Nitrokey Nitrokey Start (FSIJ-1.2.19-7AF10C50) 01 00
  token label        : OpenPGP card (User PIN (sig))
  token manufacturer : OpenPGP project
  token model        : PKCS#15 emulated
  token flags        : login required, rng, token initialized, PIN initialized
  hardware version   : 2.0
  firmware version   : 2.0
  serial num         : fffe7af10c50
  pin min/max        : 6/127
```

- Use hex value to index slot not number after slot

To do 
- Write number of bytes to a memory region so can be used in health tests
- Check health tests with more bytes of data


entropy=$(pkcs11-tool --module /usr/lib/opensc-pkcs11.so --generate-random 32 | xxd -p | tr -d '\n')
echo "Generated Entropy: $entropy"

- Try and compile with none-elf compiler 
- Remove libraries that I don't need
- Maybe look for the specific routines in the libraries 