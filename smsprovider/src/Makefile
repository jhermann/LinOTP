ECHO    = echo
PYTHON=`which python`
DESTDIR=/
BUILDIR=$(CURDIR)/debian/linotp
PROJECT=SMSProvider

all:
	@echo "make source - Create source package"
	@echo "make create - Create the source packages"
	@echo "make install - Install on local system"
	@echo "make buildrpm - Generate a rpm package"
	@echo "make builddeb - Generate a deb package"
	@echo "make clean - Get rid of scratch and byte files"
	@echo "make test_sms - test the SMS provider"
	@echo "make ppa-dev - Publish in ppa repository linotp-dev"

source:
	$(PYTHON) setup.py sdist $(COMPILE)

create:
	mkdir -p ../build
	make source
	cp dist/*.tar* ../build/

install:
	$(PYTHON) setup.py install --root $(DESTDIR) $(COMPILE)

buildrpm:
#	$(PYTHON) setup.py bdist_rpm --post-install=rpm/postinstall --pre-uninstall=rpm/preuninstall
	$(PYTHON) setup.py bdist_rpm

builddeb:
	# build the source package in the parent directory
	# then rename it to project_version.orig.tar.gz
	mkdir -p ../build
	$(PYTHON) setup.py sdist $(COMPILE) --dist-dir=../ 
	rename -f 's/$(PROJECT)-(.*)\.tar\.gz/$(PROJECT)_$$1\.orig\.tar\.gz/' ../*
	# build the package
	dpkg-buildpackage -i -I -rfakeroot
	mv ../linotp-smsprovider*.deb ../build/

clean:
	$(PYTHON) setup.py clean
	rm -rf build/ MANIFEST dist/
	find . -name '*.pyc' -delete
	#rm -r dist/
	#if [ -d dist ]; then rm -r dist/; fi
	rm -f ../linotp-smsprovider_*.deb
	rm -f ../linotp-smsprovider_*.dsc
	rm -f ../linotp-smsprovider_*.changes
	rm -f ../linotp-smsprovider_*.tar.gz
	rm -f ../linotp-smsprovider_*_source.build
	rm -f ../linotp-smsprovider_*_source.changes
	rm -f ../SMSProvider_*.tar.gz
	fakeroot $(MAKE) -f $(CURDIR)/debian/rules clean
	rm -rf ../build/

test_sms:
	(nosetests -v -x test/test_sms.py)

ppa-preprocess:
	$(PYTHON) setup.py sdist $(COMPILE) --dist-dir=../
	rename -f 's/$(PROJECT)-(.*)\.tar\.gz/$(PROJECT)_$$1\.orig\.tar\.gz/' ../*
	debuild -S

ppa-dev:
	make ppa-preprocess
	dput ppa:linotp/unstable ../linotp-smsprovider_*_source.changes
