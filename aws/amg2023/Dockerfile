FROM ubuntu:22.04

# Build command
#  docker build -t ghcr.io/rse-ops/metric-amg:libfabric-09-2024 .
#  docker push ghcr.io/rse-ops/metric-amg:libfabric-09-2024

# Compilers, autotools
RUN apt-get update && \
    apt-get -qq install -y --no-install-recommends \
        apt-utils \
        autotools-dev \
        autoconf \
        automake \
        bzip2 \
        build-essential \
        ca-certificates \
        clang \
        clang-tidy \
        curl \
        dnsutils \
        gcc \
        g++ \
        git \
        gfortran \
        jq \ 
        libibverbs-dev \
        librdmacm-dev \
        libtool \
	libffi-dev \
        locales \
        make \
        pkg-config \
        python3-dev \
        python3-pip \
        rdma-core \
        sudo \
        strace \
        telnet \
        unzip \
        wget && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN locale-gen en_US.UTF-8
ENV LANG=C.UTF-8

WORKDIR /opt
RUN git clone -b v1.21.1 https://github.com/ofiwg/libfabric.git /opt/libfabric && \
    cd /opt/libfabric && ./autogen.sh && \
    ./configure --enable-efa=yes --enable-tcp=yes --enable-udp=yes --enable-sockets=yes --enable-verbs=yes --enable-shm=yes --enable-mrail=yes --enable-rxd=yes --enable-rxm=yes && \
    make -j 4 && make install

RUN wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.2.tar.gz && \
    tar -xzvf openmpi-4.1.2.tar.gz && \
    cd openmpi-4.1.2 && \
    ./configure --with-ofi=/usr/local && \
    make -j 4 && make install && ldconfig

ENV PATH=/usr/local/pancakes/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ENV LD_LIBRARY_PATH=/usr/local/pancakes/lib:$LD_LIBRARY_PATH
 
# Clone hypre - note there is a message about --with-unified-memory
RUN git clone --recursive https://github.com/hypre-space/hypre && \
    cd hypre/src && \
    ./configure --prefix=/usr/local/pancakes --enable-maxint && \
    make -j && make install

RUN git clone --recursive https://github.com/LLNL/AMG2023 amg && \
    cd amg && \ 
    mpicc -O2 -DHAVE_CONFIG_H -I. -I/opt/hypre/src/hypre/include -I/usr/local/cuda/include    -DHYPRE_TIMING -c amg.c && \
    mpic++ -o amg amg.o  -L/opt/hypre/src/hypre/lib -lHYPRE -Wl,-rpath,/opt/hypre/src/hypre/lib -lm -Wl,-rpath,/opt/hypre/src/hypre/lib && \
    mv ./amg /usr/local/bin/amg

EXPOSE 22
