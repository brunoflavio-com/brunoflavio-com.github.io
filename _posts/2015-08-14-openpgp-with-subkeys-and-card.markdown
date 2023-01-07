---
layout: post
title:  "OpenPGP with subkeys and card"
date:   2015-08-01 00:00:00 +0100
categories: blog security
image: "/assets/img/card.png"
image-alt: "id card"
---

This document shows the steps I’ve taken to create my OpenPGP Keys and how to store the master Key in a secure medium (encrypted USB disk) and the subkeys that I’ll frequently use on a OpenPGP card.

![Card]({{page.image}} "{{page.image-alt}}")

This tutorial was created using gpg version 1.4.18 on Debian Jessie.

## Setup OpenPGP to prefer SHA2:

[Debian recommends][debian-create-key] the use of 4096bit RSA keys using SHA2 hash functions. 

I’ve changed the GnuPG configuration file, `~/.gnupg/gpg.conf`, in accordance:

```shell
utf8-strings
keyserver hkp://keys.gnupg.net
fixed-list-mode
keyid-format 0xlong

cert-digest-algo SHA256
personal-digest-preferences SHA512 SHA384 SHA256 SHA224
default-preference-list SHA512 SHA384 SHA256 SHA224 AES256 AES192 AES CAST5 BZIP2 ZLIB ZIP Uncompressed

use-agent
```

## OpenPGP Keys generation

Start by creating the master key with the `gpg --gen-key` command. In this example the master key won’t have an expiration date set, and subkeys will be valid for five years. Additionally your master key should have a strong password, this tutorial omitted one for the sake of simplicity.

### Master key:

Here’s the process of creating a key:

```
:~$ gpg --gen-key
gpg (GnuPG) 1.4.18; Copyright (C) 2014 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection? 4
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 
Key does not expire at all
Is this correct? (y/N) y

You need a user ID to identify your key; the software constructs the user ID
from the Real Name, Comment and Email Address in this form:
    "Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"

Real name: Jane Doe
Email address: jane.doe@brunoflavio.com
Comment: 
You selected this USER-ID:
    "Jane Doe <jane.doe@brunoflavio.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o
You need a Passphrase to protect your secret key.

You don't want a passphrase - this is probably a *bad* idea!
I will do it anyway.  You can change your passphrase at any time,
using this program with the option "--edit-key".

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
...+++++
...+++++
gpg: key 0x408861B75EEA201F marked as ultimately trusted
public and secret key created and signed.

gpg: checking the trustdb
gpg: public key of ultimately trusted key 0x273DBB68C1EC3DB1 not found
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   3  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 3u
pub   4096R/0x408861B75EEA201F 2014-09-20
      Key fingerprint = 02AE 3718 68C9 8B5A C45A  9AEE 4088 61B7 5EEA 201F
uid                 [ultimate] Jane Doe <jane.doe@brunoflavio.com>

Note that this key cannot be used for encryption.  You may want to use
the command "--edit-key" to generate a subkey for this purpose.
```

### Encryption and signature subkeys

The master key you’ve just created should be used solely to sign other keys. For everyday use we’ll create subkeys for encryption and signing.

We proceed to the creation of the encryption subkey:

```
:~$ gpg --edit-key 0x408861B75EEA201F
gpg (GnuPG) 1.4.18; Copyright (C) 2014 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

pub  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never       usage: SC  
                               trust: ultimate      validity: ultimate
[ultimate] (1). Jane Doe <jane.doe@brunoflavio.com>

gpg> addkey
You need a passphrase to unlock the secret key for 
user: "Jane Doe <jane.doe@brunoflavio.com>"
4096-bit RSA key, ID 0xAD6DB00434A52FD5, created 2014-09-20

Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
Your selection? 6
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 5y
Key expires at Thu 19 Sep 2019 12:49:19 WEST
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
...+++++
........................+++++

pub  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never       usage: SC  
                               trust: ultimate      validity: ultimate
sub  4096R/0xAD6DB00434A52FD5  created: 2014-09-20  expires: 2019-09-19  usage: E   
[ultimate] (1). Jane Doe <jane.doe@brunoflavio.com>

gpg> addkey
You need a passphrase to unlock the secret key for 
user: "Jane Doe <jane.doe@brunoflavio.com>"
4096-bit RSA key, ID 0xAD6DB00434A52FD5, created 2014-09-20

Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
Your selection? 4
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 5y
Key expires at Thu 19 Sep 2019 12:49:46 WEST
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
...+++++
......+++++

pub  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never       usage: SC  
                               trust: ultimate      validity: ultimate
sub  4096R/0xAD6DB00434A52FD5  created: 2014-09-20  expires: 2019-09-19  usage: E   
sub  4096R/0x010005374BB8FE68  created: 2014-09-20  expires: 2019-09-19  usage: S   
[ultimate] (1). Jane Doe <jane.doe@brunoflavio.com>

gpg> save
```

### Authentication subkey:

To create an authentication subkey we have to create it using gpg expert mode.

I created a 4096 authentication key. If you plan to use it with the OpenPGP card keep in mind that you will need gpg2.

```
:~$ gpg --expert --edit-key 0x408861B75EEA201F 
gpg (GnuPG) 1.4.18; Copyright (C) 2014 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

pub  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never       usage: SC  
                               trust: ultimate      validity: ultimate
sub  4096R/0xAD6DB00434A52FD5  created: 2014-09-20  expires: 2019-09-19  usage: E   
sub  4096R/0x010005374BB8FE68  created: 2014-09-20  expires: 2019-09-19  usage: S   
[ultimate] (1). Jane Doe <jane.doe@brunoflavio.com>

gpg> addkey
You need a passphrase to unlock the secret key for 
user: "Jane Doe <jane.doe@brunoflavio.com>"
4096-bit RSA key, ID 0xAD6DB00434A52FD5, created 2014-09-20

Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
Your selection? 8

Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Sign Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? s

Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? e

Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? a

Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Authenticate 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? q
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 5y
Key expires at Thu 19 Sep 2019 12:50:10 WEST
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
......................+++++
+++++

pub  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never       usage: SC  
                               trust: ultimate      validity: ultimate
sub  4096R/0xAD6DB00434A52FD5  created: 2014-09-20  expires: 2019-09-19  usage: E   
sub  4096R/0x010005374BB8FE68  created: 2014-09-20  expires: 2019-09-19  usage: S   
sub  4096R/0xBD360B86E03F3D7A  created: 2014-09-20  expires: 2019-09-19  usage: A   
[ultimate] (1). Jane Doe <jane.doe@brunoflavio.com>

gpg> save
```

## Storing the key in a secure USB disk together with the revocation certificate:

The next step is the creation of a secure place to keep your master key. I am using a SDCard for this with an encrypted directory. You’ll need this disk whenever you want to sign other keys or change your master key.

### Creating the encrypted storage:

In order to create the USB encrypted disk I followed the Ubuntu’s [Storing GPG Keys on an Encrypted USB Flash Drive][GPGKeyOnUSBDrive] guide. Follow their instructions there and thoroughly test the mount/unmount procedure.

### Backup your keyring and create a revocation certificate:

At this point you may backup your keyring and GnuPG and settings to the encrypted USB disk.

We’ll assume you have plugged in and mounted your USB disk and that your encrypted folder is on the following location: /media/janedoe/uuid/encrypted (don’t forget to use mount.sh before proceeding).

Copy the GnuPG settings directory to your encrypted disk:

```
:~$ cp -R ~/.gnupg /media/janedoe/uuid/encrypted/ -R
:~$ chmod 700 /media/janedoe/uuid/encrypted/.gnupg
```

Make sure that your public and secret keys are in the expected location:
```
:~$ gpg --home=/media/janedoe/uuid/encrypted/.gnupg -k /media/janedoe/uuid/encrypted/.gnupg/pubring.gpg
-----------------------------------------------------------------------------------
pub   4096R/0x408861B75EEA201F 2014-09-20
uid                 [ultimate] Jane Doe <jane.doe@brunoflavio.com>
sub   4096R/0xAD6DB00434A52FD5 2014-09-20 [expires: 2019-09-19]
sub   4096R/0x010005374BB8FE68 2014-09-20 [expires: 2019-09-19]
sub   4096R/0xBD360B86E03F3D7A 2014-09-20 [expires: 2019-09-19]
```


```
:~$ gpg --home=/media/janedoe/uuid/encrypted/.gnupg -K /media/janedoe/uuid/encrypted/.gnupg/secring.gpg
-----------------------------------------------------------------------------------
sec   4096R/0x408861B75EEA201F 2014-09-20
uid                            Jane Doe <jane.doe@brunoflavio.com>
ssb   4096R/0xAD6DB00434A52FD5 2014-09-20
ssb   4096R/0x010005374BB8FE68 2014-09-20
ssb   4096R/0xBD360B86E03F3D7A 2014-09-20
```

You may now create a revocation certificate and store it within your encrypted USB disk:

```
:~$ gpg --home=/media/janedoe/uuid/encrypted/.gnupg --output /media/janedoe/uuid/encrypted/.gnupg/0x408861B75EEA201F.gpg-revocation-certificate --armour --gen-revoke 0x408861B75EEA201F

sec  4096R/0x408861B75EEA201F 2014-09-20 Jane Doe <jane.doe@brunoflavio.com>

Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here)
Your decision? 1
Enter an optional description; end it with an empty line:
> This revocation certificate was generated at the time of the key creation.
> 
Reason for revocation: Key has been compromised
This revocation certificate was generated at the time of the key creation.
Is this okay? (y/N) y
NOTE: This key is not protected!
Revocation certificate created.

Please move it to a medium which you can hide away; if Mallory gets
access to this certificate he can use it to make your key unusable.
It is smart to print this certificate and store it away, just in case
your media become unreadable.  But have some caution:  The print system of
your machine might store the data and make it available to others!
```

## Remove your master key from your user’s keyring:

Now that you have a backup of your key you should be ready to remove the master key from your keyring. The original will stay in the USB encrypted disk.

There are 4 steps in this process:
* Export your secret subkeys to a file;
* Remove all your secret keys;
* Import the secret subkeys back, leaving the secret master key behind;
* Remove the subkeys file you’ve exported.

```
:~$ gpg --export-secret-subkeys 0x408861B75EEA201F > ~/.gnupg/subkeys

:~$ gpg --delete-secret-keys 0x408861B75EEA201F 
gpg (GnuPG) 1.4.18; Copyright (C) 2014 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.


sec  4096R/0x408861B75EEA201F 2014-09-20 Jane Doe <jane.doe@brunoflavio.com>

Delete this key from the keyring? (y/N) y
This is a secret key! - really delete? (y/N) y

:~$ gpg --import ~/.gnupg/subkeys 
gpg: key 0x408861B75EEA201F: secret key imported
gpg: key 0x408861B75EEA201F: "Jane Doe <jane.doe@brunoflavio.com>" not changed
gpg: Total number processed: 1
gpg:              unchanged: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1

:~$ shred -u ~/.gnupg/subkeys 
```

You may now confirm that your keyring is missing the secret master key:

```
:~$ gpg -K /home/jane/.gnupg/secring.gpg
---------------------------------
sec#  4096R/0x408861B75EEA201F 2014-09-20
uid                            Jane Doe <jane.doe@brunoflavio.com>
ssb   4096R/0xAD6DB00434A52FD5 2014-09-20
ssb   4096R/0x010005374BB8FE68 2014-09-20
ssb   4096R/0xBD360B86E03F3D7A 2014-09-20
```

The ‘#’ symbol confirms that the secret key is absent.

## Moving your secret subkeys into the OpenPGP card:

If you have a OpenPGP card you probably want to move your secret subkeys into it, removing all the secret keys from your computer and further improving security.
Initializing the OpenPGP card:

Use `gpg --card-edit` to initialize your card:

```
:~$ gpg --card-edit 

Application ID ...: 00000000000000000000000000000000
Version ..........: 2.0
Manufacturer .....: ZeitControl
Serial number ....: 00000000
Name of cardholder: [not set]
Language prefs ...: de
Sex ..............: unspecified
URL of public key : [not set]
Login data .......: [not set]
Private DO 1 .....: [not set]
Private DO 2 .....: [not set]
Signature PIN ....: forced
Key attributes ...: 2048R 2048R 2048R
Max. PIN lengths .: 32 32 32
PIN retry counter : 3 0 3
Signature counter : 0
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]

gpg/card> admin
Admin commands are allowed

gpg/card> help
quit       quit this menu
admin      show admin commands
help       show this help
list       list all available data
name       change card holder's name
url        change URL to retrieve key
fetch      fetch the key specified in the card URL
login      change the login name
lang       change the language preferences
sex        change card holder's sex
cafpr      change a CA fingerprint
forcesig   toggle the signature force PIN flag
generate   generate new keys
passwd     menu to change or unblock the PIN
verify     verify the PIN and list all data
unblock    unblock the PIN using a Reset Code

gpg/card> 
```

To use name, sex and the other commands to personalize your card you need to enter the admin mode.
You may also change your card admin PIN with the passwd command:

```
gpg/card> admin
Admin commands are allowed

gpg/card> name
Cardholder's surname: Doe
Cardholder's given name: Jane

gpg/card> sex
Sex ((M)ale, (F)emale or space): F

gpg/card> quit
```

### Move your secret subkeys to the card:

To move your keys to the card you’ll use the keytocard command when editing your key:

```
:~$ gpg --edit-key 0x408861B75EEA201F
gpg (GnuPG) 1.4.18; Copyright (C) 2014 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

pub  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never       usage: SC  
                               trust: ultimate      validity: ultimate
sub  4096R/0xAD6DB00434A52FD5  created: 2014-09-20  expires: 2019-09-19  usage: E   
sub  4096R/0x010005374BB8FE68  created: 2014-09-20  expires: 2019-09-19  usage: S   
sub  4096R/0xBD360B86E03F3D7A  created: 2014-09-20  expires: 2019-09-19  usage: A   
[ultimate] (1). Jane Doe <jane.doe@brunoflavio.com>
```

To select your subkeys you will need to use their number, you can determine it’s usage by looking at the “usage” field:
* key 1: Encryption key;
* key 2: Signing key;
* key 3: Authentication key.

The toggle command switches the interface to manage secret keys.

Subkeys have to be moved to the card one at a time. To select a subkey use the key command followed by the key number. The keys currently selected will have a ‘*’ next to it.

Let’s start by moving the Encryption subkey to the card:

```
gpg> toggle

sec  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never     
ssb  4096R/0xAD6DB00434A52FD5  created: 2014-09-20  expires: never     
ssb  4096R/0x010005374BB8FE68  created: 2014-09-20  expires: never     
ssb  4096R/0xBD360B86E03F3D7A  created: 2014-09-20  expires: never     
(1)  Jane Doe <jane.doe@brunoflavio.com>

gpg> key 1

sec  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never     
ssb* 4096R/0xAD6DB00434A52FD5  created: 2014-09-20  expires: never     
ssb  4096R/0x010005374BB8FE68  created: 2014-09-20  expires: never     
ssb  4096R/0xBD360B86E03F3D7A  created: 2014-09-20  expires: never     
(1)  Jane Doe <jane.doe@brunoflavio.com>

gpg> keytocard
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]

Please select where to store the key:
   (2) Encryption key
Your selection? 2

You need a passphrase to unlock the secret key for
user: "Jane Doe <jane.doe@brunoflavio.com>"
4096-bit RSA key, ID 0xAD6DB00434A52FD5, created 2014-09-20


sec  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never     
ssb* 4096R/0xAD6DB00434A52FD5  created: 2014-09-20  expires: never     
                     card-no: 0005 0000285F
ssb  4096R/0x010005374BB8FE68  created: 2014-09-20  expires: never     
ssb  4096R/0xBD360B86E03F3D7A  created: 2014-09-20  expires: never     
(1)  Jane Doe <jane.doe@brunoflavio.com>
```

Your encryption subkey has now been moved to the card.

The interface allows the selection of multiple subkeys simultaneously, so you’ll have to deselect the previous key before switching to the next.

We will now transfer the second subkey, the signing secret key:

```
gpg> key 2

sec  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never     
ssb  4096R/0xAD6DB00434A52FD5  created: 2014-09-20  expires: never     
                     card-no: 0005 0000285F
ssb* 4096R/0x010005374BB8FE68  created: 2014-09-20  expires: never     
ssb  4096R/0xBD360B86E03F3D7A  created: 2014-09-20  expires: never     
(1)  Jane Doe <jane.doe@brunoflavio.com>

gpg> keytocard
Signature key ....: [none]
Encryption key....: 4A59 741C 3037 79E4 5E22  6DC0 AD6D B004 34A5 2FD5
Authentication key: [none]

Please select where to store the key:
   (1) Signature key
   (3) Authentication key
Your selection? 1

You need a passphrase to unlock the secret key for
user: "Jane Doe <jane.doe@brunoflavio.com>"
4096-bit RSA key, ID 0x010005374BB8FE68, created 2014-09-20


sec  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never     
ssb  4096R/0xAD6DB00434A52FD5  created: 2014-09-20  expires: never     
                     card-no: 0005 0000285F
ssb* 4096R/0x010005374BB8FE68  created: 2014-09-20  expires: never     
                     card-no: 0005 0000285F
ssb  4096R/0xBD360B86E03F3D7A  created: 2014-09-20  expires: never     
(1)  Jane Doe <jane.doe@brunoflavio.com>
```

Finally, let’s transfer the third subkey, the authentication one:

```
gpg> key 3

sec  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never     
ssb  4096R/0xAD6DB00434A52FD5  created: 2014-09-20  expires: never     
                     card-no: 0005 0000285F
ssb  4096R/0x010005374BB8FE68  created: 2014-09-20  expires: never     
                     card-no: 0005 0000285F
ssb* 4096R/0xBD360B86E03F3D7A  created: 2014-09-20  expires: never     
(1)  Jane Doe <jane.doe@brunoflavio.com>

gpg> keytocard
Signature key ....: 0076 D240 0C1D 7710 4060  6BA9 0100 0537 4BB8 FE68
Encryption key....: 4A59 741C 3037 79E4 5E22  6DC0 AD6D B004 34A5 2FD5
Authentication key: [none]

Please select where to store the key:
   (3) Authentication key
Your selection? 

gpg> keytocard
Signature key ....: 0076 D240 0C1D 7710 4060  6BA9 0100 0537 4BB8 FE68
Encryption key....: 4A59 741C 3037 79E4 5E22  6DC0 AD6D B004 34A5 2FD5
Authentication key: [none]

Please select where to store the key:
   (3) Authentication key
Your selection? 3

You need a passphrase to unlock the secret key for
user: "Jane Doe <jane.doe@brunoflavio.com>"
4096-bit RSA key, ID 0xBD360B86E03F3D7A, created 2014-09-20


sec  4096R/0x408861B75EEA201F  created: 2014-09-20  expires: never     
ssb  4096R/0xAD6DB00434A52FD5  created: 2014-09-20  expires: never     
                     card-no: 0005 0000285F
ssb  4096R/0x010005374BB8FE68  created: 2014-09-20  expires: never     
                     card-no: 0005 0000285F
ssb* 4096R/0xBD360B86E03F3D7A  created: 2014-09-20  expires: never     
                     card-no: 0005 0000285F
(1)  Jane Doe <jane.doe@brunoflavio.com>
```

With all the keys on the card you may now save.
If you list your secret keys you’ll now find that your secret subkeys are marked with a ‘>’ indicating that they are no longer stored in your keyring:


```
gpg> save
:~$ gpg -K
/home/jane/.gnupg/secring.gpg
---------------------------------
sec#  4096R/0x408861B75EEA201F 2014-09-20
uid                            Jane Doe <jane.doe@brunoflavio.com>
ssb>  4096R/0xAD6DB00434A52FD5 2014-09-20
ssb>  4096R/0x010005374BB8FE68 2014-09-20
ssb>  4096R/0xBD360B86E03F3D7A 2014-09-20
```

## Wrapping up

If all these steps were successfully completed you should now have a USB encrypted disk with your secret master key and secret subkeys.

If you transferred your secret subkeys to a OpenPGP card you now have a computer without any keys to be lost or stolen.

### Test everything:

If all is well you should be able to encrypt/sign a file with the subkeys stored on the card and then decrypt with your master key USB device.

### Share your public key:

When you’re satisfied that everything is ok and that you are able to recover the system you may consider sending your public key to a pgp key server. Please note that this action will be permanent and that your uid (user and email address) will be publicly available:

`gpg --keyserver subkeys.pgp.net --send-key 0x408861B75EEA201F`

### Take care of your master key:

Whenever you need to change your master key or sign other person keys you’ll need your usb token.

*The USB encrypted disk could become corrupted or lost*, you should account for this by:

    creating multiple encrypted USB disks (make sure you won’t forget the encrypted volume password);
    printing your revocation certificate;
    (maybe burning a CD with your key and revocation certificate, unencrypted?).
    Finally, don’t store all this in the same place, of course.

## References

  *  [http://keyring.debian.org/creating-key.html][debian-create-key]  
  *  [https://www.void.gr/kargig/blog/2013/12/02/creating-a-new-gpg-key-with-subkeys/][ref-1]
  *  [https://wiki.debian.org/Smartcards/OpenPGP][ref-2]
  *  [https://inuits.eu/blog/ssh-authentication-your-pgp-key][ref-3]




[debian-create-key]: https://keyring.debian.org/creating-key.html
[GPGKeyOnUSBDrive]: https://help.ubuntu.com/community/GPGKeyOnUSBDrive
[ref-1]: https://www.void.gr/kargig/blog/2013/12/02/creating-a-new-gpg-key-with-subkeys/
[ref-2]: https://wiki.debian.org/Smartcards/OpenPGP
[ref-3]: https://inuits.eu/blog/ssh-authentication-your-pgp-key