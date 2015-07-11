VERSION=2.5
PACKAGE=Auth_Yubico
CODE=COPYING NEWS README Yubico.php package.xml demo.php
EXAMPLE=example/admin.php example/authenticate.php example/config.php	\
	example/db.sql example/debug.php example/img_press.jpg		\
	example/index.html example/Modhex_Calculator.php		\
	example/Modhex.php example/one_factor.php example/style.css	\
	example/two_factor_legacy.php example/two_factor.php		\
	example/yubicoLogo.jpg example/yubikey.jpg			\
	example/yubiright_16x16.gif

all: sync-version $(PACKAGE)-$(VERSION).tgz

$(PACKAGE)-$(VERSION).tgz: $(CODE) $(EXAMPLE)
	mkdir $(PACKAGE)-$(VERSION) $(PACKAGE)-$(VERSION)/example
	cp $(CODE) $(PACKAGE)-$(VERSION)
	cp $(EXAMPLE) $(PACKAGE)-$(VERSION)/example
	tar cfz $(PACKAGE)-$(VERSION).tgz $(PACKAGE)-$(VERSION)
	rm -rf $(PACKAGE)-$(VERSION)

.PHONY: sync-version
sync-version:
	cat package.xml | perl -p -e "s,<release>[0-9.]+</release>,<release>"$(VERSION)"</release>," > tmp && \
	if ! cmp package.xml tmp; then \
		mv tmp package.xml; \
	else \
		rm tmp; \
	fi
	cat README | perl -p -e "s,Auth_Yubico-[0-9.]+.tgz,Auth_Yubico-"$(VERSION)".tgz," > tmp && \
	if ! cmp README tmp; then \
		mv tmp README; \
	else \
		rm tmp; \
	fi

clean:
	rm -f *~
	rm -rf $(PACKAGE)-$(VERSION)

PROJECT=php-yubico

release:
	@head -3 NEWS | grep -q "Version $(VERSION) released `date -I`" || \
		(echo 'error: update date/version in $(srcdir)/NEWS'; exit 1)
	@if test ! -d "$(YUBICO_WWW_REPO)"; then \
		echo "Yubico www repo not found!"; \
		echo "Make sure that YUBICO_WWW_REPO is set"; \
		exit 1; \
	fi
	make
	gpg --detach-sign $(PACKAGE)-$(VERSION).tgz
	gpg --verify $(PACKAGE)-$(VERSION).tgz.sig
	git push
	git tag -m $(VERSION) $(PACKAGE)-$(VERSION)
	git push --tags
	$(YUBICO_WWW_REPO)/publish $(PROJECT) $(VERSION) $(PACKAGE)-$(VERSION).tgz*
