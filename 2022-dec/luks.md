# LUKS - Linux Unified Key Setup

Is a disk encryption specification created by Clemens Fruhwirth in 2004 and was originally intended for Linux. It usually encrypt block devices with some encyption specification type such as Cipher Algorithm of `twofish`, `aes` and Cipher mode such as `cbc-essiv:sha256` and others using some kind of `PGP` private keys of `X509` certifiate.

While most disk encryption software implements different, incompatible, and undocumented formats[citation needed], LUKS implements a platform-independent standard on-disk format for use in various tools. This not only facilitates compatibility and interoperability among different programs, but also assures that they all implement password management in a secure and documented manner.

Block devices includes usb, 
# Examples

Cryptsetup is the reference implementation of the LUKS frontend.

To encrypt a device with the path /dev/sda1:
```
$ cryptsetup luksFormat /dev/sda1
```
To unlock an encrypted device, where name is the mapped device `name`:
```
$ cryptsetup luksOpen /dev/sda1 `name`
```
To check for details on the encryption and cipher:
```
$ scrypsetup dumpluks `name`
```
To lock an encrypted device, after using it.
```
$ cryptsetup close `name`
```
