FROM ubuntu:14.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    software-properties-common \
 && rm -rf /var/lib/apt/lists/*

# Install basic command-line utilities and essential build tools
RUN add-apt-repository ppa:git-core/ppa -y \
 && apt-get update \
 && apt-get install -y --no-install-recommends \
    apt-utils \
    build-essential \
    ca-certificates \
    curl \
    git \
    iputils-ping \
    jq \
    libcurl3 \
    libicu52 \
    libssl1.0 \
    libunwind8 \
    locales \
    netcat \
    sudo \
    unzip \
    wget \
    zip \
 && rm -rf /var/lib/apt/lists/*

# Setup the locale
ENV LANG en_US.UTF-8
ENV LC_ALL $LANG
RUN locale-gen $LANG \
 && update-locale

# Install CMake
RUN curl -sL https://cmake.org/files/v3.22/cmake-3.22.0-linux-x86_64.sh -o cmake.sh \
 && chmod +x cmake.sh \
 && ./cmake.sh --prefix=/usr/local --exclude-subdir \
 && rm cmake.sh

# Install clang
ARG CLANG_VER=9.0.0
RUN curl -sL https://releases.llvm.org/$CLANG_VER/clang%2bllvm-$CLANG_VER-x86_64-linux-gnu-ubuntu-14.04.tar.xz -o clang.tar.xz \
 && tar -C /usr/local -xf clang.tar.xz --strip 1 \
 && curl -sL https://releases.llvm.org/$CLANG_VER/openmp-$CLANG_VER.src.tar.xz -o openmp.tar.xz \
 && tar -xf openmp.tar.xz \
 && cd openmp-$CLANG_VER.src \
 && mkdir build \
 && cd build \
 && cmake -DCMAKE_CXX_COMPILER=clang++ -DCMAKE_C_COMPILER=clang .. \
 && make \
 && make install \
 && echo /usr/local/lib > /etc/ld.so.conf.d/openmp.conf \
 && ldconfig \
 && cd ../.. \
 && rm clang.tar.xz \
 && rm openmp.tar.xz \
 && rm -rf openmp-$CLANG_VER.src

# Install Java
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9 \
 && add-apt-repository "deb http://repos.azulsystems.com/ubuntu stable main" -y \
 && apt-get update \
 && apt-get install -y --no-install-recommends \
    zulu-8 \
 && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME_8_X64=/usr/lib/jvm/zulu-8-amd64
ENV JAVA_HOME=$JAVA_HOME_8_X64

# Install SWIG
RUN curl -sLk https://sourceforge.net/projects/swig/files/swig/swig-4.0.2/swig-4.0.2.tar.gz/download -o swig.tar.gz \
 && tar -xzf swig.tar.gz \
 && cd swig-4.0.2 \
 && ./configure --prefix=/usr/local --without-pcre \
 && make \
 && make install \
 && cd .. \
 && rm swig.tar.gz \
 && rm -rf swig-4.0.2

# Install mamba[forge]
RUN curl -sL "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-$(uname -m).sh" -o miniforge.sh \
 && chmod +x miniforge.sh \
 && ./miniforge.sh -b -p /opt/miniforge \
 && rm miniforge.sh \
 && /opt/miniforge/bin/conda clean -a -y \
 && chmod -R 777 /opt/miniforge

ENV CONDA=/opt/miniforge/

# Clean system
RUN apt-get clean \
 && rm -rf /var/lib/apt/lists/* \
 && rm -rf /etc/apt/sources.list.d/* \
 && rm -rf /tmp/*

WORKDIR /vsts

COPY ./start_azure.sh .
RUN chmod +x start_azure.sh

CMD ["./start_azure.sh"]
