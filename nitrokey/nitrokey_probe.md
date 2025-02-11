# Nitrokey instructions 

## Determing which nitrokey is which 

- Install the opensc package (debian)
```
sudo apt-get install opensc
```

- Start the pcsc deamon
```
pcscd
```

- List the nitrokeys 
```
pkcs11-tool --list-slots
```

## Resetting nitrokey back to factory settings

- Change identity to 0

```
gpg-connect-agent --hex "scd apdu  00 85 00 00" /bye
```

- Restore communication to the card

```
gpg --card-status
```

- Confirm the change to identity by locating the 10th byte of the application ID, it should be FE:
<pre>
Reader ...........: 20A0:4211:FSIJ-1.2.19-7AF10C50:0
Application ID ...: D276000124010200FF<b>FE</b>7AF10C500000
Application type .: OpenPGP
Version ..........: 2.0
Manufacturer .....: unmanaged S/N range
Serial number ....: 7AF10C50
Name of cardholder: [not set]
Language prefs ...: [not set]
Salutation .......:
URL of public key : [not set]
Login data .......: [not set]
Signature PIN ....: forced
Key attributes ...: rsa2048 rsa2048 rsa2048
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 3 3
Signature counter : 0
KDF setting ......: off
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]
<pre>

- Reset card back to factory settings

```
gpg --card-edit` -> admin -> factory-reset
```

- Change identity to 1 

```
gpg-connect-agent --hex "scd apdu  00 85 00 01" /bye
```

- Restore communication to the car

```
gpg --card-status
```

- Confirm the change to identity by locating the 10th byte of the application ID, it should be 01:

```
Reader ...........: Nitrokey Nitrokey Start (FSIJ-1.2.19-7AF10C50) 00 00
Application ID ...: D276000124010200FF**01**7AF10C500000
Application type .: OpenPGP
Version ..........: 2.0
Manufacturer .....: unmanaged S/N range
Serial number ....: 7AF10C50
Name of cardholder: [not set]
Language prefs ...: [not set]
Salutation .......:
URL of public key : [not set]
Login data .......: [not set]
Signature PIN ....: forced
Key attributes ...: rsa2048 rsa2048 rsa2048
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 3 3
Signature counter : 0
KDF setting ......: off
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]

```

- Reset card back to factory settings

```
gpg --card-edit` -> admin -> factory-reset
```

- Change identity to 2

```
gpg-connect-agent --hex "scd apdu  00 85 00 02" /bye
```

- Restore communication to the card

```
gpg --card-status
```

- Confirm the change to identity by locating the 10th byte of the application ID, it should be 02:
```
Reader ...........: Nitrokey Nitrokey Start (FSIJ-1.2.19-7AF10C50) 00 00
Application ID ...: D276000124010200FF**02**7AF10C500000
Application type .: OpenPGP
Version ..........: 2.0
Manufacturer .....: unmanaged S/N range
Serial number ....: 7AF10C50
Name of cardholder: [not set]
Language prefs ...: [not set]
Salutation .......:
URL of public key : [not set]
Login data .......: [not set]
Signature PIN ....: forced
Key attributes ...: rsa2048 rsa2048 rsa2048
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 3 3
Signature counter : 0
KDF setting ......: off
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]
```

- Reset card back to factory settings

```
gpg --card-edit` -> admin -> factory-reset
```

## Configuring HSM's

- Followed the instructions in: https://docs.nitrokey.com/nitrokeys/features/openpgp-card/openpgp-keygen-on-device

### HSM1

#### Generating keys
```
gpg –card-edit -> admin -> generate
```

- Key name: Apricot HSM1

#### Change pins

- Enter passwd settings
```
gpg –card-edit -> admin -> passwd
```

- Select 3 for admin pin

- Change admin pin to: 1111111199999999 (8 '1s' then 8 '9s')

- Select 1 for user pin

- Change user pin to: 1111111155555555 (8 '1s' then 8 '5s')

### HSM3

#### Generating keys
```
gpg –card-edit -> admin -> generate
```

- Key name: Apricot HSM3

#### Change pins

- Enter passwd settings
```
gpg –card-edit -> admin -> passwd
```

- Select 3 for admin pin

- Change admin pin to: 3333333399999999 (8 '3's then 8 '9's)

- Select 1 for user pin

- Change user pin to: 3333333355555555 (8 3's then 8 5s)

## Helpful commands

- Kill gpg agent
```
gpgconf -kill gpg-agent
```
