rpmdev-spectool:
	if rpm -q rpmdevtools; then echo "RPM 'rpmdevtools' is already installed"; else echo "RPM 'rpmdevtools' need to be installed first"; dnf install -y rpmdevtools; fi

cargo:
	if rpm -q cargo; then echo "RPM 'cargo' is already installed"; else echo "RPM 'cargo' need to be installed first"; dnf install -y cargo; fi

download: rpmdev-spectool
	rpmdev-spectool $(spec) -g

cargovendor: rpmdev-spectool cargo
	# retrieve package and version
	# extract toml+lock
	# cleanup vendor directory
	# download vendor packages
	# create tar.gz
	packagetgz=$$(rpmdev-spectool $(spec) -S -s 0 | awk -F/ '{ print $$NF }'); \
	echo "INFO: packagetgz=$$packagetgz"; \
	package=$$(basename $$packagetgz .tar.gz); \
	packageconv="$${package//_/-}"; \
	echo "INFO: package=$$package packageconv=$$packageconv"; \
	tar xzf $$packagetgz $$packageconv/Cargo.toml $$packageconv/Cargo.lock --strip-components=1; \
	cargo vendor; \
	tar czf vendor-$$package.tar.gz vendor

srpm:	download cargovendor
	if [ -n "$(outdir)" ]; then \
		rpmbuild -bs $(spec) --undefine=_disable_source_fetch --define "_topdir ." --define "_sourcedir ." --define "_srcrpmdir $(outdir)"; \
	else \
		rpmbuild -bs $(spec) --undefine=_disable_source_fetch --define "_topdir ." --define "_sourcedir ." --define "_srcrpmdir ."; \
	fi
