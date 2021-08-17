THISDIR=$(shell pwd)
SRC_NAME=binaries

all: download_test config_test
	
download_test: 
	( if [ ! -f $(THISDIR)/$(SRC_NAME).tar.gz ]; then \
		wget -t5 --timeout=20 --no-check-certificate $(curl -s https://api.github.com/repos/Wind4/vlmcsd/releases/latest | grep binaries.tar.gz |tail -n 1| cut -d '"' -f 4); \
	fi )

config_test:
	( if [ -f ./config_done ]; then \
		echo "the same configuration"; \
	else \
		make configure && touch config_done; \
	fi )

configure:
	( if [ ! -d $(THISDIR)/$(SRC_NAME) ]; then \
		tar xf $(SRC_NAME).tar.gz; \
	fi )

clean:
	if [ -f $(SRC_NAME)/Makefile ] ; then \
		$(MAKE) -C $(SRC_NAME) distclean ; \
	fi ; \
	rm -f config_done
	rm -rf install

romfs:
	# -p perms		: chmod style permissions for dst
	# -S			: don't strip after installing
	# -s sym-link		: dst is a sym-link to 'sym-link'
        $(ROMFSINST) -p +x -S $(THISDIR)/$(SRC_NAME)/binaries/Linux/mips/little-endian/static/vlmcsdmulti-mips32r2el-Fritzbox-71xx-72xx-uclibc-static /usr/bin/vlmcsdmulti
	$(ROMFSINST) -s vlmcsdmulti /usr/bin/vlmcsd
	$(ROMFSINST) -s vlmcsdmulti /usr/bin/vlmcs
	$(ROMFSINST) -p +x $(THISDIR)/vlmcsd.sh /usr/bin/vlmcsd.sh

