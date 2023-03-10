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

IMAGE_NAME

    oowow | android | ubuntu

EXAMPLES

    ONLINE

    curl dl.khadas.com/online/rockchip-burn | sh -s -
    curl dl.khadas.com/online/rockchip-burn | sh -s - oowow --write --spi
    curl dl.khadas.com/online/rockchip-burn | sh -s - oowow --write --refresh --no-reset

    LOCAL

    wget https://raw.githubusercontent.com/hyphop/rockchip-burn/main/rockchip-burn
    chmod 0755 rockchip-burn
    ./rockchip-burn oowow --write

    ./rockchip-burn --update

```
