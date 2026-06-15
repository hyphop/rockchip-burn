# rockchip-burn

```
USAGE

[ENV] rockchip-burn [board] [IMAGE_NAME|TAG|INDEX] [ACTION] [ARGS...]

## VER 0.161

BOARD

    edge2 | edge-2l

    If board is not specified, it is auto-detected from USB.
    If USB detection fails, default is edge2.

TAGS

    oowow      exclusive OOWOW/SPI mode
    android    normal images catalog filter
    ubuntu     normal images catalog filter

INDEX

    Index from --list output. Accepted anywhere in arguments.

    ./rockchip-burn edge-2l --list
    ./rockchip-burn edge-2l 1 --dl
    ./rockchip-burn edge-2l --write 2

    ./rockchip-burn edge-2l oowow --list
    ./rockchip-burn edge-2l oowow --dl 1
    ./rockchip-burn edge-2l oowow --write 2

IMAGE_NAME

    edge-2l-oowow-260529.000-spi.img.gz
    edge-2l-ubuntu-24.04-server-linux-6.1-fenix-1.7.6-260519.img.xz
    edge-2l-android-14-v260428.raw.img.xz

    Local file/path is used directly when it exists.
    http/https URL is used directly.
    Bare filename is resolved through catalog.

ACTIONS

    --devices   show detected USB devices only
    --list      list images only, enumerated
    --dl        download image only
    --write     download/decompress/write image
    --clean     clean selected storage
    --test      show devices and upgrade_tool version if installed
    --update    self update
    --help      show help

ARGS

    --spi       force SPI destination
    --sd        force eMMC/SD destination
    --refresh   re-download image / refresh catalog cache
    --no-reset  do not reset device after write

CLEAR USAGE

    ./rockchip-burn --devices

    ./rockchip-burn
    ./rockchip-burn edge-2l

    # normal images catalog
    ./rockchip-burn --list
    ./rockchip-burn edge-2l --list
    ./rockchip-burn edge-2l ubuntu --list
    ./rockchip-burn edge-2l android --list
    ./rockchip-burn edge-2l --dl 1
    ./rockchip-burn edge-2l --write 2

    # oowow exclusive mode
    ./rockchip-burn edge-2l oowow
    ./rockchip-burn edge-2l oowow --list
    ./rockchip-burn edge-2l oowow --dl
    ./rockchip-burn edge-2l oowow --write
    ./rockchip-burn edge-2l oowow --dl 1
    ./rockchip-burn edge-2l oowow --write 2

    # exact image
    ./rockchip-burn edge-2l-oowow-260529.000-spi.img.gz --dl
    ./rockchip-burn edge-2l-oowow-260529.000-spi.img.gz --write
    ./rockchip-burn edge-2l-ubuntu-24.04-server-linux-6.1-fenix-1.7.6-260519.img.xz --write

ONLINE USAGE

    curl dl.khadas.com/online/rockchip-burn | sh -s -
    curl dl.khadas.com/online/rockchip-burn | sh -s - --devices
    curl dl.khadas.com/online/rockchip-burn | sh -s - edge-2l --list
    curl dl.khadas.com/online/rockchip-burn | sh -s - edge-2l oowow --list
    curl dl.khadas.com/online/rockchip-burn | sh -s - edge-2l oowow --write

ENV VAR=value

    MIRROR = dl.khadas.com | dl.khadas.cn
    BOARD  = edge2 | edge-2l
    DEBUG  = 1
```
