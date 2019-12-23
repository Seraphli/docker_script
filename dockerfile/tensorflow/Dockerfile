FROM nvidia/cuda:10.1-cudnn7-devel-ubuntu18.04
CMD ["bash"]
RUN apt-get update \
   &&  apt-get install -y --no-install-recommends ca-certificates curl netbase wget \
   &&  rm -rf /var/lib/apt/lists/*
RUN set -ex; if ! command -v gpg > /dev/null; then apt-get update; apt-get install -y --no-install-recommends gnupg dirmngr ; rm -rf /var/lib/apt/lists/*; fi
RUN apt-get update \
   &&  apt-get install -y --no-install-recommends git mercurial openssh-client subversion procps \
   &&  rm -rf /var/lib/apt/lists/*
RUN set -ex; apt-get update; apt-get install -y --no-install-recommends autoconf automake bzip2 dpkg-dev file g++ gcc imagemagick libbz2-dev libc6-dev libcurl4-openssl-dev libdb-dev libevent-dev libffi-dev libgdbm-dev libglib2.0-dev libgmp-dev libjpeg-dev libkrb5-dev liblzma-dev libmagickcore-dev libmagickwand-dev libmaxminddb-dev libncurses5-dev libncursesw5-dev libpng-dev libpq-dev libreadline-dev libsqlite3-dev libssl-dev libtool libwebp-dev libxml2-dev libxslt-dev libyaml-dev make patch unzip xz-utils zlib1g-dev $( if apt-cache show 'default-libmysqlclient-dev' 2>/dev/null | grep -q '^Version:'; then echo 'default-libmysqlclient-dev'; else echo 'libmysqlclient-dev'; fi ) ; rm -rf /var/lib/apt/lists/*
ENV PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ENV LANG=C.UTF-8
RUN apt-get update \
   &&  DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends tk-dev uuid-dev \
   &&  rm -rf /var/lib/apt/lists/*
ENV GPG_KEY=0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D
ENV PYTHON_VERSION=3.7.5
ARG PYTHON_MIRROR
RUN set -ex \
   &&  wget -O python.tar.xz "${PYTHON_MIRROR:-https://www.python.org/ftp/}python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz" \
   &&  wget -O python.tar.xz.asc "${PYTHON_MIRROR:-https://www.python.org/ftp/}python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz.asc" \
   &&  export GNUPGHOME="$(mktemp -d)" \
   &&  gpg --batch --keyserver ipv4.pool.sks-keyservers.net --recv-keys "$GPG_KEY" \
   &&  gpg --batch --verify python.tar.xz.asc python.tar.xz \
   &&  { command -v gpgconf > /dev/null \
   &&  gpgconf --kill all || :; } \
   &&  rm -rf "$GNUPGHOME" python.tar.xz.asc \
   &&  mkdir -p /usr/src/python \
   &&  tar -xJC /usr/src/python --strip-components=1 -f python.tar.xz \
   &&  rm python.tar.xz \
   &&  cd /usr/src/python \
   &&  gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" \
   &&  ./configure --build="$gnuArch" --enable-loadable-sqlite-extensions --enable-optimizations --enable-shared --with-system-expat --with-system-ffi \
   &&  make -j "$(nproc)" PROFILE_TASK='-m test.regrtest --pgo test_array test_base64 test_binascii test_binhex test_binop test_bytes test_c_locale_coercion test_class test_cmath test_codecs test_compile test_complex test_csv test_decimal test_dict test_float test_fstring test_hashlib test_io test_iter test_json test_long test_math test_memoryview test_pickle test_re test_set test_slice test_struct test_threading test_time test_traceback test_unicode ' \
   &&  make install \
   &&  ldconfig \
   &&  find /usr/local -depth \( \( -type d -a \( -name test -o -name tests -o -name idle_test \) \) -o \( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \) -exec rm -rf '{}' + \
   &&  rm -rf /usr/src/python \
   &&  python3 --version
RUN cd /usr/local/bin \
   &&  ln -s idle3 idle \
   &&  ln -s pydoc3 pydoc \
   &&  ln -s python3 python \
   &&  ln -s python3-config python-config
ENV PYTHON_PIP_VERSION=19.3.1
ARG PYPI_MIRROR
RUN set -ex; ${PYPI_MIRROR:+pip3 config set global.index-url $PYPI_MIRROR}; pip3 install "pip==$PYTHON_PIP_VERSION"; pip --version
RUN pip install tensorflow==1.15
CMD ["python3"]
