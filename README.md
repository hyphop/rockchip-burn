# rockchip-burn

```
USAGE rockchip-burn [IMAGE_NAME] [ARGS...]

ARGS

    --write
    --spi
    --refresh
    --no-reset
    --clean
    --help
    --update
    --list

IMAGE_NAME

    oowow | android | ubuntu

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

    ./rockchip-burn --update

    # get oowow images list
    ./rockchip-burn oowow --list
    # write local image
    ./rockchip-burn ./edge2-oowow-230316.307-sd.img.gz

```
