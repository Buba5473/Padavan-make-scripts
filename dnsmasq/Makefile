SRC_NAME="$(curl -fsSL --compressed 'http://www.thekelleys.org.uk/dnsmasq/' | grep -E '<a href="dnsmasq-[0-9][^"/]*.tar.xz"' | sed -r 's!.*<a href="dnsmasq-([0-9][^"/]*).tar.xz".*!\1!' | sort -V | tail -1)"
SRC_URL=http://www.thekelleys.org.uk/dnsmasq/dnsmasq-$SRC_NAME.tar.xz
THISDIR = $(shell pwd)

COPTS := -DHAVE_BROKEN_RTC -DNO_INOTIFY -DNO_AUTH -DNO_LOOP
ifndef CONFIG_IPV6
COPTS += -DNO_IPV6
endif
ifndef CONFIG_USB_SUPPORT
COPTS += -DNO_TFTP
endif
ifneq ($(CONFIG_FIRMWARE_INCLUDE_IPSET),y)
COPTS += -DNO_IPSET
endif

all: download_test extract_test
	$(MAKE) -j$(HOST_NCPU) -C $(SRC_NAME) COPTS="$(COPTS)"

download_test:
	( if [ ! -f dnsmasq-$SRC_NAME.tar.xz ]; then \
		wget -t 5 --timeout=20 --no-check-certificate $SRC_URL; \
	fi )

extract_test:
	( if [ ! -d $(SRC_NAME) ]; then \
		tar xf dnsmasq-$SRC_NAME.tar.xz; \
	fi )

clean:
	$(MAKE) -C $(SRC_NAME) clean
	rm -f dnsmasq

romfs:
	cp $(SRC_NAME)/src/dnsmasq .
	$(STRIP) dnsmasq
	$(ROMFSINST) /usr/sbin/dnsmasq
	$(ROMFSINST) -p +x $(THISDIR)/dnsmasq.sh /usr/bin/dnsmasq.sh
