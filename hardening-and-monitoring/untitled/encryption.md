# Encryption

## <mark style="color:red;">LUKS</mark>

{% hint style="info" %}
You can implement LUKS only before system installation because LUKS is an FDE solution and your file system will be implemented on top of it, so there is no way to setup LUKS on a system with installed OS.
{% endhint %}

create a linux partition:

```
fdisk /dev/sdb1
cryptsetup --help
```

create an encryption layer on top of /dev/sdb1

```
cryptsetup luksFormat /dev/sdb1 
```

open the device with the given name in /dev/mapper

```
cryptsetup luksOpen /dev/sdb1 [name] 
```

```
cd /dev/mapper

mkfs.ext4 /dev/mapper/[name]

mkdir /secrey 

mount /dev/mapper/secret /secret

cryptset luksClose /dev/mapper/secret
```

for automatic mount of the encrypted device we have to create a config file and an entry in /etc/fstab:

```
dd if=/dev/urandom of=/root/lukskey bs=4096 count=1

cryptsetup luksAddkey /dev/sdb1 /root/lukskey
```

nano `/etc/crypttab` add following:

```
secret  /dev/sdb1   /root/lukskey
```

`nano /etc/fstab` add following:

```
/dev/mapper/secret      /secret ext4    defaults    1   2
```

reboot

## <mark style="color:red;">GNU Privacy Guard (GPG)</mark>

create key pair:

```
gpg --gen-key
```

list keys:

```
gpg --list-keys
ls -l .gnupg
```

### <mark style="color:orange;">Symmetrically Encrypting Files</mark>

```
gpg -c file.txt
```

the -c option indicates that I chose to use symmetric encryption with a passphrase for the file. The passphrase that you enter will be for the file, not for your private key.

decryption:

```
gpg -d file.txt
```

### <mark style="color:orange;">Encrypting Files with Public Keys</mark>

after creating key pairs on both ends, extract your own public keys into an ASCII text file.

```
cd .gnupg
gpg --export -a -o donnie_public-key.txt
```

do this on both ends

export pubkeys

```
gpg --import pub.txt
```

Normally, the participants in this would send their keys to each other either through an email attachment or by placing the keys in a shared directory place these pub keys into your respective .gnupg directories. Once that's done, import each other's keys.

encrypt your file:

```
gpg -s -e secret.txt
```

send the file

decrypt and open the file on the other side:

```
gpg -d secret.txt.gpg
```

to add the pub key to the trusted keys:

```
cd .gnupg
gpg --edit-key [username]
gpg> trust
```

### <mark style="color:orange;">Signing a File Without Encryption</mark>

If a file isn't secret but you still need to ensure authenticity and integrity, you can just sign it without encrypting it the pub key exchange should be done before doing this:

Create an unencrypted message and sign it:

```
gpg -s secret.txt
```

Send the message, try to open it with less:

```
less secret.txt.gpg
```

There's a lot of gibberish there because of the signature, but if you look carefully,you'll see the plain, unencrypted message.

use gpg with the --verify option to verify that the signature really does belong to you:

```
gpg --verify secret.txt.gpg
```

## <mark style="color:red;">eCryptfs</mark>

{% hint style="info" %}
Red Hat and CentOS no longer include eCryptfs in either version 7 or 8 of their products.
{% endhint %}

### <mark style="color:orange;">Encrypting a Home Directory for a New User Account</mark>

install the ecryptfs-utils package:

```
sudo apt install ecryptfs-utils
```

create Goldie's account with an encrypted directory:

```
sudo adduser --encrypt-home goldie
```

Have Goldie log in. Have her unwrap her mount passphrase, write it down, and store it in a secure place. She'll need it if she ever needs to recover a corrupted directory:

```
ecryptfs-unwrap-passphrase .ecryptfs/wrapped-passphrase
```

### <mark style="color:orange;">Creating a Private Directory Within an Existing Home Directory</mark>

<mark style="color:orange;">I</mark>nstead of encrypting an entire home directory, any user can create an encrypted private directory within his or her own home directory

```
ecryptfs-setup-private
```

### <mark style="color:orange;">Encrypting the swap Partition with ecryptfs</mark>

```
sudo ecryptfs-setup-swap
```

Don't mind the warning about the missing /dev/mapper/cryptswap1 file. It will get created the next time you reboot the machine.

## <mark style="color:red;">VeraCrypt (cross-platform)</mark>

VeraCrypt is the successor to TrueCrypt, and it allows the sharing of encrypted containers across Linux, Windows, macOS, and FreeBSD machines. Although LUKS and eCryptfs are good, VeraCrypt does offer more flexibility in certain ways:

As mentioned, VeraCrypt offers cross-platform sharing, whereas LUKS and eCryptfs don't.

VeraCrypt allows you to encrypt either whole partitions or whole storage devices, or to create virtual encrypted disks.

Not only can you create encrypted volumes with VeraCrypt, you can also hide them, giving you plausible deniability.

VeraCrypt comes in both command-line and GUI variants, so it's appropriate for either server use or for the casual desktop user.

Like LUKS and eCryptfs, VeraCrypt is free open source software, which means that it's free to use, and that the source code can be audited for either bugs or backdoors.

Download VeraCrypt from here:

{% embed url="https://www.veracrypt.fr/en/Downloads.html" %}

```
dpkg -i [package name]
```

### <mark style="color:orange;">creating and mounting a VeraCrypt volume in console mode</mark>

To create a new encrypted volume

```
veracrypt -c
```

This will take you into an easy-to-use interactive utility. For the most part, you'll be fine just accepting the default options

Mount this container in order to use it. Begin by creating a mount point directory

```
mkdir good_stuff_dir
veracrypt good_stuff good_stuff_dir
```

To see what VeraCrypt volumes you have mounted

```
veracrypt -l
```

## <mark style="color:red;">OpenSSL</mark>

With OpenSSL, we can encrypt information on the fly as it goes across the network. There's no need to manually encrypt our data before we send it across the network because OpenSSL encryption happens automatically. This is important because online commerce and banking couldn't exist without it.

generating rsa key:

```
openssl genrsa -aes256 -out example.pem 2048
```

see the key structure:

```
openssl rsa -text -in example.pem
```

separate public key from key file:

```
openssl rsa -in example.pem -pubout -out public.pem
```

generate DSA key

```
openssl dsaparam -genkey 2048 | openssl dsa -out dsa.key -aes256
```

generate an ECDSA key :

```
// Sopenssl ecparam -genkey -name secp256r1 | openssl ec -out ec.key -aes256
```

OpenSSL supports many named curves (you can get a full list with the -list\_curves switch), but, for web server keys, you’re limited to only two curves that are supported by all major browsers: secp256r1 (OpenSSL uses the name prime256v1 ) and secp384r1 .

Creating Certificate Signing Requests

```
openssl -req -key -new example.pem -out example.csr
```

check the CSR:

```
openssl -req -text -in example.csr -noout
```

generate a public and private key pair:

```
 openssl genrsa -des3 -out private.pem 2048
```

That generates a 2048-bit RSA key pair, encrypts them with a password you provide and writes them to a file. You need to next extract the public key file. You will use this, for instance, on your web server to encrypt content so that it can only be read with the private key.

#### create a 2048-bit private key (domain.key) and a CSR (domain.csr) from scratch:

```
 openssl req  -newkey rsa:2048 -nodes -keyout domain.key  -out domain.csr
```

Generate a CSR from an Existing Private Key

```
 openssl req  -key domain.key  -new -out domain.csr
```

Generate a Self-Signed Certificate

```
openssl req  -newkey rsa:2048 -nodes -keyout domain.key  -x509 -days 365 -out domain.crt
```

Generate a Self-Signed Certificate from an Existing Private Key

```
 openssl req  -key domain.key  -new  -x509 -days 365 -out domain.crt
```

Generate a Self-Signed Certificate from an Existing Private Key and CSR

```
 openssl x509  -signkey domain.key  -in domain.csr -req -days 365 -out domain.crt
```

View Certificate Entries

```
openssl x509 -text -noout -in domain.crt
```

Verify a Certificate was Signed by a CA

```
openssl verify -verbose -CAFile ca.crt domain.crt
```

Create a Private Key

```
openssl genrsa -des3 -out domain.key 2048
```

Verify a Private Key

```
openssl rsa -check -in domain.key
```

Verify a Private Key Matches a Certificate and CSR

```
openssl rsa -noout -modulus -in domain.key | openssl md5
openssl x509 -noout -modulus -in domain.crt | openssl md5
openssl req -noout -modulus -in domain.csr | openssl md5
```

Encrypt a Private Key

```
/ openssl rsa -des3 -in unencrypted.key  -out encrypted.key
```
