# Qualcomm Crashdump

!!! info
    Last Updated for: Android 14
    
This is your last resort, if you can't get USB working and/or ADB access; and you're bootlooping. So, some Qualcomm devices have crashdump mode enabled, and this allows us to get a memory dump using EDL, when the kernel panics.

In my case, I had to enable dload mode in the kernel:

```diff
Author: Raghuram Subramani <raghus2247@gmail.com>
Date:   Wed Oct 16 09:51:00 2024 +0530

    power: reset: msm: Use download mode by default

diff --git a/drivers/power/reset/msm-poweroff.c b/drivers/power/reset/msm-poweroff.c
index f267ec9d..50b5d76d 100644
--- a/drivers/power/reset/msm-poweroff.c
+++ b/drivers/power/reset/msm-poweroff.c
@@ -84,7 +84,7 @@ static struct notifier_block panic_blk = {
 #endif

 static int dload_type = SCM_DLOAD_FULLDUMP;
-static int download_mode;
+static int download_mode = 1;
 static struct kobject dload_kobj;
 static void *dload_mode_addr, *dload_type_addr;
 static bool dload_mode_enabled;
```

To trigger a kernel panic on reboot:

```diff
Author: Raghuram Subramani <raghus2247@gmail.com>
Date:   Wed Oct 16 09:54:35 2024 +0530

    kernel: reboot: Panic before restart

diff --git a/kernel/reboot.c b/kernel/reboot.c
index 2946ed1d..aaec620a 100644
--- a/kernel/reboot.c
+++ b/kernel/reboot.c
@@ -221,6 +221,7 @@ void kernel_restart(char *cmd)
        else
                pr_emerg("Restarting system with command '%s'\n", cmd);
        kmsg_dump(KMSG_DUMP_RESTART);
+  panic("SOMETHINGRANDOMICANSEARCHFOR");
        machine_restart(cmd);
 }
 EXPORT_SYMBOL_GPL(kernel_restart);
```

Next, compile the kernel and flash it. When reboot is triggered, the kernel panics and the phone reboots into crashdump mode. To dump the kernel, install [edl](https://github.com/bkerler/edl) and run:

```sh
edl memorydump
```

This will create a `memory/` folder, and inside it there will be multiple files. We are concerned with the DDRCSx.bin.

First, we can try searching for what requested the reboot:

```sh
strings DDRCSx.bin | grep reboot | grep request
```

If you find something, open the bin file in a text editor, search for the string, and look above it to see what requested the reboot.

Alternatively, we can get the dmesg log. To do this, we search for the username of the computer that built the kernel. This is because, the first line of the kernel log contains the username of the builder.

```sh
strings DDRCSx.bin | grep USERNAME
```

Look for this string in the dump, and below it will be the kernel log.
