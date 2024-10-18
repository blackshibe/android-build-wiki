# Build Errors

!!! info
    Last Updated for: Android 14

## `error while loading shared libraries: libcrypt.so.1`

-   `error while loading shared libraries: libcrypt.so.1: cannot open shared object file: No such file or directory`
-   You're missing SSL libraries locally; install libxcrypt-compat (on arch)

## `cannot find <yaml/yaml.h>`

-   Make sure yaml-dev headers are installed on your PC
-   If they are, edit `build/soong/ui/build/paths/config.go` and do [this](img/pkg-config.png)
-   [Telegram message link](https://t.me/alaskalinuxuser_romdevelopment/403961)
