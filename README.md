# rockchip-burn

```
USAGE

[ENV] rockchip-burn [board] [IMAGE_NAME] [ARGS...]

## VER 0.155

board

    edge2 [default] | edge-2l

ARGS
    --write
    --spi
    --refresh
    --no-reset
    --clean
    --help
    --update
    --list
    --test

IMAGE_NAME TAGS

    oowow | android | ubuntu

ENV VAR=value

    MIRROR = dl.khadas.com | dl.khadas.cn
    BOARD  = edge2 | edge-2l
    ...

EXAMPLES

ONLINE USAGE

    curl dl.khadas.com/online/rockchip-burn | sh -s -
    curl dl.khadas.com/online/rockchip-burn | sh -s - oowow --write --spi
    curl dl.khadas.com/online/rockchip-burn | sh -s - oowow --write --refresh --no-reset

    # get oowow images list
    curl dl.khadas.com/online/rockchip-burn | sh -s - oowow --list
    # write custom version ( auto download image )
    curl dl.khadas.com/online/rockchip-burn | sh -s - edge2-oowow-230316.307-sd.img.gz

LOCAL USAGE

    wget https://raw.githubusercontent.com/hyphop/rockchip-burn/main/rockchip-burn
    chmod 0755 rockchip-burn
    ./rockchip-burn oowow --write

    # script self update to latest
    ./rockchip-burn --update

    # get oowow images list
    ./rockchip-burn oowow --list
    # write local image
    ./rockchip-burn ./edge2-oowow-230316.307-sd.img.gz

    # write remote image from URL
    ./rockchip-burn http://dl.khadas.com/products/oowow/system/versions/edge2/edge2-oowow-230308.000-sd.img.gz

```
