lastSuccefulBuild="$(curl -fsSL --compressed 'https://build.transmissionbt.com/job/trunk-linux/lastSuccessfulBuild/artifact/' | grep -E 'transmission-master-[a-z0-9][^"/]*.tar.xz' | sed -r 's!.*transmission-master-([a-z0-9][^"/]*).tar.xz.*!\1!')"
SRC_NAME=transmission-3.00+
SRC_URL=https://build.transmissionbt.com/job/trunk-linux/lastSuccessfulBuild/artifact/transmission-master-$lastSuccefulBuild.tar.xz

CFLAGS   += -O3 -ffunction-sections -fdata-sections -D_LARGEFILE64_SOURCE -D_LFS64_LARGEFILE
LDFLAGS  += -Wl,--gc-sections

THISDIR = $(shell pwd)

all: download_test extract_test config_test
	$(MAKE) -C $(SRC_NAME) && \
	$(MAKE) -C $(SRC_NAME) install DESTDIR=$(THISDIR)/install

download_test:
	( if [ ! -f transmission-master-$lastSuccefulBuild.tar.xz ]; then \
		wget -t 5 --timeout=20 --no-check-certificate $SRC_URL; \
#		wget -t5 --timeout=20 --no-check-certificate -O $(SRC_NAME) $(SRC_URL); \
	fi )

extract_test:
	( if [ ! -d $(SRC_NAME) ]; then \
		tar xf transmission-master-$lastSuccefulBuild.tar.xz; \
	fi )

config_test:
	( if [ -f ./config_done ]; then \
		echo "the same configuration"; \
	else \
		make configure && touch config_done; \
	fi )

configure:
	( cd $(SRC_NAME) ; \
	HAVE_CXX=yes \
	PKG_CONFIG_PATH=$(STAGEDIR)/lib/pkgconfig \
	./configure \
		--prefix=/usr \
		--enable-daemon \
		--enable-lightweight \
		--disable-nls \
		--disable-cli \
		--disable-mac \
		--without-gtk \
		--with-inotify \
		--with-zlib=$(STAGEDIR) \
		OPENSSL_CFLAGS="-I$(STAGEDIR)/include" \
		OPENSSL_LIBS="-L$(STAGEDIR)/lib -lssl -lcrypto" \
		LIBCURL_CFLAGS="-I$(STAGEDIR)/include" \
		LIBCURL_LIBS="-L$(STAGEDIR)/lib -lcurl" \
		LIBEVENT_CFLAGS="-I$(STAGEDIR)/include" \
		LIBEVENT_LIBS="-L$(STAGEDIR)/lib -levent" \
		--host=$(HOST_TARGET) \
		--build=$(HOST_BUILD) ; \
	)

clean:
	if [ -f $(SRC_NAME)/Makefile ] ; then \
		$(MAKE) -C $(SRC_NAME) distclean ; \
	fi ; \
	rm -f config_done
	rm -rf install

romfs:
ifeq ($(CONFIG_FIRMWARE_INCLUDE_TRANSMISSION_WEB_CONTROL),y)
	mkdir -p $(INSTALLDIR)/usr/share/transmission
	cp -rf $(THISDIR)/transmission-control/web $(INSTALLDIR)/usr/share/transmission
else
	cp -rf $(THISDIR)/install/usr/share/transmission $(INSTALLDIR)/usr/share
	rm -f $(INSTALLDIR)/usr/share/transmission/web/LICENSE
endif
	$(ROMFSINST) $(THISDIR)/install/usr/bin/transmission-daemon /usr/bin/transmission-daemon
	$(ROMFSINST) -p +x /usr/bin/transmission.sh
