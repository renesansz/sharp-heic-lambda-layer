# Library Versions
WEBP_VERSION=1.3.2
LIBDE265_VERSION=1.0.12
LIBHEIF_VERSION=1.19.5
VIPS_VERSION=8.12.2
SHARP_VERSION=0.33.5

PREFIX_PATH=/opt
ARTIFACTS_DIR=$(PREFIX_PATH)/artifacts

export PKG_CONFIG_PATH=$(PREFIX_PATH)/lib/pkgconfig

build-SharpHEICLayer: libvips
	mkdir -p "$(ARTIFACTS_DIR)/nodejs"
	mkdir -p "$(ARTIFACTS_DIR)/lib"

	# sharp uses several of the libs we installed or compiled. extract the full list and copy all of those into /opt/lib
	# extract list with ldd from sharp.node, manipulate a bit with sed to only get the absolute paths, then copy
	LD_LIBRARY_PATH=$(PREFIX_PATH)/lib npm --prefix "$(ARTIFACTS_DIR)/nodejs/" install sharp@$(SHARP_VERSION)
	LD_LIBRARY_PATH=$(PREFIX_PATH)/lib ldd $(ARTIFACTS_DIR)/nodejs/node_modules/sharp/build/Release/sharp-linux-x64.node | sed -nE "s/^[^\/\n]*(\/[^ ]+)(.*)/\1/p" | xargs cp -t $(ARTIFACTS_DIR)/lib

libwebp:
	curl -L https://github.com/webmproject/libwebp/archive/v$(WEBP_VERSION).tar.gz | tar zx
	cd libwebp-$(WEBP_VERSION) && ./autogen.sh && ./configure --enable-libwebpmux --prefix=$(PREFIX_PATH) && make V=0 && make install

libde265:
	# libde265
	curl -L https://github.com/strukturag/libde265/releases/download/v$(LIBDE265_VERSION)/libde265-$(LIBDE265_VERSION).tar.gz | tar zx
	cd libde265-$(LIBDE265_VERSION) && ./autogen.sh && ./configure --disable-dec265 --disable-sherlock265 --prefix=$(PREFIX_PATH) && make V=0 && make install

libheif: libde265
	# libheif
	curl -L https://github.com/strukturag/libheif/releases/download/v$(LIBHEIF_VERSION)/libheif-$(LIBHEIF_VERSION).tar.gz | tar zx
	cd libheif-$(LIBHEIF_VERSION) && ./scripts/install-ci-linux.sh && make V=0 && make install

libvips: installLib libheif libwebp
	# libvips
	curl -L https://github.com/libvips/libvips/releases/download/v$(VIPS_VERSION)/vips-$(VIPS_VERSION).tar.gz | tar zx
	cd vips-$(VIPS_VERSION) && ./configure --prefix=$(PREFIX_PATH) && make V=0 && make install

installLib:
	sudo apt-get update && \
	sudo apt-get install -y \
		libglib2.0-dev \
		libexpat1-dev \
		libjpeg-dev \
		libjpeg-turbo8-dev \
		libpng-dev \
		libgif-dev \
		libexif-dev \
		librsvg2-dev \
		libtiff-dev \
		liblcms2-dev
