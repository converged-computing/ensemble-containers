FROM ubuntu:22.04

# Build command
#  docker build -t ghcr.io/converged-computiong/ensemble-laghos:libfabric .
#  docker push ghcr.io/converged-computiong/ensemble-laghos:libfabric

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

ENV MAKE_CXX_FLAG="MPICXX=mpic++"

# Install hypre
RUN apt-get install -y libc6-dev && \
    export hypre_options="--disable-fortran --without-fei --enable-bigint" && \
    wget --no-verbose https://github.com/hypre-space/hypre/archive/v2.31.0.tar.gz && \
    tar -xzf v2.31.0.tar.gz && \
    mv hypre-2.31.0 hypre && \
    cd hypre/src && \
   ./configure ${hypre_options} CC=mpicc CXX=mpic++ && \
    make -j 4

# Metis
RUN wget --no-verbose https://github.com/mfem/tpls/raw/gh-pages/metis-4.0.3.tar.gz && \
    tar -xzf metis-4.0.3.tar.gz && \
    mv metis-4.0.3 metis-4.0 && \
    make -j 4 -C metis-4.0/Lib CC=mpicc OPTFLAGS="-Wno-error=implicit-function-declaration -O2"

RUN wget https://github.com/mfem/mfem/archive/refs/tags/v4.7.tar.gz && \
    tar -xzvf v4.7.tar.gz && \
    mv mfem-4.7 /opt/mfem && \
    cd /opt/mfem && \
    unset LD_LIBRARY_PATH && \
    make config MFEM_USE_MPI=YES MPICXX=mpiCC MFEM_MPI_NP=2 MFEM_DEBUG=${DEBUG} CPPFLAGS="${CPPFLAGS}" && \
    make -j 4
        
WORKDIR /opt
RUN wget https://github.com/CEED/Laghos/archive/refs/tags/v3.1.tar.gz && \ 
    tar -xzvf v3.1.tar.gz && \
    mv Laghos-3.1 /opt/laghos && \
    cd /opt/laghos && \
    make -j 4

EXPOSE 22
WORKDIR /opt/laghos

# Finally, install oras for saving artifacts
RUN export VERSION="1.1.0" && \
    curl -LO "https://github.com/oras-project/oras/releases/download/v${VERSION}/oras_${VERSION}_linux_amd64.tar.gz" && \
    mkdir -p oras-install/ && \
    tar -zxf oras_${VERSION}_*.tar.gz -C oras-install/ && \
    mv oras-install/oras /usr/local/bin/ && \
    rm -rf oras_${VERSION}_*.tar.gz oras-install/
    
# Ensure we add path where libfabric is!
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
