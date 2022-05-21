# For COPR builds directly from git.
#
# https://docs.pagure.org/copr.copr/user_documentation.html#make-srpm
#

TARGET=NetworkManager-openconnect

srpm: /usr/bin/git
	git config --global --add safe.directory "*"
	./mkspec.sh $(TARGET)
	set -x; if grep -q "global gitsnapshot 1" $(TARGET).spec; then \
		tarprefix=$$(sed -n '/^%global snapcommit /s/.* //p' $(TARGET).spec); \
		tarname=$${tarprefix:0:7}; \
	else \
		tarprefix=$$(sed -n '/^%global tagver /s/.* //p' $(TARGET).spec); \
		tarname=$${tarprefix}; \
	fi; \
	git archive --prefix=$(TARGET)-$${tarname}/ HEAD -o .copr/$(TARGET)-$${tarname}.tar.gz
	rpmbuild -bs $(TARGET).spec --define "_sourcedir .copr" --define "_srcrpmdir $(outdir)"

/usr/bin/git:
	dnf install git

build: srpm
	rpmbuild -bb $(TARGET).spec --define "_sourcedir `pwd`/.copr"


