FROM microsoft/vsts-agent:ubuntu-14.04

# Install basic command-line utilities
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    curl \
    locales \
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

# Install essential build tools
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    build-essential \
 && rm -rf /var/lib/apt/lists/*

# Install clang 9.0
RUN add-apt-repository ppa:ubuntu-toolchain-r/test -y \
 && cd /tmp \
 && wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add - \
 && add-apt-repository "deb http://apt.llvm.org/trusty/ llvm-toolchain-trusty-9 main" -y \
 && apt-get update \
 && apt-get install -y --no-install-recommends \
    clang-9 \
    libomp-9-dev \
 && update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-9 100 \
 && update-alternatives --install /usr/bin/clang clang /usr/bin/clang-9 100 \
 && rm -rf /var/lib/apt/lists/*

# Install CMake
RUN curl -sL https://cmake.org/files/v3.16/cmake-3.16.0-Linux-x86_64.sh -o cmake.sh \
 && chmod +x cmake.sh \
 && ./cmake.sh --prefix=/usr/local --exclude-subdir \
 && rm cmake.sh

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
RUN curl -sL https://downloads.sourceforge.net/project/swig/swig/swig-3.0.12/swig-3.0.12.tar.gz -o swig.tar.gz \
 && tar -xzf swig.tar.gz \
 && cd swig-3.0.12 \
 && ./configure --prefix=/usr/local --without-pcre \
 && make \
 && make install \
 && cd .. \
 && rm swig.tar.gz \
 && rm -rf swig-3.0.12

# Install Miniconda
RUN curl -sL https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -o miniconda.sh \
 && chmod +x miniconda.sh \
 && ./miniconda.sh -b -p /opt/conda \
 && rm miniconda.sh \
 && /opt/conda/bin/conda install python=3 -q -y \
 && /opt/conda/bin/conda install mkl qt -q -y \
 && /opt/conda/bin/conda clean -a -y \
 && chmod -R 777 /opt/conda
 
ENV CONDA=/opt/conda/

# Clean system
RUN apt-get clean \
 && rm -rf /var/lib/apt/lists/* \
 && rm -rf /etc/apt/sources.list.d/* \
 && rm -rf /tmp/*
