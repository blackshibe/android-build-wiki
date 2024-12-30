# Proprietary files

!!! info
    Last Updated for: Android 15
    
`proprietary-files.txt` is a list of proprietary libraries (called blobs) that need to be pulled from stock device firmware to be used in your custom ROM. They are used with scripts `extract-files.py` and `setup-makefiles.py` (or *.sh for LineageOS older than 22.0) to generate makefiles and soong files in `vendor`.

## Dumping firmware

You shouldn't make edits to the generated vendor folder. Instead, any changes should be made to `proprietary-files.txt` and `extract-files.py`.

You can run `extract-files.py` either with an ADB device connected, or with `extract-files.py /path/to/firmware/folder`. This will pull the files from the source to the vendor folder. It will also tell you about any errors.

You will know about missing libraries either from soong library checks, make errors or dlopen errors in the build logcat. You can fix them by including the libraries the blobs are asking for from stock firmware.

## Fixup

Some blobs may need fixing when imported, for reasons including Android updates. This is what `blob_fixups` is for.

```py
blob_fixups: blob_fixups_user_type = {
    # Simple file edit
    'vendor/etc/msm_irqbalance.conf': blob_fixup()
    .regex_replace('IGNORED_IRQ=27,23,38', 'IGNORED_IRQ=27,23,38,115,332'),

    # Add line to file
    'vendor/etc/seccomp_policy/qwesd@2.0.policy': blob_fixup()
    .add_line_if_missing('pipe2: 1')
    .add_line_if_missing('gettid: 1'),

    # Add lib import to blob
    'system_ext/lib64/libwfdnative.so': blob_fixup()
    .add_needed('libinput_shim.so'),

    # Load android.hardware.light-V1-ndk.so instead of android.hardware.light-V1-ndk_platform.so
    'vendor/lib64/vendor.semc.hardware.extlight-V1-ndk_platform.so': blob_fixup()
    .replace_needed('android.hardware.light-V1-ndk_platform.so', 'android.hardware.light-V1-ndk.so'),
}  # fmt: skip
```

## Shims

Sometimes blobs may have unresolved symbols due to Android API changes:

```
[PATH]/libwvhidl.so: error: Unresolved symbol: CBS_init
[PATH]/libwvhidl.so: note:
[PATH]/libwvhidl.so: note: Some dependencies might be changed, thus the symbol(s) above cannot be resolved.
[PATH]/libwvhidl.so: note: Please re-build the prebuilt file: "[PATH]/libwvhidl.so".
[PATH]/libwvhidl.so: note:
[PATH]/libwvhidl.so: note: If this is a new prebuilt file and it is designed to have unresolved symbols, add one of the following properties:
[PATH]/libwvhidl.so: note:   Android.bp: allow_undefined_symbols: true,
[PATH]/libwvhidl.so: note:   Android.mk: LOCAL_ALLOW_UNDEFINED_SYMBOLS := true
```

here `libwvhidl.so` is missing `CBS_init`. [Looking at LineageOS gerrit](https://review.lineageos.org/q/libwvhidl) you can see this file is commonly patched with a LineageOS libcrypto shim - All you need to do is what the patches do, or just to cherry-pick them