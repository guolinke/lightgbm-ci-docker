FROM quay.io/pypa/manylinux_2_28_x86_64

RUN yum update -y \
 && yum makecache --refresh

# Install basic command-line utilities and essential build tools
# RUN add-apt-repository ppa:git-core/ppa -y \
#  && apt-get update \
#  && apt-get install -y --no-install-recommends \
#     apt-utils \
#     build-essential \
#     ca-certificates \
#     curl \
#     git \
#     iputils-ping \
#     jq \
#     libcurl3 \
#     libhwloc-dev \
#     libicu52 \
#     libssl1.0 \
#     libtinfo-dev \
#     libunwind8 \
#     locales \
#     netcat \
#     ocl-icd-dev \
#     pkg-config \
#     sudo \
#     unzip \
#     wget \
#     zip \
#     zlib1g-dev \
#  && rm -rf /var/lib/apt/lists/*

# Setup the locale
# ENV LANG en_US.UTF-8
# ENV LC_ALL $LANG
# RUN locale-gen $LANG \
#  && update-locale

# Install CMake
RUN curl -sL https://cmake.org/files/v3.23/cmake-3.23.1-linux-x86_64.sh -o cmake.sh \
 && chmod +x cmake.sh \
 && ./cmake.sh --prefix=/usr/local --exclude-subdir \
 && rm -f ./cmake.sh

# Install clang (NOTE: v11.0.0 requires GLIBC==2.29, too new for this image)
#ARG CLANG_VER=10.0.0

# RUN yum install -y \
#     ncurses-devel  # for libtinfo.so

# building clang
# pre-compiled clang+llvm is not available for AlmaLinux, so build it from source
# https://clang.llvm.org/get_started.html
# https://llvm.org/docs/GettingStarted.html#compiling-the-llvm-suite-source-code
ARG CLANG_VER=14.0.6
RUN mkdir /usr/local/src/clang \
 && cd /usr/local/src/clang \
 && git clone --depth 1 --branch llvmorg-$CLANG_VER https://github.com/llvm/llvm-project.git \
 && cd llvm-project \
 && mkdir build \
 && cd build \
 && cmake \
     -DCMAKE_BUILD_TYPE=Release \
     -DLLVM_BUILD_BENCHMARKS=OFF \
     -DLLVM_BUILD_DOCS=OFF \
     -DLLVM_BUILD_TESTS=OFF \
     -DLLVM_INCLUDE_BENCHMARKS=OFF \
     -DLLVM_INCLUDE_DOCS=OFF \
     -DLLVM_INCLUDE_TESTS=OFF \
     -DLLVM_ENABLE_BINDINGS=OFF \
     -DLLVM_ENABLE_DOXYGEN=OFF \
     -DLLVM_ENABLE_OCAMLDOC=OFF \
     -DLLVM_ENABLE_PROJECTS="clang;openmp" \
     -DLLVM_ENABLE_SPHINX=OFF \
     -DLLVM_TARGETS_TO_BUILD="X86" \
     -G "Unix Makefiles" \
     ../llvm \
 && make -j2

# ARG CLANG_VER=14.0.0
# RUN curl -sL https://github.com/llvm/llvm-project/releases/download/llvmorg-$CLANG_VER/clang+llvm-$CLANG_VER-x86_64-linux-gnu-ubuntu-18.04.tar.xz -o clang.tar.xz \
#  && tar -C /usr/local -xf clang.tar.xz --strip 1 \
#  && curl -sL https://github.com/llvm/llvm-project/releases/download/llvmorg-$CLANG_VER/openmp-$CLANG_VER.src.tar.xz -o openmp.tar.xz \
#  && tar -xf openmp.tar.xz \
#  && cd openmp-$CLANG_VER.src \
#  && mkdir build \
#  && cd build \
#  && cmake -DCMAKE_CXX_COMPILER=clang++ -DCMAKE_C_COMPILER=clang -DGCC_INSTALL_PREFIX=/opt/rh/gcc-toolset-11/root/usr/bin .. \
#  && make \
#  && make install \
#  && echo /usr/local/lib > /etc/ld.so.conf.d/openmp.conf \
#  && ldconfig \
#  && cd ../.. \
#  && rm clang.tar.xz \
#  && rm openmp.tar.xz \
#  && rm -rf openmp-$CLANG_VER.src

# RUN curl -sL https://releases.llvm.org/$CLANG_VER/clang%2bllvm-$CLANG_VER-x86_64-linux-gnu-ubuntu-14.04.tar.xz -o clang.tar.xz \
#  && tar -C /usr/local -xf clang.tar.xz --strip 1 \
#  && curl -sL https://releases.llvm.org/$CLANG_VER/openmp-$CLANG_VER.src.tar.xz -o openmp.tar.xz \
#  && tar -xf openmp.tar.xz \
#  && cd openmp-$CLANG_VER.src \
#  && mkdir build \
#  && cd build \
#  && cmake -DCMAKE_CXX_COMPILER=clang++ -DCMAKE_C_COMPILER=clang .. \
#  && make \
#  && make install \
#  && echo /usr/local/lib > /etc/ld.so.conf.d/openmp.conf \
#  && ldconfig \
#  && cd ../.. \
#  && rm clang.tar.xz \
#  && rm openmp.tar.xz \
#  && rm -rf openmp-$CLANG_VER.src

#  # Install PoCL
# RUN git clone --depth 1 --branch v1.8 https://github.com/pocl/pocl.git \
#  && cmake \
#     -B pocl/build \
#     -S pocl \
#     -DCMAKE_BUILD_TYPE=release \
#     -DCMAKE_C_COMPILER=clang \
#     -DCMAKE_CXX_COMPILER=clang++ \
#     -DCMAKE_CXX_FLAGS=-stdlib=libc++ \
#     -DPOCL_INSTALL_ICD_VENDORDIR=/etc/OpenCL/vendors \
#     -DPOCL_DEBUG_MESSAGES=OFF \
#     -DSTATIC_LLVM=ON \
#     -DINSTALL_OPENCL_HEADERS=OFF \
#     -DENABLE_SPIR=OFF \
#     -DENABLE_POCLCC=OFF \
#     -DENABLE_TESTS=OFF \
#     -DENABLE_EXAMPLES=OFF \
#  && cmake --build pocl/build -j4 \
#  && cmake --install pocl/build

# # Install Java
# RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9 \
#  && add-apt-repository "deb http://repos.azulsystems.com/ubuntu stable main" -y \
#  && apt-get update \
#  && apt-get install -y --no-install-recommends \
#     zulu-8 \
#  && rm -rf /var/lib/apt/lists/*

# ENV JAVA_HOME_8_X64=/usr/lib/jvm/zulu-8-amd64
# ENV JAVA_HOME=$JAVA_HOME_8_X64

# # Install SWIG
# RUN curl -sLk https://sourceforge.net/projects/swig/files/swig/swig-4.0.2/swig-4.0.2.tar.gz/download -o swig.tar.gz \
#  && tar -xzf swig.tar.gz \
#  && cd swig-4.0.2 \
#  && ./configure --prefix=/usr/local --without-pcre \
#  && make \
#  && make install \
#  && cd .. \
#  && rm swig.tar.gz \
#  && rm -rf swig-4.0.2

# # Install miniforge
# RUN curl -sL "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-$(uname -m).sh" -o miniforge.sh \
#  && chmod +x miniforge.sh \
#  && ./miniforge.sh -b -p /opt/miniforge \
#  && rm miniforge.sh \
#  && /opt/miniforge/bin/conda clean -a -y \
#  && chmod -R 777 /opt/miniforge

# ENV CONDA=/opt/miniforge/

# # Clean system
# RUN apt-get clean \
#  && rm -rf /var/lib/apt/lists/* \
#  && rm -rf /etc/apt/sources.list.d/* \
#  && rm -rf /tmp/*

# WORKDIR /vsts

# COPY ./start_azure.sh .
# RUN chmod +x start_azure.sh

# CMD ["./start_azure.sh"]
