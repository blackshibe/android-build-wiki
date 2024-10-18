# Starting out

!!! info
    Last Updated for: Android 14

## General requirements

You need to be decent at using Linux & development tools like Git, capable of solving problems on your own, comfortable with a commandline, and knowledgeable with flashing custom ROMs. Builds are slow, debugging is difficult. The list of languages and tools Android as a whole uses is so long it's not worth listing.

Android also takes a lot of resources to compile. At bare minimum, you need:

-   A linux installation - VM or bare metal, preferrably Ubuntu
-   A reasonably fast CPU
-   500GB of free disk space
-   16GB of RAM (with a massive swapfile)

## Figure out whether your device has existing work done

Device dev support is on a (rough) scale from:

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

## Set up repo, get to syncing

!!! INFO
    This part of the guide is from [the crDroid build manifest](https://github.com/crdroidandroid/android)

[Repo](http://source.android.com/source/developing.html) is a tool provided by Google that
simplifies using [Git](http://git-scm.com/book) in the context of the Android source.

### Install dependencies

```
sudo apt install bc bison build-essential ccache curl flex g++-multilib gcc-multilib git git-lfs gnupg gperf imagemagick lib32ncurses5-dev lib32readline-dev lib32z1-dev liblz4-tool libncurses5 libncurses5-dev libsdl1.2-dev libssl-dev libwxgtk3.0-gtk3-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev
```

If some are missing (in the future), find replacements or continue anyway. Compilation won't succeed or give you more useful specific errors as to what you're missing if you're short on needed packages.

## Install Repo tool
```bash
# Make a directory where Repo will be stored and add it to the path
$ mkdir ~/bin
$ PATH=~/bin:$PATH

# Download Repo itself
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo

# Make Repo executable
$ chmod a+x ~/bin/repo
```

## Initialize repo

You're going to want to find the build manifest for the ROM and the ROM version you're building. In this example, [LineageOS](https://github.com/lineageOS/android).

```bash
# Create a directory for the source files
# This can be located anywhere (as long as the fs is case-sensitive)
$ mkdir my-rom
$ cd my-rom

# the manifest tells you what to clone
repo init -u https://github.com/LineageOS/android.git -b lineage-21.0 --git-lfs
```

## Sync

This is what you will run each time you want to pull in upstream changes. Keep in mind that on your
first run, it is expected to take a while as it will download all the required Android source files
and their change histories.

```bash
# Let Repo take care of all the hard work
# This always downloads the latest changes from all repos, skipping repositories with uncommited changes
$ repo sync
```

## Initialize build manifest

The .repo folder has manifests, and those manifests tell repo what to download. You will need a local one for device-specific things.
Assuming the folder you created for the ROM is `my-rom`, make a folder in `my-rom/.repo/local_manifests`, and make a file called
`my-rom/.repo/local_manifests/manifest.xml`.

Example contents:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
    <!-- Remotes are places you get repositories from. The name is arbitrary -->
    <remote name="blackshibe" fetch="https://github.com/blackshibe"/>
    <remote name="LineageOS" fetch="https://github.com/LineageOS"/>

    <!-- The device tree as mentioned above. This one is commonized, meaning non-device specific changes are in another repo (sm8550-common) -->
    <project path="device/sony/pdx234" name="android_device_sony_pdx234" remote="blackshibe" revision="lineage-21" />

    <!-- path is the path the repo will be cloned to, name is the name of the repository, the remote is the remote from above, revision is the branch  -->
    <project path="device/sony/sm8550-common" name="android_device_sony_sm8550-common" remote="blackshibe" revision="lineage-21" />

    <!-- Device kernel. Sometimes you need additional repos related -->
    <project path="kernel/sony/sm8550" name="android_kernel_sony_sm8550" remote="LineageOS" revision="lineage-21" />
    <project path="kernel/sony/sm8550-devicetrees" name="android_kernel_sony_sm8550-devicetrees" remote="LineageOS" revision="lineage-21" />
    <project path="kernel/sony/sm8550-modules" name="android_kernel_sony_sm8550-modules" remote="LineageOS" revision="lineage-21" />

    <!-- Vendor files. These are commonized as well to match the device trees -->
    <project path="vendor/sony/pdx234" name="proprietary_vendor_sony_pdx234" remote="blackshibe" revision="lineage-21" />
    <project path="vendor/sony/sm8550-common" name="proprietary_vendor_sony_sm8550-common" remote="blackshibe" revision="lineage-21" />

    <!-- You can also delete projects from the main build manifest -->
    <!-- <remove-project name="frameworks/base" /> -->
</manifest>
```

Once this is done run repo sync again. It will tell you if any repos are defined incorrectly. Once they are all there, proceed

## Build

### For devices officially supported by the ROM you're building:

```bash
# Run to prepare our devices list
$ . build/envsetup.sh
# ... now run
$ brunch devicecodename
```

### For other devices

Your device tree will have a primary makefile. Lineage uses `lineage_CODENAME.mk` In the example above, it is `lineage_pdx234.mk`. You need this for the build target

```bash
# Prepare build
$ . build/envsetup.sh
# Run build. Format is device-release-type, 
$ brunch lineage_pdx234-ap2a-eng
```

ap2a means 14 QPR3. ap3a is 15. Eng build type enables adb on boot and various debugging features in recovery and system as well as disabling a lot of optimizations.
You should build userdebug and user once the ROM works fine

## Result

Once done, the files are in `out/target/product/CODENAME`. You will want to flash and to start debugging your build