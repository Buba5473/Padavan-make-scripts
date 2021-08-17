SRC_NAME="$(curl -fsSL --compressed 'https://busybox.net/downloads/' | grep -E '<a href="busybox-[0-9][^"/]*.tar.bz2"' | sed -r 's!.*<a href="busybox-([0-9][^"/]*).tar.bz2".*!\1!' | sort -V | tail -1)"
SRC_URL=https://busybox.net/downloads/busybox-$SRC_NAME.tar.bz2

all: download_test extract_test
	$(MAKE) -j$(HOST_NCPU) -C $(SRC_NAME)

download_test:
	( if [ ! -f busybox-$SRC_NAME.tar.bz2 ]; then \
		wget -t 5 --timeout=20 --no-check-certificate $SRC_URL; \
	fi )

extract_test:
	( if [ ! -d $(SRC_NAME) ]; then \
		tar xf busybox-$SRC_NAME.tar.bz2; \
	fi )

clean:
	$(MAKE) -C $(SRC_NAME) distclean

romfs:
	$(MAKE) -C $(SRC_NAME) CONFIG_PREFIX=$(ROMFSDIR) install

