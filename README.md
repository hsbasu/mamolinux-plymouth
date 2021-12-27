# Mamolinux Plymouth
Ubuntu-like Plymouth themes for Mamolinux. Replaces Ubuntu Logo with Mamolinux Logo

## ToDo
1. [ ] Add mamolinux-text theme

## Building from source
Building plymouth theme from source is a bit tricky. The way I do it for **MamoLinux Focal** is as follows:
1. Create a working directory
	```
	mkdir plymouth
	```
2. Get the source from ubuntu
	```
	git clone https://git.launchpad.net/ubuntu/+source/plymouth
	mv plymouth mamolinux-plymouth
	wget https://launchpad.net/ubuntu/+archive/primary/+sourcefiles/plymouth/0.9.4git20200323-0ubuntu6/plymouth_0.9.4git20200323.orig.tar.xz
	```
3. Prepare a working branch
	```
	cd plymouth
	git checkout applied/ubuntu/focal
	git checkout -b mamolinux/focal
	```
4. Modify the sources:
	1. Put `mamolinux-logo.png` in `themes/ubuntu-logo` and **delete** `ubuntu-logo.png`
	2. Replace `ubuntu-logo.png` with `mamolinux-logo.png` in
		```
		debian/local/plymouth.hook#L120-L125		# look at Lines between 120 and 125
		debian/patches/ubuntu-logo.patch#(L10-L15,L235-L240)
		debian/rules#(L40-L45,L80-L90)
		debian/source/include-binaries#L1-L5
		themes/ubuntu-logo/Makefile.am#L1-L5
		themes/ubuntu-logo/ubuntu-logo.script#L185-L190
		```
5. Commit the changes
	```
	git add debian/local/plymouth.hook\
			 debian/patches/ubuntu-logo.patch\
			 debian/rules\
			 debian/source/include-binaries\
			 themes/ubuntu-logo/Makefile.am\
			 themes/ubuntu-logo/mamolinux-logo.png
			 themes/ubuntu-logo/ubuntu-logo.png\
			 themes/ubuntu-logo/ubuntu-logo.script
	git commit
	dch -D focal -l mamolinux	# Increase version number with Myself as Source Changer
	git commit -m "plymouth_0.9.4git20200323-0ubuntu6mamolinux1"
	```
6. Build packages using `pbuilder`. [Check this]() on how to build using `pbuilder`.
	1. Copy `mamolinux-plymouth` and `plymouth_0.9.4git20200323.orig.tar.xz` to `~/pbuilder`
	2. Open terminal at `~/pbuilder/mamolinux-plymouth`
	3. Build locally:
		1. Install build-depends. Check `debian/control` for dependency packages.
		2. run `dpkg-buildpackage --no-sign`
		
		This will create `*.deb` files at `~/pbuilder`
	4. Build using **Launchpad PPA**. Copy paste the following commands one-by-one:
		```
		debuild -S -d -us -uc	# creates unsigned sources
		pbuilder-dist focal build ../plymouth_0.9.420200323-0ubuntu6.dsc	# Grenerates deb packages for local testing
		debuild -S -sa -d	# creates signed sources for uploading to Launchpad
		cd ..
		dput ppa:mamolinux/daily-builds plymouth_0.9.4git20200323-0ubuntu6mamolinux1_source.changes
7. After a few minutes `plymouth_0.9.4git20200323-0ubuntu6mamolinux1` will appear as **building** [here](https://launchpad.net/~mamolinux/+archive/ubuntu/daily-builds/+packages)