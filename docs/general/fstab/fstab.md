To create the fstab, you can use sebaubuntu's twrpdtgen, which generates a basic device tree. Then, edit the device tree using the newer TWRP device tree template. The fstab is important because it contains information and mount points for your system.

twrpdtgen creates the fstab file, but you'll need to add ";backup=1;flashimg" flags for partitions you want to backup and flash, and ";wipeingui" flag to the partitions you want to wipe. For partitions you want to flash images to, change ext4 to emmc.

For dynamic partitions devices, there is no partition called "system", only a partition called "super". Unpacking the super.img reveals that it contains the system, vendor, product, and odm partitions. The super partition is acting like a container.

If you have a super partition, remove the "logical" flag in the system, vendor, product, and odm lines. Then, add the ";wipeingui" flag, make a copy of the four lines, and rename the copied line's mount points to system_image. Change the ext4 to emmc in the copied lines, and add "/dev/block/mapper/" to the beginning of their locations. Remove the ";wipeingui" from the copied lines and add ";backup=1;flashimg" flags.

After making these modifications, you will be able to backup, wipe, and flash the partitions inside the super partition of your device. Good luck!


