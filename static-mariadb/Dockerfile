FROM notcompsky/static:latest AS intermediate

COPY static-connector.patch /static-connector.patch

RUN mkdir /git \
	&& cd /git \
	\
	&& git clone --depth 1 https://github.com/madler/zlib \
	&& git clone --depth 1 https://github.com/libunwind/libunwind \
	&& git clone --depth 1 https://github.com/google/glog \
	&& git clone --depth 1 https://github.com/google/double-conversion \
	&& git clone --depth 1 https://github.com/lz4/lz4 \
	&& git clone --depth 1 https://github.com/google/snappy \
	&& git clone --depth 1 https://github.com/openssl/openssl \
	&& git clone --depth 1 https://github.com/jedisct1/libsodium \
	&& git clone --depth 1 https://github.com/fmtlib/fmt \
	&& git clone --depth 1 https://github.com/gflags/gflags \
	&& git clone --depth 1 https://github.com/libevent/libevent \
	&& git clone --depth 1 https://github.com/curl/curl \
	&& git clone --depth 1 https://github.com/facebook/zstd \
	&& git clone --depth 1 https://github.com/ARMmbed/mbedtls \
	&& git clone --depth 1 https://github.com/Tencent/rapidjson \
	&& git clone https://github.com/mariadb-corporation/mariadb-connector-c \
	\
	&& mv /git/rapidjson/include/rapidjson /usr/include/rapidjson \
	\
	&& cd /git/zlib \
	&& cmake \
		-DCMAKE_BUILD_TYPE=Release \
		-DAMD64=ON \
		. \
	&& make zlibstatic \
	&& mv ./libz.a /usr/local/lib/ \
	&& mkdir /usr/local/include/zlib \
	&& mv *.h /usr/local/include/zlib/ \
	\
	&& cd /git/libunwind \
	&& ./autogen.sh \
	&& ./configure --disable-tests --disable-documentation \
	&& make install \
	\
	&& cd /git/glog \
	&& cmake \
		-Wno-dev -DWITH_GFLAGS=OFF -DBUILD_SHARED_LIBS=OFF -DWITH_UNWIND=OFF -DWITH_PKGCONFIG=OFF -DWITH_SYMBOLIZE=OFF \
		-DCMAKE_EXE_LINKER_FLAGS=-static \
		-DCMAKE_BUILD_TYPE=Release \
		. \
	&& make install \
	\
	&& cd /git/double-conversion \
	&& cmake -DBUILD_TESTING=OFF . \
	&& make install \
	\
	&& cd /git/lz4/contrib/cmake_unofficial \
	&& cmake -DBUILD_SHARED_LIBS=OFF -DBUILD_STATIC_LIBS=ON . \
	&& make install \
	\
	&& cd /git/snappy \
	&& cmake \
		-DBUILD_SHARED_LIBS=OFF \
		-DSNAPPY_BUILD_TESTS=OFF \
		-DSNAPPY_INSTALL=ON \
		-DCMAKE_BUILD_TYPE=Release \
		. \
	&& make install \
	\
	&& cd /git/openssl \
	&& ./config no-ssl2 no-err no-psk no-srp -static --static \
	&& make install_sw \
	\
	&& cd /git/libsodium \
	&& ./autogen.sh -s \
	&& ./configure \
		--with-pic \
		--disable-pie \
		--enable-static \
		--disable-pie \
		--disable-debug \
		--disable-opt \
	&& make install \
	\
	&& cd /git/fmt \
	&& cmake \
		-DFMT_DOC=OFF -DFMT_INSTALL=ON -DFMT_TEST=OFF -DFMT_FUZZ=OFF -DFMT_CUDA_TEST=OFF \
		-DBUILD_SHARED_LIBS=OFF \
		. \
	&& make install \
	\
	&& mkdir /git/mbedtls/build_dir \
	&& cd /git/mbedtls/build_dir \
	&& cmake \
		-DENABLE_TESTING=OFF \
		-DUSE_SHARED_MBEDTLS_LIBRARY=OFF \
		-DENABLE_PROGRAMS=OFF \
		-DCMAKE_BUILD_TYPE=Release \
		.. \
	&& make install \
	\
	&& cd /git/gflags \
	&& cmake \
		-DBUILD_STATIC_LIBS=ON \
		-DBUILD_SHARED_LIBS=OFF \
		-DINSTALL_HEADERS=ON \
		-DINSTALL_SHARED_LIBS=OFF \
		-DBUILD_gflags_nothreads_LIB=OFF \
		-DGFLAGS_BUILD_TESTING=OFF \
		-DGFLAGS_BUILD_PACKAGING=OFF \
		-DGFLAGS_BUILD_STATIC_LIBS=ON \
		-DGFLAGS_BUILD_SHARED_LIBS=OFF \
		-DBUILD_TESTING=OFF \
		-DCMAKE_BUILD_TYPE=Release \
		. \
	&& make install \
	\
	&& cd /git/libevent \
	&& cmake \
		-DEVENT__DISABLE_BENCHMARK=ON \
		-DEVENT__DISABLE_TESTS=ON \
		-DEVENT__DISABLE_REGRESS=ON \
		-DEVENT__DISABLE_SAMPLES=ON \
		-DEVENT__LIBRARY_TYPE=STATIC \
		-DEVENT__ENABLE_GCC_FUNCTION_SECTIONS=ON \
		. \
	&& make install \
	\
	&& cd /git/curl \
	&& cmake \
		-DBUILD_CURL_EXE=OFF -DBUILD_SHARED_LIBS=OFF -DCURL_LTO=ON \
		-DCURL_DISABLE_LDAP=ON -DCURL_DISABLE_TELNET=ON -DCURL_DISABLE_DICT=ON -DCURL_DISABLE_FILE=ON -DCURL_DISABLE_TFTP=ON -DCURL_DISABLE_LDAPS=ON -DCURL_DISABLE_RTSP=ON -DCURL_DISABLE_PROXY=ON -DCURL_DISABLE_POP3=ON -DCURL_DISABLE_IMAP=ON -DCURL_DISABLE_SMTP=ON -DCURL_DISABLE_GOPHER=ON \
		-DCURL_DISABLE_COOKIES=ON -DCURL_DISABLE_VERBOSE_STRINGS=ON -DENABLE_IPV6=OFF \
		-DENABLE_MANUAL=OFF \
		-DENABLE_UNIX_SOCKETS=OFF \
		. \
	&& make install \
	\
	&& cd /git/zstd/build/cmake \
	&& cmake \
		-DZSTD_BUILD_PROGRAMS=OFF \
		-DBUILD_TESTING=OFF \
		-DZSTD_BUILD_TESTS=OFF \
		-DZSTD_PROGRAMS_LINK_SHARED=OFF \
		\
		-DZSTD_BUILD_STATIC=ON \
		-DZSTD_BUILD_SHARED=OFF \
		. \
	&& make install \
	\
	&& cd /git/mariadb-connector-c \
	&& for f in service_sha2; do \
		curl -s "https://raw.githubusercontent.com/MariaDB/server/10.5/include/mysql/$f.h" > "include/mysql/$f.h"; \
	done \
	\
	&& git apply /static-connector.patch \
	\
	&& cmake \
		-DWITH_MYSQLCOMPAT=ON \
		-DWITH_UNIT_TESTS=OFF \
		-DCMAKE_BUILD_TYPE=Release \
		-DCMAKE_C_STANDARD_INCLUDE_DIRECTORIES=/server/include \
		\
		-DCLIENT_PLUGIN_CLIENT_ED25519=STATIC \
		-DCLIENT_PLUGIN_CACHING_SHA2_PASSWORD=STATIC \
		-DCLIENT_PLUGIN_SHA256_PASSWORD=STATIC \
		-DCLIENT_PLUGIN_AUTH_GSSAPI_CLIENT=STATIC \
		-DCLIENT_PLUGIN_MYSQL_OLD_PASSWORD=STATIC \
		-DCLIENT_PLUGIN_MYSQL_CLEAR_PASSWORD=STATIC \
		\
		-DCLIENT_PLUGIN_AURORA=STATIC \
		-DCLIENT_PLUGIN_DIALOG=STATIC \
		\
		-DCLIENT_PLUGIN_REMOTE_IO=STATIC \
		\
		-DCLIENT_PLUGIN_PVIO_SOCKET=STATIC \
		-DCLIENT_PLUGIN_PVIO_NPIPE=STATIC \
		-DCLIENT_PLUGIN_PVIO_SHMEM=STATIC \
		\
		. \
	&& make install \
	\
	&& rm -rf /git
