include ../../Makefile.include PYTHONMODULE-LXML-VERSION ../../download-files.include
DEPS= ../../Makefile.include Makefile PYTHONMODULE-LXML-VERSION ../../download-files.include

LDSHARED=$(CC) -shared

ifeq ($(OS),android)
  LDFLAGS+= -L$(PREFIX)/lib/dummy-lib$(APP_NAME)/ -l$(APP_NAME) -lm
endif

ifeq (darwin, $(findstring darwin, $(HOST)))
  ifeq ($(CPU),arm64)
    CFLAGS+= -target arm64-apple-darwin
    LDFLAGS+= -target arm64-apple-darwin
  endif
  LDSHARED:=$(CC) -bundle -undefined dynamic_lookup $(LDFLAGS)
endif

export CC CFLAGS AR RANLIB
export LDSHARED LDFLAGS
export PYTHONPATH=$(PYTHON_SITE_PKG)
export SETUPTOOLS_EXT_SUFFIX=.so
export PKG_CONFIG_PATH=$(PREFIX)/lib/pkgconfig

BUILD_OPTS=--plat-name $(OS)-$(CPU) --static

all: .installed-$(PLATFORM)


.configured-$(PLATFORM): $(DEPS) | $(TARBALLS_LOCATION)/$(ARCHIVE).$(HASH_TYPE)
	rm -rf $(PLATFORM)/*; mkdir -p $(PLATFORM)
	cd $(PLATFORM); $(ARCHIVE_TOOL) $(ARCHIVE_TOOL_FLAGS) $(TARBALLS_LOCATION)/$(ARCHIVE)
	touch $@

.installed-$(PLATFORM): .configured-$(PLATFORM)
	cd $(PLATFORM); $(NATIVEPREFIX)/bin/python3 setup.py build_ext $(BUILD_OPTS)
	cd $(PLATFORM); $(NATIVEPREFIX)/bin/python3 setup.py install --prefix=$(PREFIX)
	touch $@

clean:
	$(MAKE) -C $(PLATFORM) clean
	rm -f .installed-$(PLATFORM) .configured-$(PLATFORM)

distclean::
	rm -rf $(PLATFORM) .installed-$(PLATFORM) .configured-$(PLATFORM)
