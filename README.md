# nvidia-shield-portable
Awesome hacks for the Nvidia Shield Portable

## Flash TWRP

### Download TWRP

#### Download community TWRP roth image
The original instructions can be found on XDA Developers [here](https://forum.xda-developers.com/nvidia-shield/development/recovery-twrp-2-8-5-0-t3042190), but that thread now suggests to go [here](https://forum.xda-developers.com/nvidia-shield/development/mod-multirom-shield-portable-t3090177) which provides a link to TWRP source [here](https://github.com/Tasssadar/Team-Win-Recovery-Project). If you don't want to build it yourself, you can download a build [here](https://www.androidfilehost.com/?fid=24566382913912866). (MD5 checksum: `ad5d6e5561d6b70b15ced6555992d632`)

#### Download official TWRP roth image
You could also download one of the official TWRP roth images [here](https://dl.twrp.me/roth/) which also come with nifty GPG signatures.
``` bash
$ gpg --verify twrp-3.2.3-0-roth.img.asc twrp-3.2.3-0-roth.img
gpg: Signature made Mon, Aug  6, 2018  9:08:53 PM MDT using RSA key ID 891A43DF
gpg: Good signature from "TeamWin <admin@teamw.in>"
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 9570 7D42 307C 9D41 D09B  F709 1D85 97D7 891A 43DF
```
If you get this message instead:
``` bash
gpg: Signature made Mon, Aug  6, 2018  9:08:53 PM MDT using RSA key ID 891A43DF
gpg: Can't check signature: public key not found
```
Then you need to download the TWRP public signature:
``` bash
$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 891A43DF
gpg: requesting key 891A43DF from hkp server keys.gnupg.net
gpg: key 891A43DF: public key "TeamWin <admin@teamw.in>" imported
gpg: no ultimately trusted keys found
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
```
Then, re-run `gpg --verify twrp-3.2.3-0-roth.img.asc twrp-3.2.3-0-roth.img`

## Install TWRP
### Enable ADB debugging
You'll need to go into _Settings_ then _About Shield_ then tap on Build number a bunch of times to enable _Developer options_ menu. Go back up one menu level and then click _Developer options_ then enable _USB debugging_ and click OK.

### Install ADB
If you haven't already, install Android debugging tools on the computer.

### Reboot Shield into bootloader
Connect your device to your computer via USB. Then, open up a command line and check the device is recognized by your computer:
``` bash
$ adb devices
List of devices attached
05116425003142FC0442    device
```
If that's all good, reboot the device into bootloader:
``` bash
$ adb reboot bootloader
```
The shield should immediately restart into a black screen with some menu options. Check Device Manager to see how the Windows recognizes the device in fastboot mode. 

### Update Shield drivers
Download the Shield Family drivers [here](http://developer.download.nvidia.com/mobile/shield/SHIELD_Family_WHQL_USB_driver_201801.zip). If it's displayed as _Fastboot_, right-click, choose _Update Driver_, _Browse my computer for driver software_, _Let me pick from a list of available drivers on my computer_, double-click _Show all devices_, then click _Have Disk..._. Browse into the driver package where `android_winusb.inf` is located. Click OK and choose _Android Bootloader Interface_. Click _Yes_ through the warning.

### Boot into bootloader menu
Back on the command line, you should see if the device is recognized in fastboot:
``` bash
$ fastboot devices
05115235003151FC42C0    fastboot
```

### Unlock bootloader
On the device, if it says _Device - locked_ at the top of the screen, you need to unlock your bootloader. On the command line, enter the command:
``` bash
fastboot oem unlock
```
The device should switch to another screen that warns you about unlocking the bootloader. Press the Home button to select _Unlock_ then press the Nvidia button to unlock. You'll be returned to the previous screen.

### Flash recovery image
Flash the TWRP image:
``` bash
$ fastboot flash recovery twrp-3.2.3-0-roth.img
target reported max download size of 643825664 bytes
sending 'recovery' (8190 KB)...
OKAY [  1.145s]
writing 'recovery'...
OKAY [  0.264s]
finished. total time: 1.409s
```
Reboot the system and do the initial setup.

### Root device
Reboot into recovery. If you have ADB debugging enabled:
``` bash
$ adb reboot recovery
```
Install SuperSU. Reboot device. Have fun.

### Backup installed programs
You might want to save Sonic and Expendable.

### Backup original system data
Reboot into recovery. If you have ADB debugging enabled:
``` bash
$ adb reboot recovery
```
Once TWRP has loaded, go to Backup. To be continued...

### Flase official system update
Download update from [here](https://developer.nvidia.com/gameworksdownload). You probably want either Update 110 (Android 5.0) or Update 101 (Android 4.2). Unzip the downloaded package. From a command line, boot the Shield into bootloader mode and install the images in the package.
``` sh
$ fastboot devices
05116135003151FC04C0    fastboot

James@vinyl /cygdrive/d/android/shield/nv-recovery-image-shield-portable-update110
$ fastboot flash recovery recovery.img
target reported max download size of 643825664 bytes
sending 'recovery' (7004 KB)...
OKAY [  0.981s]
writing 'recovery'...
OKAY [  0.229s]
finished. total time: 1.210s

$ fastboot flash boot boot.img
target reported max download size of 643825664 bytes
sending 'boot' (6408 KB)...
OKAY [  0.902s]
writing 'boot'...
OKAY [  0.214s]
finished. total time: 1.116s

$ fastboot flash system system.img
target reported max download size of 643825664 bytes
erasing 'system'...
OKAY [  0.064s]
sending sparse 'system' (628460 KB)...
OKAY [ 87.941s]
writing 'system'...
OKAY [ 20.544s]
sending sparse 'system' (101011 KB)...
OKAY [ 14.266s]
writing 'system'...
OKAY [  3.842s]
finished. total time: 126.659s

$ fastboot flash staging blob
target reported max download size of 643825664 bytes
sending 'staging' (4471 KB)...
OKAY [  0.633s]
writing 'staging'...
OKAY [  0.161s]
finished. total time: 0.793s

$ fastboot flash dtb tegra114-roth.dtb
target reported max download size of 643825664 bytes
sending 'dtb' (0 KB)...
OKAY [  0.022s]
writing 'dtb'...
OKAY [  0.017s]
finished. total time: 0.040s
```
