#!/bin/sh

## hyphop ##

#= Rockchip burn online
#< https://github.com/hyphop/rockchip-burn


HELP(){ cat <<HELP_
USAGE

[ENV] rockchip-burn [board] [IMAGE_NAME] [ARGS...]

## VER 0.155

board

    edge2 - default
    edge-2l

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
    --edge2l

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

HELP_
exit
}

usb_find(){
	local VID=${1-2207}
	local d
	local p
	for d in /sys/bus/usb/devices/*; do
		[ -f "$d/idVendor" ] || continue
		read v < "$d/idVendor"
		[ "$v" = "$VID" ] || continue
		read p < "$d/idProduct"
		#echo "+ $d $p">&2
		case $p in
			330c|350b|350e)
			echo "$v:$p"
			;;
		esac
	done
}

NAME=rockchip-burn
PROG=$(realpath -s "$0")
WORK=$(dirname "$PROG")

TOOL_HOME=~/upgrade_tool
TOOL_BIN="$TOOL_HOME/bin"

: ${MIRROR:=dl.khadas.com}
echo "MIRROR: $MIRROR"
: ${SOURCE:=https://raw.githubusercontent.com/hyphop/rockchip-burn/main/$NAME}
: ${DL_OOWOW:=$MIRROR/products/oowow/system/}

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
CMDF(){
    echo "### $@">&2
    "$@" || exit 1
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

usb_list(){
echo "Usb list:"
usb_find
#echo $USB_ID
}

debug(){
[ "$DEBUG" ] || return
echo "$@">&2
}

echo "rockchip-burn $@">&2

for VER in $(grep -m1 VER "$0"); do true; done

: ${DST:=emmc}
TYPE=sd
RESET=1

case $1 in
	edge2l|edge-2l|Edge-2l|Edge-2L) BOARD=edge-2l ; shift ;;
	edge2|Edge2) BOARD=edge2 ; shift ;;
esac

USB_ID=$(usb_find)
debug "usb_id: +$USB_ID+"

case $USB_ID in
	2207:350e) BOARD=edge-2l ;;
esac

: ${BOARD:=edge2}
board=$(lowcase $BOARD)

case $board in
	edge-2l) LOADER=rk3576_spl_loader_v1.10.108.bin ;;
esac

: ${LOADER:=rk3588_spl_loader_v1.08.111.bin}
: ${TOOL:=upgrade_tool}
: ${TOOL_DL:=https://raw.githubusercontent.com/khadas/utils/master/rk-flash-tool/tools/Linux_Upgrade_Tool/$TOOL}
: ${LOADER_DL:=$MIRROR/products/$board/firmware/boot/$LOADER}
: ${UT:=$TOOL_BIN/$TOOL}

for a in "$@"; do
    case $a in
	-t|--test)
	echo "Test mode"
	usb_list
	CMD $UT -v
	exit
	;;
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
	DEST=SPINOR
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
	    echo "[i] use a local file $SRC">&2 && LOCAL=y && continue
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
	    echo "[i] use a local file $SRC">&2 && LOCAL=y
	MODE=write
	;;
    esac
done

DL=${DL:-$MIRROR/.images/$board/}

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

grep -q OKAY2 $UDEV 2>/dev/null || {

MSG "install usb device permission to $UDEV, need admin permissions!"

cat <<EOF | sudo tee $UDEV
#OKAY2
#Maskrom
SUBSYSTEM=="usb", ENV{DEVTYPE}=="usb_device", ATTR{idVendor}=="2207", ATTR{idProduct}=="330c", MODE:="0666"
#Loader
SUBSYSTEM=="usb", ENV{DEVTYPE}=="usb_device", ATTR{idVendor}=="2207", ATTR{idProduct}=="350b", MODE:="0666"
#Rockchip Maskrom (new generation)
SUBSYSTEM=="usb", ENV{DEVTYPE}=="usb_device", ATTR{idVendor}=="2207", ATTR{idProduct}=="350e", MODE:="0666"
EOF

}

)

export PATH="$TOOL_BIN:$PATH"
#echo $PATH

loader(){
	local DEST
	CMDF $TOOL LD | grep Mode=Maskrom
	CMDF $TOOL DB "bin/$LOADER" $DEST
	sleep 1
	CMDF $TOOL RCI
	CMDF $TOOL RCB
	#CMDF $TOOL UL "bin/$LOADER" -noreset $DEST
	sleep 1
}

setup_dst(){
case $DST in
    spi)
    CMD $TOOL SSD 5
    ;;
    *)
    #CMD $TOOL SSD
    CMD $TOOL SSD 2 # EMMC
    ;;
esac
sleep 1
}

which $TOOL || FAIL "cant find $TOOL"

case $MODE in
    clean)
	echo "Clean storage $DST"
	loader    || FAIL "oops loader fail"
	setup_dst || FAIL "oops can setup DST: $DST"

	case $DST in
		SPI|spi)
	CMDF $TOOL EL 0x4000 0x10
	CMDF $TOOL EL 0x5000 0x10
		;;
		*)
	CMDF $TOOL EL 0x0 0x100000
		;;
	esac
	exit
	;;
esac


case $SRC in
    oowow)
    DL=$DL_OOWOW
    DST=spi
    TYPE=spi
    FILE=$BOARD-oowow-latest-$TYPE.img.gz
    #DST=spi
    ;;
#    android)
 #   FILE=$BOARD-android-12-v230114.raw.img.xz
  #  DST=emmc
  #  ;;
 #   ubuntu)
  #  FILE=$BOARD-ubuntu-22.04-gnome-linux-5.10-fenix-1.4-221229.img.xz
   # DST=emmc
   # ;;
esac

get_list(){

case $1-$2 in
    list-)
    echo "!$1">&2
    LL="dl/$board.list"
    GET - "$DL" | grep -o "${board}-[^\" ]*\.xz" | sort -u | tee "$LL"
    echo "$DL" > "$LL.url"
    ;;
    list-oowow)
    echo "!$1-$2:">&2
    DD= "${DL}versions/$board"
    LL="dl/$board-oowow.list"
    GET - "$DD" 2>/dev/null | grep -o $board-$SRC-..........-$TYPE.img.gz | sort -u | tee "$DD"
    echo "$DD" > "$LL.url"
    ;;
    *)
    #echo "??? $MODE-$SRC"
    ;;
esac
}

get_list $MODE $SRC

[ "$MODE" = "list" ] && exit

[ "$1" ] && {
echo --
D=dl/$board.list
[ -s "$D" ] || get_list list
F=$(grep -m1 "$1" "$D")
[ "$F" ] && FILE="$F" && DL=$(cat "$D.url")
echo "[$@]"
echo --
}

PKG=dl/$FILE
IMG=${PKG%.*}

echo MODE $MODE

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
    PKG="$SRC"
    IMG=$(basename "$SRC")
    IMG=${IMG%.??}
    echo "LOCAL: $PKG -> $IMG"
    ;;
esac

case $PKG in
    *.gz)
    [ "$gz" ] || INSTALL gzip pigz
    MSG "decompress $PKG wait..."
    CMD $gz -dc < $PKG > $IMG || FAIL "decompress fail"
    LOCAL=
    ;;
    *.xz)
    [ "$xz" ] || INSTALL pixz xz
    MSG "decompress $PKG wait..."
    CMD $xz -dc < $PKG > $IMG || FAIL "decompress fail"
    LOCAL=
    ;;
esac

PWD=$(pwd)

[ "$GET_ONLY" ] && {
MSG "get image only"
echo "$PWD/$PKG"
exit 0
}

MSG "write $IMG to $DST wait..."

[ "$LOCAL" ] && IMG=$PKG

TRY=1

while [ "1" ] ; do
    $TOOL LD | grep Mode=Maskrom && break
    printf "\r[i] wait device with upgrade mode - need press KEY_FN 3x times - $TRY" && sleep 1
    TRY=$((TRY+1))
done

echo

echo "[i] check is ready ..."

#$TOOL TD || $TOOL DB "bin/$LOADER" || exit 4
loader
setup_dst

CMD $TOOL WL 0 "$IMG" || FAIL "oops"

[ "$FILE" ]  && echo && echo "[i] OKAY $FILE was written to $DST"
[ "$RESET" ] && MSG "reset device" && CMD $TOOL RD 0
[ "$RESET" ] || MSG "no reset device"

exit
<<EOF

# edge2l
Bus 007 Device 033: ID 2207:350e Fuzhou Rockchip Electronics Company

# edge2
Bus 007 Device 035: ID 2207:350b Fuzhou Rockchip Electronics Company

