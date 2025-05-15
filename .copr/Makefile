rpmdev-spectool:
	if rpm -q rpmdevtools; then echo "RPM rpmdevtools is already installed"; else echo "RPM rpmdevtools need to be installed first"; dnf install -y rpmdevtools; fi

curl:
	if rpm -q curl; then echo "RPM curl is already installed"; else echo "RPM curl need to be installed first"; dnf install -y curl; fi

download: rpmdev-spectool curl
	rpmdev-spectool $(spec) | awk '$$1 ~ /^Source/ && $$2 ~ /^http/ { print $$2 }' | while read url; do \
		file=$$(basename $$url); \
		package=$$(echo $$file | sed 's/-[0-9].*//g'); \
		echo "INFO  : download source  $$file"; \
		if [ ! -e $$file ]; then \
			echo "NOTICE: download source: $$url (MISSING)"; \
			download=1; \
		fi; \
		if [ $$download -eq 1 ]; then \
			echo "INFO  : download source: $$url to file $$file"; \
			curl -s -o $$file $$url; \
			r=$$?; \
			if [ $$r -eq 0 ]; then \
				echo "INFO  : download source: $$url (SUCCESS)"; \
			else \
				echo "ERROR : download source: $$url (ERROR)"; \
				exit 1; \
			fi; \
		fi; \
	done

srpm:	download
	if [ -n "$(outdir)" ]; then \
		rpmbuild -bs $(spec) --undefine=_disable_source_fetch --define "_topdir ." --define "_sourcedir ." --define "_srcrpmdir $(outdir)"; \
	else \
		rpmbuild -bs $(spec) --undefine=_disable_source_fetch --define "_topdir ." --define "_sourcedir ." --define "_srcrpmdir ."; \
	fi
