FROM ubuntu:22.04

# Build command
#  docker build -t ghcr.io/converged-computing/ensemble-mt-gemm:libfabric .
#  docker push ghcr.io/converged-computing/ensemble-mt-gemm:libfabric

# Compilers, autotools
ENV DEBIAN_FRONTEND=noninteractive
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

# Finally, install oras for saving artifacts
RUN export VERSION="1.1.0" && \
    curl -LO "https://github.com/oras-project/oras/releases/download/v${VERSION}/oras_${VERSION}_linux_amd64.tar.gz" && \
    mkdir -p oras-install/ && \
    tar -zxf oras_${VERSION}_*.tar.gz -C oras-install/ && \
    mv oras-install/oras /usr/local/bin/ && \
    rm -rf oras_${VERSION}_*.tar.gz oras-install/

ENV PATH=/usr/local/pancakes/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ENV LD_LIBRARY_PATH=/usr/local/pancakes/lib:/usr/local/lib

WORKDIR /opt

RUN git clone https://repository.prace-ri.eu/git/CodeVault/hpc-kernels/dense_linear_algebra.git
RUN apt-get update && apt-get install -y cmake && apt-get clean && rm -rf /var/lib/apt/lists/*
COPY gemm_mpi.cpp /opt/dense_linear_algebra/gemm/mpi/src/
RUN cd /opt/dense_linear_algebra/gemm/mpi && \
    mkdir build && cd build && \
    cmake ../ && make && make install
WORKDIR /opt/dense_linear_algebra/gemm/mpi
