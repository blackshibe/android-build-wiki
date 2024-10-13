# Starting out

Guide updated for: Android 14

## General requirements

Android takes a lot of resources to compile. You need a:

-   A linux installation - VM or bare metal
-   A reasonably fast CPU
-   500GB of free disk space
-   16GB of RAM (with a large swapfile)

## Figure out whether your device has existing work done

Device dev support is on a scale from:

-   Official (Builds directly downloadable from the LineageOS website, for example)
-   Unofficial (XDA threads, or Telegram groups related to the device)
-   WIP trees (Device tree repos on the internet, no actually working builds)
-   No trees
-   No trees and no kernel

If you're new you should start out with a device with either official or unofficial builds.
To start your first compilation, you need to find the following things for your device:

-   A device tree ([example](https://github.com/lineageos/android_device_sony_pdx234))
    -   A repository with makefiles and configurations that specifies everything about the device.
-   A kernel ([example](https://github.com/lineageos/android_kernel_sony_sm8550))
    -   Android devices don't run mainline Linux. Each device has its' own manufacturer-developed kernel released somewhere, unless the manufacturer is breaking the GPL license.
-   Vendor blobs ([example](https://github.com/blackshibe/proprietary_vendor_sony_pdx234))
    -   Every device needs proprietary blobs for things ranging from the camera to custom built-in apps.

Vendor blobs can be extracted from a device running a working ROM if you can't find a repository with them. LineageOS does not store them at all for example, and part of their build instructions is pulling the files from a device already running LineageOS.

XDA threads for ROMs usually link all of these - [Example](https://xdaforums.com/t/crdroid-10-for-nb1-android-14-unofficial.4686492/)

If you have official ROM support, you may find them under the organization of the ROM that supports your device officially.

If you can't find a device tree, you're out of luck.

## Get the manifest for the ROM you're compiling

TODO. In short, download the build manifest for the ROM you're building and follow the instructions in the README.md

-   [LineageOS manifest](https://github.com/lineageos/android)
-   [CrDroid manifest](https://github.com/crdroidandroid/android)
