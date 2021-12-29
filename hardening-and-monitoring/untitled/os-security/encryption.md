# Encryption

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

