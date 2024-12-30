# Starting a bringup

!!! info
    Last Updated for: Android 15

This is a relatively difficult topic with no universal guide for good reason - Your experience will differ significantly whether you're bringing up an MTK or Qualcomm device and whether your manufacturer has released properly working sources or not. Expect errors completely unrelated to your actual issue and a lot of log digging.
    
## Gathering information

As a basis for bringup, you need to know the specifics of your device - The codename, build number, place to find stock firmware, the specs it's running, etc.

You will also need to find a:
- Kernel source code
    - OEMs are required to release their kernel sources according to GPL but they are sometimes late, or break the law.
- Stock firmware dump
    - You will need this for proprietary blobs and configs, partition and kernel config, build fingerprint, etc
- Similiar device tree 
    - Find one in the following order:
        - Same SoC with same manufacturer
        - Same SoC
        - Different SoC, same brand, same manufacturer

## Unpacking boot image

Bootimage will give you kernel configuration information. AOSP has an unpack bootimg utility

!!! TODO 

## proprietary-files.txt

There will be a lot of libraries you cannot build from source, which are pulled from the device using proprietary-files.txt. The script that does the extraction is `extract-files.sh` or `extract-files.py` depending on your device tree. You can also point it to a folder with a ROM dump (which makes things easier if you don't own the device yourself)

[More information here](proprietary_files.md)

## Building kernel

You will need to find a copyleft release of a kernel for your device. Each manufacturer releases it in different places and some don't release full source code. Afterwards, you need to

!!! TODO 


