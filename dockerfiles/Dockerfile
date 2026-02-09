FROM ghcr.io/taffish-org/debian:12

LABEL maintainer="taffish@163.com"

WORKDIR /root

RUN apt-get update && apt-get install -y curl git gcc g++ make cmake python3-dev python3-pip python3-venv \
                                         libsqlite3-dev liblmdb-dev libzstd-dev libbz2-dev zlib1g-dev \
                                         mcl libssl-dev flex bison libgmp3-dev unzip \
                                         automake autoconf libtool libtool-bin

RUN cd / && git clone https://github.com/OrthoFinder/OrthoFinder.git && cd /OrthoFinder/ && \
    python3 -m venv of3 && . /OrthoFinder/of3/bin/activate && pip install .

ENV PATH=/OrthoFinder/of3/bin:${PATH}

# automake :: 1.18
RUN cd / && curl -LO https://ftp.gnu.org/gnu/automake/automake-1.18.1.tar.gz && \
    tar -zxvf /automake-1.18.1.tar.gz && rm /automake-1.18.1.tar.gz && cd /automake-1.18.1/ && \
    ./configure && make && make install

ENV PATH=/automake-1.18.1/bin:${PATH}

# autoconf :: 1.18
RUN cd / && curl -LO https://ftp.gnu.org/gnu/autoconf/autoconf-2.72.tar.gz && \
    tar -zxvf /autoconf-2.72.tar.gz && rm /autoconf-2.72.tar.gz && cd /autoconf-2.72/ && \
    ./configure && make && make install

ENV PATH=/autoconf-2.72/bin:${PATH}

# libtool configs
RUN mkdir -p /usr/local/share/aclocal && \
    cp -a /usr/share/aclocal/libtool.m4 /usr/local/share/aclocal/ && \
    cp -a /usr/share/aclocal/lt*.m4 /usr/local/share/aclocal/

# BLAST :: 2.17.0
RUN cd / && curl -LO https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/ncbi-blast-2.17.0+-src.tar.gz && \
    tar -zxvf /ncbi-blast-2.17.0+-src.tar.gz && rm ncbi-blast-2.17.0+-src.tar.gz && cd /ncbi-blast-2.17.0+-src/c++/ && \
    ./configure && cd /ncbi-blast-2.17.0+-src/c++/ReleaseMT/build/ && make all_r

ENV PATH=/ncbi-blast-2.17.0+-src/c++/ReleaseMT/bin:${PATH}

# diamond :: 2.1.22
RUN cd / && curl -LO https://github.com/bbuchfink/diamond/archive/refs/tags/v2.1.22.tar.gz && \
    tar -zxvf /v2.1.22.tar.gz && rm /v2.1.22.tar.gz && cd /diamond-2.1.22/ && \
    mkdir /diamond-2.1.22/bin && cd /diamond-2.1.22/bin/ && \
    cmake .. && make -j $(nproc --all) && make install

# fastme :: 2.1.6.4
RUN cd / && git clone https://github.com/djhshih/fastme.git && cd /fastme/ && \
    ./configure && make

ENV PATH=/fastme/src:${PATH}

# MMseqs2
RUN cd / && curl -LO https://github.com/Kitware/CMake/archive/refs/tags/v3.31.5.tar.gz && \
    tar -zxvf /v3.31.5.tar.gz && rm /v3.31.5.tar.gz && cd /CMake-3.31.5/ && \
    /CMake-3.31.5/bootstrap && make && make install && cp /CMake-3.31.5/bin/cmake /usr/local/bin/

RUN cd / && curl -LO https://github.com/soedinglab/MMseqs2/archive/refs/tags/17-b804f.tar.gz && \
    tar -zxvf /17-b804f.tar.gz && rm /17-b804f.tar.gz && cd /MMseqs2-17-b804f/ && \
    mkdir /MMseqs2-17-b804f/build/ && cd /MMseqs2-17-b804f/build/ && \
    if [ "$(uname -m)" = "aarch64" ]; then cmake -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=. -DHAVE_ARM8=1 ..; else cmake -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=. ..; fi && \
    make -j8 && make install

ENV PATH=/MMseqs2-17-b804f/build/bin:/CMake-3.31.5/bin:${PATH}

# famsa :: 2.4.1
RUN cd / && mkdir /famsa && cd /famsa && \
    if [ "$(uname -m)" = "aarch64" ]; then curl -LO https://github.com/refresh-bio/FAMSA/releases/download/v2.4.1/famsa-v2.4.1-arm64_linux.tar.gz ; else curl -LO https://github.com/refresh-bio/FAMSA/releases/download/v2.4.1/famsa-v2.4.1-x64_linux.tar.gz ; fi && \
    tar -zxvf /famsa/famsa-v2.4.1-*.tar.gz && rm /famsa/famsa-v2.4.1-*.tar.gz

ENV PATH=/famsa:${PATH}

# mafft
RUN cd / && curl -LO https://mafft.cbrc.jp/alignment/software/mafft-7.525-with-extensions-src.tgz && \
    tar -zxvf /mafft-7.525-with-extensions-src.tgz && rm /mafft-7.525-with-extensions-src.tgz && \
    cd /mafft-7.525-with-extensions/core/       && make clean && make && make install && \
    cd /mafft-7.525-with-extensions/extensions/ && make clean && make && make install

# muscle :: 5.3
RUN cd / && curl -LO https://github.com/rcedgar/muscle/archive/refs/tags/v5.3.tar.gz && \
    tar -zxvf /v5.3.tar.gz && rm /v5.3.tar.gz && cd /muscle-5.3/src/ && \
    sed -i 's/asserta(sizeof(void \*) == 4);/asserta(sizeof(void \*) == 8);/g' myutils.cpp && \
    sh ./build_linux.bash

ENV PATH=/muscle-5.3/bin:${PATH}

# fasttree
RUN cd / && curl -LO https://github.com/morgannprice/fasttree/archive/refs/tags/v2.2.0.tar.gz && \
    tar -zxvf /v2.2.0.tar.gz && rm /v2.2.0.tar.gz

ENV PATH=/fasttree-2.2.0:${PATH}

# raxmlHPC
RUN cd / && curl -LO https://github.com/stamatak/standard-RAxML/archive/refs/tags/v8.2.13.tar.gz && \
    tar -zxvf /v8.2.13.tar.gz && rm /v8.2.13.tar.gz && cd /standard-RAxML-8.2.13/ && \
    make -f Makefile.gcc && rm *.o

ENV PATH=/standard-RAxML-8.2.13:${PATH}

# raxml-ng
RUN mkdir /raxml-ng && cd /raxml-ng && \
    curl -LO https://github.com/amkozlov/raxml-ng/releases/download/1.2.2/raxml-ng_v1.2.2_linux_x86_64.zip

ENV PATH=/raxml-ng:${PATH}

# iqtree3
RUN cd / && curl -LO https://github.com/iqtree/iqtree3/releases/download/v3.0.1/iqtree-3.0.1-Linux.tar.gz && \
    tar -zxvf /iqtree-3.0.1-Linux.tar.gz && rm /iqtree-3.0.1-Linux.tar.gz

ENV PATH=/iqtree-3.0.1-Linux/bin:${PATH}

# make bin ln
RUN cd /OrthoFinder/of3/lib/python3.*/site-packages/orthofinder/bin/ && \
    rm -rf mcl && rm -rf diamond && rm -rf fastme && rm -rf mmseqs && rm -rf famsa && rm -rf mafft && rm -rf muscle && \
    rm -rf FastTree && rm -rf raxmlHPC && rm -rf raxml-ng && rm -rf iqtree2 && rm -rf iqtree3 && \
    ln -sf /usr/bin/mcl                                 ./ && \
    ln -sf /ncbi-blast-2.17.0+-src/c++/ReleaseMT/bin/*  ./ && \
    ln -sf /usr/local/bin/diamond                       ./ && \
    ln -sf /fastme/src/fastme                           ./ && \
    ln -sf /MMseqs2-17-b804f/build/bin/mmseqs           ./ && \
    ln -sf /famsa/famsa                                 ./ && \
    ln -sf /usr/local/bin/mafft                         ./ && \
    ln -sf /mafft-7.525-with-extensions/binaries/*      ./ && \
    ln -sf /muscle-5.3/bin/muscle                       ./ && \
    ln -sf /fasttree-2.2.0/FastTree                     ./ && \
    ln -sf /standard-RAxML-8.2.13/raxmlHPC              ./ && \
    ln -sf /raxml-ng/raxml-ng                           ./ && \
    ln -sf /iqtree-3.0.1-Linux/bin/*                    ./

ENV TAFFISH_ENV=TAFFISH
ENV TAFFISH_NAME=orthofinder
