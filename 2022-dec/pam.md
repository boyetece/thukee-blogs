# Pluggable Authentication Module
It is an Authentication, Authorization and Accounting **`(AAA)`** system built before Linux came along. Features such as mounting devices, enable and disable services, create hoem directory ( except for RedHat as they implement their own module for it ), most of all application can use PAM modules to authenticate to the target server and many more implementation.
Although this blog would not be a detailed, I leave a source link from ***`Michael Lucas-PAM Mastery`***   book.

## Directories, configuration files and Libraries:
**`Configuration Files:`**
```
/etc/pam.conf
/etc/pam.d/{included_custom_directives}
```
**`Libraries involved:`**
```
/usr/lib -> older 32bit system
/usr/lib64 -> newer 64bit system
```

### **`Trivia:`** - ***`usr`***  directory does not mean ***`user`***, it means **`unix system resources`**. ###



[**`Micheal W. Lucas - PAM Mastery book`**](https://www.tiltedwindmillpress.com/product/pam/)