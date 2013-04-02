.DEFAULT: help
.PRECIOUS: build/hostname-setup-%

help:
	@echo 'Usage: make hostname-setup-$$version.deb'

hostname-setup-%.deb: build/hostname-setup-%
	rm -f '$@'
	dpkg --build 'build/hostname-setup-$*'
	mv 'build/$@' '$@'

build/hostname-setup-%: .maintainer
	rm -rf '$@'
	mkdir -p '$@'
	cp -r ../source/* '$@'
	find '$@' -maxdepth 1 -not -type d | xargs rm -f
	find '$@' -type f -name .gitignore | xargs rm -f
	cp -r prototype/* '$@'
	sed -i -e "s/^Version:.*/Version: $*/" \
	       -e "s/^Maintainer:.*/Maintainer: `cat .maintainer | xargs echo`/" \
	    '$@/DEBIAN/control'
	cd '$@' && ls \
	|   grep -v '^\(DEBIAN\)$$' \
	|   xargs -I{} find {} -type f \
	|   xargs md5sum > DEBIAN/md5sums

.maintainer:
	@echo -n 'Please enter your GPG identity: '
	@read IDENTITY; echo "$$IDENTITY" > .maintainer

distclean: clean
	rm -f .maintainer

clean:
	rm -rf build
	rm -f *.deb

