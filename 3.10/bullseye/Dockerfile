#
# NOTE: THIS DOCKERFILE IS GENERATED VIA "apply-templates.sh"
#
# PLEASE DO NOT EDIT IT DIRECTLY.
#

FROM buildpack-deps:bullseye
# [1/5] FROM docker.io/library/buildpack-deps:bullseye

# RUN env
# RUN env
# HOME=/root
# PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# ensure local python is preferred over distribution python
ENV PATH /usr/local/bin:$PATH

# cannot remove LANG even though https://bugs.python.org/issue19846 is fixed
# last attempted removal of LANG broke many users:
# https://github.com/docker-library/python/pull/570
ENV LANG C.UTF-8

# runtime dependencies
RUN set -eux; \
	apt-get update; \
	apt-get install -y --no-install-recommends \
		libbluetooth-dev \
		tk-dev \
		uuid-dev \
	; \
	rm -rf /var/lib/apt/lists/*
# [2/5] RUN
# set -eux;
# apt-get update;
# apt-get install -y --no-install-recommends libbluetooth-dev tk-dev uuid-dev;
# rm -rf /var/lib/apt/lists/*

ENV GPG_KEY A035C8C19219BA821ECEA86B64E628F8D684696D
ENV PYTHON_VERSION 3.10.14

RUN set -eux; \
	\
	wget -O python.tar.xz "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz"; \
	wget -O python.tar.xz.asc "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz.asc"; \
	GNUPGHOME="$(mktemp -d)"; export GNUPGHOME; \
	gpg --batch --keyserver hkps://keys.openpgp.org --recv-keys "$GPG_KEY"; \
	gpg --batch --verify python.tar.xz.asc python.tar.xz; \
	gpgconf --kill all; \
	rm -rf "$GNUPGHOME" python.tar.xz.asc; \
	mkdir -p /usr/src/python; \
	tar --extract --directory /usr/src/python --strip-components=1 --file python.tar.xz; \
	rm python.tar.xz; \
	\
	cd /usr/src/python; \
	gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)"; \
	./configure \
		--build="$gnuArch" \
		--enable-loadable-sqlite-extensions \
		--enable-optimizations \
		--enable-option-checking=fatal \
		--enable-shared \
		--with-lto \
		--with-system-expat \
		--without-ensurepip \
	; \
	nproc="$(nproc)"; \
	EXTRA_CFLAGS="$(dpkg-buildflags --get CFLAGS)"; \
	LDFLAGS="$(dpkg-buildflags --get LDFLAGS)"; \
	make -j "$nproc" \
		"EXTRA_CFLAGS=${EXTRA_CFLAGS:-}" \
		"LDFLAGS=${LDFLAGS:-}" \
		"PROFILE_TASK=${PROFILE_TASK:-}" \
	; \
# https://github.com/docker-library/python/issues/784
# prevent accidental usage of a system installed libpython of the same version
	rm python; \
	make -j "$nproc" \
		"EXTRA_CFLAGS=${EXTRA_CFLAGS:-}" \
		"LDFLAGS=${LDFLAGS:--Wl},-rpath='\$\$ORIGIN/../lib'" \
		"PROFILE_TASK=${PROFILE_TASK:-}" \
		python \
	; \
	make install; \
	\
# enable GDB to load debugging data: https://github.com/docker-library/python/pull/701
	bin="$(readlink -ve /usr/local/bin/python3)"; \
	dir="$(dirname "$bin")"; \
	mkdir -p "/usr/share/gdb/auto-load/$dir"; \
	cp -vL Tools/gdb/libpython.py "/usr/share/gdb/auto-load/$bin-gdb.py"; \
	\
	cd /; \
	rm -rf /usr/src/python; \
	\
	find /usr/local -depth \
		\( \
			\( -type d -a \( -name test -o -name tests -o -name idle_test \) \) \
			-o \( -type f -a \( -name '*.pyc' -o -name '*.pyo' -o -name 'libpython*.a' \) \) \
		\) -exec rm -rf '{}' + \
	; \
	\
	ldconfig; \
	\
	python3 --version

# [3/5] RUN
# set -eux;
# wget -O python.tar.xz "https://www.python.org/ftp/python/3.10.14/Python-3.10.14.tar.xz";
# wget -O python.tar.xz.asc "https://www.python.org/ftp/python/3.10.14/Python-3.10.14.tar.xz.asc";
# GNUPGHOME="$(mktemp -d)";
# export GNUPGHOME;
# gpg --batch --keyserver hkps://keys.openpgp.org --recv-keys "A035C8C19219BA821ECEA86B64E628F8D684696D";
# gpg --batch --verify python.tar.xz.asc python.tar.xz;
# gpgconf --kill all;
# rm -rf "$GNUPGHOME" python.tar.xz.asc;
# mkdir -p /usr/src/python;
# tar --extract --directory /usr/src/python --strip-components=1 --file python.tar.xz;
# rm python.tar.xz;
# cd /usr/src/python;
# gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)";
# ./configure --build="$gnuArch" --enable-loadable-sqlite-extensions --enable-optimizations --enable-option-checking=fatal --enable-shared --with-lto --with-system-expat --without-ensurepip;
# nproc="$(nproc)";
# EXTRA_CFLAGS="$(dpkg-buildflags --get CFLAGS)";
# LDFLAGS="$(dpkg-buildflags --get LDFLAGS)";
# make -j "$nproc" "EXTRA_CFLAGS=${EXTRA_CFLAGS:-}" "LDFLAGS=${LDFLAGS:-}" "PROFILE_TASK=${PROFILE_TASK:-}";
# rm python;
# make -j "$nproc" "EXTRA_CFLAGS=${EXTRA_CFLAGS:-}" "LDFLAGS=${LDFLAGS:--Wl},-rpath='$$ORIGIN/../lib'" "PROFILE_TASK=${PROFILE_TASK:-}" python;
# make install;
# bin="$(readlink -ve /usr/local/bin/python3)";
# dir="$(dirname "$bin")";
# mkdir -p "/usr/share/gdb/auto-load/$dir";
# cp -vL Tools/gdb/libpython.py "/usr/share/gdb/auto-load/$bin-gdb.py";
# cd /;
# rm -rf /usr/src/python;
# find /usr/local -depth ( ( -type d -a ( -name test -o -name tests -o -name idle_test ) ) -o ( -type f -a ( -name '*.pyc' -o -name '*.pyo' -o -name 'libpython*.a' ) ) ) -exec rm -rf '{}' +;
# ldconfig;
# python3 --version

# + wget -O python.tar.xz https://www.python.org/ftp/python/3.10.14/Python-3.10.14.tar.xz
# + wget -O python.tar.xz.asc https://www.python.org/ftp/python/3.10.14/Python-3.10.14.tar.xz.asc
# + mktemp -d
# + GNUPGHOME=/tmp/tmp.VKdwxd2Nwk
# + export GNUPGHOME
# + gpg --batch --keyserver hkps://keys.openpgp.org --recv-keys A035C8C19219BA821ECEA86B64E628F8D684696D
# + gpg --batch --verify python.tar.xz.asc python.tar.xz
# + gpgconf --kill all
# + rm -rf /tmp/tmp.VKdwxd2Nwk python.tar.xz.asc
# + mkdir -p /usr/src/python
# + tar --extract --directory /usr/src/python --strip-components=1 --file python.tar.xz
# + rm python.tar.xz
# + cd /usr/src/python
# + dpkg-architecture --query DEB_BUILD_GNU_TYPE
# + gnuArch=x86_64-linux-gnu
# + ./configure --build=x86_64-linux-gnu --enable-loadable-sqlite-extensions --enable-optimizations --enable-option-checking=fatal --enable-shared --with-lto --with-system-expat --without-ensurepip
# + nproc
# + nproc=4
# + dpkg-buildflags --get CFLAGS
# + EXTRA_CFLAGS=-g -O2 -ffile-prefix-map=/usr/src/python=. -fstack-protector-strong -Wformat -Werror=format-security
# + dpkg-buildflags --get LDFLAGS
# + LDFLAGS=-Wl,-z,relro
# + make -j 4 EXTRA_CFLAGS=-g -O2 -ffile-prefix-map=/usr/src/python=. -fstack-protector-strong -Wformat -Werror=format-security LDFLAGS=-Wl,-z,relro PROFILE_TASK=
# + rm python
# + make -j 4 EXTRA_CFLAGS=-g -O2 -ffile-prefix-map=/usr/src/python=. -fstack-protector-strong -Wformat -Werror=format-security LDFLAGS=-Wl,-z,relro,-rpath='$$ORIGIN/../lib' PROFILE_TASK= python
# + make install
# + readlink -ve /usr/local/bin/python3
# + bin=/usr/local/bin/python3.10
# + dirname /usr/local/bin/python3.10
# + dir=/usr/local/bin
# + mkdir -p /usr/share/gdb/auto-load//usr/local/bin
# + cp -vL Tools/gdb/libpython.py /usr/share/gdb/auto-load//usr/local/bin/python3.10-gdb.py
# + cd /
# + rm -rf /usr/src/python
# + find /usr/local -depth ( ( -type d -a ( -name test -o -name tests -o -name idle_test ) ) -o ( -type f -a ( -name *.pyc -o -name *.pyo -o -name libpython*.a ) ) ) -exec rm -rf {} +
# + ldconfig
# + python3 --version

# make some useful symlinks that are expected to exist ("/usr/local/bin/python" and friends)
RUN set -eux; \
	for src in idle3 pydoc3 python3 python3-config; do \
		dst="$(echo "$src" | tr -d 3)"; \
		[ -s "/usr/local/bin/$src" ]; \
		[ ! -e "/usr/local/bin/$dst" ]; \
		ln -svT "$src" "/usr/local/bin/$dst"; \
	done
# [4/5] RUN
# set -eux;
# for src in idle3 pydoc3 python3 python3-config; do
# 	dst="$(echo "$src" | tr -d 3)";
# 	[ -s "/usr/local/bin/$src" ];
# 	[ ! -e "/usr/local/bin/$dst" ];
# 	ln -svT "$src" "/usr/local/bin/$dst";
# done

# if this is called "PIP_VERSION", pip explodes with "ValueError: invalid truth value '<VERSION>'"
ENV PYTHON_PIP_VERSION 23.0.1
# https://github.com/docker-library/python/issues/365
ENV PYTHON_SETUPTOOLS_VERSION 65.5.1
# https://github.com/pypa/get-pip
ENV PYTHON_GET_PIP_URL https://github.com/pypa/get-pip/raw/ac00c61f60b2df101b7cdf90ed319b625ac93b42/public/get-pip.py
ENV PYTHON_GET_PIP_SHA256 0f8bb2652c0b0965f268312f49ec21e772d421d381af4324beea66b8acf2635c

RUN set -eux; \
	\
	wget -O get-pip.py "$PYTHON_GET_PIP_URL"; \
	echo "$PYTHON_GET_PIP_SHA256 *get-pip.py" | sha256sum -c -; \
	\
	export PYTHONDONTWRITEBYTECODE=1; \
	\
	python get-pip.py \
		--disable-pip-version-check \
		--no-cache-dir \
		--no-compile \
		"pip==$PYTHON_PIP_VERSION" \
		"setuptools==$PYTHON_SETUPTOOLS_VERSION" \
	; \
	rm -f get-pip.py; \
	\
	pip --version
# [5/5] RUN
# set -eux;
# wget -O get-pip.py "https://github.com/pypa/get-pip/raw/ac00c61f60b2df101b7cdf90ed319b625ac93b42/public/get-pip.py";
# echo "0f8bb2652c0b0965f268312f49ec21e772d421d381af4324beea66b8acf2635c *get-pip.py" | sha256sum -c -;
# export PYTHONDONTWRITEBYTECODE=1;
# python get-pip.py --disable-pip-version-check --no-cache-dir --no-compile "pip==23.0.1" "setuptools==65.5.1";
# rm -f get-pip.py;
# pip --version

# + wget -O get-pip.py https://github.com/pypa/get-pip/raw/ac00c61f60b2df101b7cdf90ed319b625ac93b42/public/get-pip.py
# + echo 0f8bb2652c0b0965f268312f49ec21e772d421d381af4324beea66b8acf2635c *get-pip.py
# + sha256sum -c -
# + export PYTHONDONTWRITEBYTECODE=1
# + python get-pip.py --disable-pip-version-check --no-cache-dir --no-compile pip==23.0.1 setuptools==65.5.1
# + rm -f get-pip.py
# + pip --version

# RUN env
# RUN env
# HOME=/root
# PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
# LANG=C.UTF-8
# GPG_KEY=A035C8C19219BA821ECEA86B64E628F8D684696D
# PYTHON_VERSION=3.10.14
# PYTHON_PIP_VERSION=23.0.1
# PYTHON_SETUPTOOLS_VERSION=65.5.1
# PYTHON_GET_PIP_URL=https://github.com/pypa/get-pip/raw/ac00c61f60b2df101b7cdf90ed319b625ac93b42/public/get-pip.py
# PYTHON_GET_PIP_SHA256=0f8bb2652c0b0965f268312f49ec21e772d421d381af4324beea66b8acf2635c

CMD ["python3"]
