FROM ubuntu:22.04

# Build command
#  docker build -t ghcr.io/converged-computing/ensemble-osu .
#  docker push ghcr.io/converged-computing/ensemble-osu

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
RUN wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.2.tar.gz && \
    tar -xzvf openmpi-4.1.2.tar.gz && \
    cd openmpi-4.1.2 && \
    ./configure && \
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

RUN apt-get update && apt-get install -y openssh-client openssh-server cmake && apt-get clean
RUN echo "        LogLevel ERROR" >> /etc/ssh/ssh_config && \
    echo "        StrictHostKeyChecking no" >> /etc/ssh/ssh_config && \
    echo "        UserKnownHostsFile=/dev/null" >> /etc/ssh/ssh_config && \
    cd /root && \
    mkdir -p /run/sshd && \
    ssh-keygen -t rsa -N "" -f /root/.ssh/id_rsa && chmod og+rX . && \
    cd .ssh && cat id_rsa.pub > authorized_keys && chmod 644 authorized_keys
EXPOSE 22

WORKDIR /opt

# Prepare tutorial per
# https://ulhpc-tutorials.readthedocs.io/en/latest/parallel/mpi/OSU_MicroBenchmarks/

RUN git clone --depth 1 https://github.com/ULHPC/tutorials /opt/tutorials && \
    mkdir -p /opt/osu-benchmark && \
    cd /opt/osu-benchmark && \
    ln -s /opt/tutorials/parallel/mpi/OSU_MicroBenchmarks ref.d && \
    ln -s ref.d/Makefile . && \
    ln -s ref.d/scripts  . && \
    mkdir src && \
    cd src && \
    export OSU_VERSION=5.8 && \
    wget --no-check-certificate http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-${OSU_VERSION}.tgz && \
    tar xf osu-micro-benchmarks-${OSU_VERSION}.tgz && \
    cd /opt/osu-benchmark && \
    # Compile based on openmpi
    mkdir -p build.openmpi && cd build.openmpi && \
    ../src/osu-micro-benchmarks-${OSU_VERSION}/configure CC=mpicc CXX=mpicxx CFLAGS=-I$(pwd)/../src/osu-micro-benchmarks-${OSU_VERSION}/util --prefix=$(pwd) && \
    make && make install
