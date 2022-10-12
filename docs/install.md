# Installation

This tutorial is only for the initial installation of Magisk.

If you already have Magisk installed, it is **strongly recommended** that you upgrade directly via the Magisk app using its "Direct Install" method.

## Start

Before you start:

- This tutorial assumes you understand how to use `adb` and `fastboot`
- Your device's bootloader has to be unlocked
- If you plan to also install custom kernels, install it after Magisk

---

Download the [latest Magisk APK](https://github.com/topjohnwu/Magisk/releases) from the official releases on GitHub and install it on your device. The app displays important information about your device that is required to determine the correct install process. In the app's home screen, you should see this:

<p align="center"><img src="images/device_info.png" width="500"/></p>

Follow the steps below:

1. **Are you using a Huawei device?**
   - **Yes.** Huawei devices are not supported. Sorry.
   - **No.** Continue to next question.
2. **Does the Magisk app report that your device has ramdisk in the boot partition (`Ramdisk = Yes`)?**
   - **Yes.** Continue to next question.
   - **No.** Read the [Magisk in Recovery](#magisk-in-recovery) section before installing. The information in that section is VERY important!
3. **Are you using a Samsung device _and_ the Magisk app reports `SAR = Yes`?**
   - **Yes.** Please read the section on [Samsung (System-as-root)](#samsung-system-as-root).
   - **No.** Continue to [Patching Images](#patching-images) section.

> Note: *Unfortunately, there are cases where the bootloader of some devices (primarily Xiaomi devices) accepts ramdisk even if it should not.
> There is no automated way to detect this scenario, so the only way to know for sure is to try. Follow the instructions outlined above
> and assume that your your device's boot partition does, in fact, include ramdisk. Fortunately, as far as we know, this issue is limited to
> some Xiaomi devices, so most people can simply ignore this notification.*
 
> Note: If your device does have ramdisk in the boot partition, you _can_ also install Magisk with a [custom recovery](#custom-recovery), **but it is not recommended**.

## Patching Images

1. **Obtain the correct image file.**
    
    Does the Magisk app report that your device has ramdisk in the boot partition (`Ramdisk = Yes`)?
   - **Yes.** You need a copy of the `boot.img` image file.
   - **No.** You need a copy of the `recovery.img` image file.

   You should be able to extract the file you need from official firmware packages or your custom ROM zip (if you are using one).
   If you are still having trouble, go to [XDA-Developers](https://forum.xda-developers.com/) and look for resources, guides, discussions,
   or ask for help in your device's forum.

2. **Copy the image file from your computer to your device.**
   
   `$> adb push boot-or-recovery.img /sdcard/Download/`

3. **Use the Magisk app to patch the image file.**
   - In the Magisk app, press the blue `Install` button in the top right corner of the grey Magisk card.
   - Choose the `Select and Patch a File` option.
   - If you are patching a recovery image, make sure `Recovery Mode` is checked in options. In most cases, it should already be automatically checked.
   - Navigate to the image file copied onto the device.
   - Click `Let's go!` and wait while the Magisk app patches the stock image file.
   - The patched image file will be written to `[Internal Storage]/Download/magisk_patched_[random_strings].img`

5. **Copy the patched image file from your device to your computer.**
   
   `$> adb pull /sdcard/Download/magisk_patched_[random_strings].img`

6. **Flash the patched image to your device.** For most devices, reboot into fastboot mode and flash the patched boot or recovery image. For example:
   ```
   $> adb reboot bootloader
   $> fastboot flash boot magisk_patched-24300_G0ifL.img
   ```
   
   or
   
   ```
   $> adb reboot bootloader
   $> fastboot flash recovery magisk_patched-24300_G0ifL.img
   ```

7. **Reboot and voila! Done.**

## Uninstallation

The easiest way to uninstall Magisk is directly through the Magisk app. If you insist on using custom recoveries, rename the Magisk APK to `uninstall.zip` and flash it like any other ordinary flashable zip.

## Magisk in Recovery

If your device does not have ramdisk in boot images, Magisk has no choice but to hijack the recovery partition. For these devices, you will have to **reboot to recovery** every time you want Magisk enabled.

Since Magisk now hijacks the recovery, there is a special mechanism for you to *actually* boot into recovery mode. Each device model has its own key combo to boot into recovery, as an example for Galaxy S10 it is (Power + Bixby + Volume Up). A quick search online should easily get you this info. As soon as you press the combo and the device vibrates with a splash screen, release all buttons to boot into Magisk. If you decide to boot into the actual recovery mode, **long press volume up until you see the recovery screen**.

As a summary, after installing Magisk in recovery **(starting from power off)**:

- **(Power up normally) → (System with NO Magisk)**
- **(Recovery Key Combo) → (Splash screen) → (Release all buttons) → (System with Magisk)**
- **(Recovery Key Combo) → (Splash screen) → (Long press volume up) → (Recovery Mode)**

(Note: You **CANNOT** use custom recoveries to install or upgrade Magisk in this case!!)

## Samsung (System-as-root)

> If your Samsung device is NOT launched with Android 9.0 or higher, you are reading the wrong section.

### Before Installing Magisk

- Installing Magisk **WILL** trip KNOX
- Installing Magisk for the first time **REQUIRES** a full data wipe (this is **NOT** counting the data wipe when unlocking bootloader). Backup your data before continue.
- Download Odin (only runs on Windows) that supports your device. You can find it easily with a quick search online.

### Unlocking Bootloader

Unlocking the bootloader on modern Samsung devices have some caveats. The newly introduced `VaultKeeper` service will make the bootloader reject any unofficial partitions in some cirumstances.

- Allow bootloader unlocking in **Developer options → OEM unlocking**
- Reboot to download mode: power off your device and press the download mode key combo for your device
- Long press volume up to unlock the bootloader. **This will wipe your data and automatically reboot.**
- Go through the initial setup. Skip through all the steps since data will be wiped again in later steps. **Connect the device to Internet during the setup.**
- Enable developer options, and **confirm that the OEM unlocking option exists and is grayed out.** This means the `VaultKeeper` service has unleashed the bootloader.
- Your bootloader now accepts unofficial images in download mode

### Instructions

- Use either [samfirm.js](https://github.com/jesec/samfirm.js), [Frija](https://forum.xda-developers.com/s10-plus/how-to/tool-frija-samsung-firmware-downloader-t3910594), or [Samloader](https://forum.xda-developers.com/s10-plus/how-to/tool-samloader-samfirm-frija-replacement-t4105929) to download the latest firmware zip of your device directly from Samsung servers.
-  Unzip the firmware and copy the `AP` tar file to your device. It is normally named as `AP_[device_model_sw_ver].tar.md5`
- Press the **Install** button in the Magisk card
- If your device does **NOT** have boot ramdisk, make sure **"Recovery Mode"** is checked in options.<br>In most cases it should already be automatically checked.
- Choose **"Select and Patch a File"** in method, and select the `AP` tar file
- The Magisk app will patch the whole firmware file to `[Internal Storage]/Download/magisk_patched_[random_strings].tar`
- Copy the patched tar file to your PC with ADB:<br>
`adb pull /sdcard/Download/magisk_patched_[random_strings].tar`<br>
**DO NOT USE MTP** as it is known to corrupt large files.
- Reboot to download mode. Open Odin on your PC, and flash `magisk_patched.tar` as `AP`, together with `BL`, `CP`, and `CSC` (**NOT** `HOME_CSC` because we want to **wipe data**) from the original firmware. This could take a while (>10 mins).
- Your device should reboot automatically once Odin finished flashing. Agree to do a factory reset if asked.
- If your device does **NOT** have boot ramdisk, reboot to recovery now to enable Magisk (reason stated in [Magisk in Recovery](#magisk-in-recovery)).
- Install the latest Magisk app and launch the app. It should show a dialog asking for additional setup. Let it do its job and the app will automatically reboot your device.
- Voila! Enjoy Magisk 😃

### Upgrading the OS

Once you have rooted your Samsung device, you can no longer upgrade your Android OS through OTA. To upgrade your device's OS, you have to manually download the new firmware zip file and go through the same `AP` patching process written in the previous section. **The only difference here is in the Odin flashing step: we do NOT use the `CSC` tar, but the `HOME_CSC` tar instead as we are performing an upgrade, not the initial install**.

### Important Notes

- **Never, ever** try to restore either `boot` or `recovery` partitions back to stock! You can brick your device by doing so, and the only way to recover from this is to do a full Odin restore with data wipe.
- To upgrade your device with a new firmware, **NEVER** directly use the stock `AP` tar file with reasons mentioned above. **Always** patch `AP` in the Magisk app and use that instead.
- Never just flash only `AP`, or else Odin may shrink your `/data` filesystem size. Flash `AP` + `BL` + `CP` + `HOME_CSC` when upgrading.

## Custom Recovery

> **This installation method is deprecated and is maintained with minimum effort. YOU HAVE BEEN WARNED!**

It is very difficult to accurately detect the device's information in custom recovery environments. Due to this reason, installing Magisk through custom recoveries on modern devices is no longer recommended. If you face any issues, please use the [Patch Image](#patching-images) method as it is guaranteed to work 100% of the time.

- Download the Magisk APK
- Rename the `.apk` file extension to `.zip`, for example: `Magisk-v22.0.apk` → `Magisk-v22.0.zip`. If you have trouble renaming the file extension (like on Windows), use a file manager on Android or the one included in TWRP to rename the file.
- Flash the zip just like any other ordinary flashable zip.
- Reboot and check whether the Magisk app is installed. If it isn't installed automatically, manually install the APK.

> Warning: the `sepolicy.rule` file of modules may be stored in the `cache` partition. DO NOT MANUALLY WIPE THE `CACHE` PARTITION.
