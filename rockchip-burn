#!/bin/sh

## hyphop ##

#= Rockchip burn online
#< https://github.com/hyphop/rockchip-burn


HELP(){ cat <<HELP_
USAGE rockchip-burn [IMAGE_NAME] [ARGS...]

## VER 0.144

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

    # script self update to latest
    ./rockchip-burn --update

    # get oowow images list
    ./rockchip-burn oowow --list
    # write local image
    ./rockchip-burn ./edge2-oowow-230316.307-sd.img.gz

    # write remote image from URL
    ./rockchip-burn http://dl.khadas.com/products/oowow/system/versions/edge2/edge2-oowow-230308.000-sd.img.gz

HELP_
exit
}

for VER in $(grep -m1 VER "$0" 2>/dev/null); do true; done

PROG=$(realpath -s "$0")
WORK=$(dirname "$PROG")

NAME=rockchip-burn
SOURCE=https://raw.githubusercontent.com/hyphop/rockchip-burn/main/$NAME

LOADER=${LOADER-rk3588_spl_loader_v1.07.111.bin}
TOOL=${TOOL-upgrade_tool}

TOOL_DL=${TOOL_DL-https://raw.githubusercontent.com/khadas/utils/master/rk-flash-tool/tools/Linux_Upgrade_Tool/$TOOL}
LOADER_DL=${LOADER_DL-http://dl.khadas.com/products/edge2/firmware/boot/$LOADER}

TOOL_HOME=~/upgrade_tool
TOOL_BIN=$TOOL_HOME/bin

FAIL(){
    echo "[e] $@">&2
    exit 1
}
MSG(){
    echo "[i] $@"
}
CMD(){
    echo "### $@">&2
    "$@"
}

lowcase(){
    echo "$@" | tr '[:upper:]' '[:lower:]'
}

UP(){
gz=${GZ-$(which pigz || which gzip)}
xz=${XZ-$(which pixz || which xz)}
get=${GET-$(which curl || which wget)}
}

UP

INSTALL(){
    MSG "install $@, need admin permissions!"
    CMD sudo apt install $@ || FAIL "please install it manually"
    UP
}

[ "$get" ] || {
    MSG "need install wget or curl"
    INSTALL wget
}

[ "$get" ] || FAIL "curl or wget not found"

GET(){
    (
    [ "$3" ] && CMD cd "$3"

    case $get in
	*wget*)
	CMD wget "$2" -O"$1"
	;;
	*curl*)
	CMD curl -R -jkL "$2" -o"$1"
	;;
    esac
    )
}

CHK(){
    [ -s "$3$1.okay" ] || {
	GET "$@" && md5sum "$3$1" > "$3$1.okay"
    }
}

echo "rockchip-burn $@">&2


DST=${DST-emmc}
TYPE=sd

RESET=1

BOARD=${BOARD-edge2}
board=$(lowcase $BOARD)

DL_OOWOW=http://dl.khadas.com/products/oowow/system/

for a in "$@"; do
    case $a in
	-v|--version)
	echo $VER && exit
	;;
	--clean)
	MODE=clean
	;;
	--no-reset)
	RESET=
	;;
	-r|--refresh)
	REFRESH=1
	;;
	-g|--get)
	MODE=write
	GET_ONLY=1
	;;
	--spi)
	DST=spi
	TYPE=spi
	;;
	-w|--write)
	MODE=write
	;;
	-l|--list)
	MODE=list
	;;
	--readme)
	echo "# $NAME"
	echo
	echo '```'
	sh $0 --help
	echo '```'
	exit
	;;
	-h|--help)
	MODE=help
	;;
	-u|--update)
	MODE=update
	;;
	$board-*.img.gz)
	MODE=write
	[ -s "$a" ] && SRC=$(realpath "$a") && \
	    echo "[i] use a local file $SRC">&2 && LOCAL="$SRC" && continue
	DL="${DL_OOWOW}versions/$board/"
	FILE="$a"
	SRC="$DL$FILE"
	echo "[i] remote file $SRC">&2
	;;
	http://*|https://*)
	SRC="$a"
	DL=$(dirname "$a")/
	FILE=$(basename "$a")
	MODE=write
	;;
	*)
	SRC="$a"
	[ -s "$a" ] && SRC=$(realpath "$a") && \
	    echo "[i] use a local file $SRC">&2 && LOCAL="$SRC"
	MODE=write
	;;
    esac
done

case $MODE in
    ""|help)
    HELP
    ;;
    update)

    RND=${RANDOM-$$}

    VER_OLD=$(grep -m1 "^## VER" $PROG)

    MSG "self update mode $PROG from $VER_OLD to ..."

    [ -d "$WORK/.git" ] && FAIL "inside Git not allowed"

    GET "$NAME.new" "$SOURCE?$RND" /tmp/ || FAIL "download update fail"
    VER_NEW=$(grep -m1 "^## VER" /tmp/$NAME.new)
    cp /tmp/$NAME.new $PROG || FAIL "update fail..."
    MSG "OKAY self updated $VER_NEW"
    exit 0
    ;;
    write|clean|list)
    ;;
    *)
    FAIL "unknown mode "
    ;;
esac

mkdir -p $TOOL_BIN || FAIL "mkdir $TOOL_BIN"
mkdir -p $TOOL_HOME/dl || FAIL "mkdir dl"

CMD cd $TOOL_HOME || FAIL "cd fail"

ARCH=$(uname -m 2>/dev/null)

case $ARCH in
    x86_64)
    ;;
    *)
    FAIL "not supported arch $ARCH"
    ;;
esac

( # pre check

cd $TOOL_BIN || FAIL "cd $TOOL_BIN"
CHK "$TOOL" "$TOOL_DL" || FAIL "tool"

[ -x "$TOOL" ] || chmod 0755 $TOOL

CHK "$LOADER" "$LOADER_DL" || FAIL "loader"

UDEV=/etc/udev/rules.d/80-rockchip-usb.rules

grep -q OKAY $UDEV 2>/dev/null || {

MSG "install usb device permission to $UDEV, need admin permissions!"

cat <<EOF | sudo tee $UDEV
#OKAY
SUBSYSTEM=="usb", ENV{DEVTYPE}=="usb_device", ATTR{idVendor}=="2207", ATTR{idProduct}=="330c", MODE:="0666"
SUBSYSTEM=="usb", ENV{DEVTYPE}=="usb_device", ATTR{idVendor}=="2207", ATTR{idProduct}=="350b", MODE:="0666"
EOF

}

)

export PATH="$TOOL_BIN:$PATH"
#echo $PATH

which $TOOL || FAIL "cant find $TOOL"


case $MODE in
    clean)
    FAIL "not ready"
    ;;
esac

DL=${DL:-http://dl.khadas.com/.images/$BOARD/}

case $SRC in
    oowow)
    DL=$DL_OOWOW
    FILE=$BOARD-oowow-latest-$TYPE.img.gz
    ;;
    android)
    FILE=$BOARD-android-12-v230114.raw.img.xz
    DST=emmc
    ;;
    ubuntu)
    FILE=$BOARD-ubuntu-22.04-gnome-linux-5.10-fenix-1.4-221229.img.xz
    DST=emmc
    ;;
esac

case $MODE-$SRC in
    list-oowow)
    echo "$MODE-$SRC:">&2
    GET - ${DL}versions/$board 2>/dev/null | grep -o $board-$SRC-..........-$TYPE.img.gz | uniq | sort
    exit 0
    ;;
esac

PKG=dl/$FILE
IMG=${PKG%.*}

[ "$REFRESH" ] && {
    MSG "refresh mode"
    [ -s "dl/$FILE" ] && {
	MSG "refresh $FILE"
	rm -rf "dl/$FILE" "dl/$FILE.okay"
    }
}

case "$LOCAL" in
    "")
    CHK "$FILE" "$DL$FILE" dl/ || FAIL "oops $FILE"
    ;;
    *)
    PKG="$LOCAL"
    IMG=$(basename "$LOCAL")
    IMG=${IMG%.??}
    echo "LOCAL: $PKG -> $IMG"
    ;;
esac

case $PKG in
    *.gz)
    [ "$gz" ] || INSTALL gzip pigz
    MSG "decompress $PKG wait..."
    CMD $gz -dc $PKG > $IMG || FAIL "decompress fail"
    ;;
    *.xz)
    [ "$xz" ] || INSTALL pixz xz
    MSG "decompress $PKG wait..."
    CMD $xz -dc $PKG > $IMG || FAIL "decompress fail"
    ;;
esac

[ "$GET_ONLY" ] && {
MSG "get image only"
PWD=$(pwd)
echo "$PWD/$PKG"
exit 0
}

MSG "write $IMG to $DST wait..."

TRY=1

while [ "1" ] ; do
    $TOOL LD | grep Mode=Maskrom && break
    printf "\r[i] wait device with upgrade mode - need press KEY_FN 3x times - $TRY" && sleep 1
    TRY=$((TRY+1))
done

echo

echo "[i] check is ready ..."
$TOOL TD || $TOOL DB bin/$LOADER || exit 4

case $DST in
    spi)
(
sleep 1
echo 1
echo RCB
sleep 2
echo SSD
echo 5
echo WL 0 $IMG
echo Q
) | $TOOL || FAIL "oops"
    ;;
    emmc)
    CMD $TOOL WL 0 "$IMG" || FIAL "oops"
    ;;
esac

[ "$FILE" ] && \
echo "\n[i] OKAY $FILE was written to $DST"

[ "$RESET" ] && MSG "reset device" && CMD $TOOL RD 0
[ "$RESET" ] || MSG "no reset device"
