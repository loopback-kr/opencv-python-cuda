FROM nvidia/cuda:11.6.1-cudnn8-devel-ubuntu20.04

### Set environment variables
# Change default Shell to bash
SHELL ["/bin/bash", "-c"]
# Set Timezone
ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Seoul
# Set python encoding type
ENV PYTHONIOENCODING=UTF-8
# Set Beep theme
ENV BEEP_MARIO='-f 130 -l 100 -n -f 262 -l 100 -n -f 330 -l 100 -n -f 392 -l 100 -n -f 523 -l 100 -n -f 660 -l 100 -n -f 784 -l 300 -n -f 660 -l 300 -n -f 146 -l 100 -n -f 262 -l 100 -n -f 311 -l 100 -n -f 415 -l 100 -n -f 523 -l 100 -n -f 622 -l 100 -n -f 831 -l 300 -n -f 622 -l 300 -n -f 155 -l 100 -n -f 294 -l 100 -n -f 349 -l 100 -n -f 466 -l 100 -n -f 588 -l 100 -n -f 699 -l 100 -n -f 933 -l 300 -n -f 933 -l 100 -n -f 933 -l 100 -n -f 933 -l 100 -n -f 1047 -l 400'


### Change settings
# Change bash shell prompt color of root account
RUN sed -i 's/    xterm-color) color_prompt=yes;;/    #xterm-color) color_prompt=yes;;\n    xterm-color|*-256color) color_prompt=yes;;/' /root/.bashrc
# Change original green color of bash shell prompt to red color
RUN sed -i 's/    PS1=\x27${debian_chroot:+(\$debian_chroot)}\\\[\\033\[01;32m\\\]\\u@\\h\\\[\\033\[00m\\\]:\\\[\\033\[01;34m\\\]\\w\\\[\\033\[00m\\\]\\\$ \x27/    PS1=\x27\${debian_chroot:+(\$debian_chroot)}\\\[\\033\[01;31m\\\]\\u@\\h\\\[\\033\[00m\\\]:\\\[\\033\[01;34m\\\]\\w\\\[\\033\[00m\\\]\\\$ \x27/' /root/.bashrc

# Install essential packages
RUN apt update \
    && apt install -y \
        tzdata \
        beep \
        wget \
        git \
        ca-certificates \
        apt-transport-https
# Change Ubuntu repository address to Kakao server in Republic of Korea
RUN sed -i 's/^deb http:\/\/archive.ubuntu.com/deb http:\/\/mirror.kakao.com/g' /etc/apt/sources.list
RUN sed -i 's/^deb http:\/\/security.ubuntu.com/deb http:\/\/mirror.kakao.com/g' /etc/apt/sources.list

### Setup requirements
# OpenCV build arguments
ARG OPENCV_VERSION=4.5.5
# Install build packages
RUN apt update \
    && apt install -y \
    # Install build tools, build dependencies and python
        wget \
        unzip \
        cmake \
        build-essential \
        pkg-config \
        python3-pip \
        python3-tk \
        python3-dev \
        python3-numpy \
        yasm \
        libjpeg-dev \
        libpng-dev \
        libtiff5-dev \
        ffmpeg \
        libavcodec-dev \
        libavformat-dev \
        libswscale-dev \
        libxvidcore-dev \
        libx264-dev \
        libxine2-dev \
        libv4l-dev \
        v4l-utils \
        libgstreamer1.0-dev \
        libgstreamer-plugins-base1.0-dev  \
        libgtk-3-dev \
        libgtkgl2.0-dev \
        libgtkglext1-dev \
        mesa-utils \
        libgl1-mesa-dri \
        libatlas-base-dev \
        gfortran \
        libeigen3-dev \
        zlib1g-dev \
        libsm6 \
        libxext6 \
        libxrender-dev \
        libgl1-mesa-glx \
        libtbb2 \
        libtbb-dev \
        libtiff-dev \
        libpq-dev \
        libglew-dev \
        libavutil-dev \
        libpostproc-dev

# Compile OpenCV-CUDA
RUN cd /opt/ \
    # Download and unzip OpenCV and opencv_contrib and delte zip files
    && wget https://github.com/opencv/opencv/archive/${OPENCV_VERSION}.zip \
    && unzip ${OPENCV_VERSION}.zip \
    && rm ${OPENCV_VERSION}.zip \
    && wget https://github.com/opencv/opencv_contrib/archive/${OPENCV_VERSION}.zip \
    && unzip ${OPENCV_VERSION}.zip \
    && rm ${OPENCV_VERSION}.zip \
    # Create build folder and switch to it
    && mkdir /opt/opencv-${OPENCV_VERSION}/build
WORKDIR /opt/opencv-${OPENCV_VERSION}/build
RUN cmake \
        -D CMAKE_BUILD_TYPE=RELEASE \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D INSTALL_PYTHON_EXAMPLES=ON \
        -D INSTALL_C_EXAMPLES=ON \
        -D BUILD_DOCS=OFF \
        -D BUILD_PERF_TESTS=OFF \
        -D BUILD_TESTS=OFF \
        -D BUILD_PACKAGE=OFF \
        -D BUILD_EXAMPLES=OFF \
        -D WITH_TBB=ON \
        -D ENABLE_FAST_MATH=1 \
        -D CUDA_FAST_MATH=1 \
        -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda \
        -D WITH_CUDA=ON \
        -D WITH_CUBLAS=ON \
        -D WITH_CUFFT=ON \
        -D WITH_NVCUVID=ON \
        -D WITH_IPP=OFF \
        -D WITH_V4L=ON \
        -D WITH_1394=OFF \
        -D WITH_GTK=ON \
        -D WITH_QT=OFF \
        -D WITH_OPENGL=ON \
        -D WITH_EIGEN=ON \
        -D WITH_FFMPEG=ON \
        -D WITH_GSTREAMER=ON \
        -D BUILD_JAVA=OFF \
        -D BUILD_opencv_python3=ON \
        -D BUILD_opencv_python2=OFF \
        -D BUILD_NEW_PYTHON_SUPPORT=ON \
        -D OPENCV_SKIP_PYTHON_LOADER=ON \
        -D OPENCV_GENERATE_PKGCONFIG=ON \
        -D OPENCV_ENABLE_NONFREE=ON \
        -D OPENCV_EXTRA_MODULES_PATH=/opt/opencv_contrib-${OPENCV_VERSION}/modules \
        -D WITH_CUDNN=ON \
        -D OPENCV_DNN_CUDA=ON \
        -D CUDA_ARCH_BIN=8.6 \
        -D CUDA_ARCH_PTX=8.6 \
        -D CUDNN_LIBRARY=/usr/lib/x86_64-linux-gnu/libcudnn.so.8.4.0 \
        -D CUDNN_INCLUDE_DIR=/usr/local/cuda/include \
        ..
RUN make -j"$(nproc)" \
    # Install to /usr/local/lib
    && make install \
    && ldconfig \
    # Remove OpenCV sources and build folder
    && rm -rf /opt/opencv-${OPENCV_VERSION} && rm -rf /opt/opencv_contrib-${OPENCV_VERSION}

# Configure default Pypi repository address and default progress bar style
RUN mkdir -p ~/.config/pip \
    && echo -e \
"[global]\n"\
"index-url=https://mirror.kakao.com/pypi/simple/\n"\
"trusted-host=mirror.kakao.com\n"\
        > ~/.config/pip/pip.conf \
    && pip3 install -U pip

### Postprocessing & Cleaning
# Add Bash alias
RUN echo 'alias l="ls -alh --full-time"' >> ~/.bashrc
RUN echo 'alias cls="clear"' >> ~/.bashrc
RUN echo 'alias python="python3"' >> ~/.bashrc
RUN echo 'alias pip="pip3"' >> ~/.bashrc

# Welcome message
RUN echo "echo -e '\x1b[1;34m  ____                ______   __              _      ____           __  \n / __ \\___  ___ ___  / ___/ | / / ___  ___    | | /| / / /  ___ ___ / /__\n/ /_/ / _ \\/ -_) _ \\/ /__ | |/ / / _ \\/ _ \\   | |/ |/ / _ \\/ -_) -_) (_-<    \x1b[1;34mOpenCV: Open Source Computer Vision Library\x1b[1;34m\n\\____/ .__/\\__/_//_/\\___/ |___/  \\___/_//_/   |__/|__/_//_/\\__/\\__/_/___/    \x1b[0;34mPre-built CUDA-Enabled OpenCV packages for Python.\x1b[1;34m\n    /_/                                                                  \n'" >> ~/.bashrc
RUN echo "echo -e '\x1b[0;33mUbuntu\t'\$(sed -n '/VERSION=/p' /etc/os-release | cut -d '\"' -f 2)" >> ~/.bashrc
RUN echo "echo -e 'Python \t'\$(python -V | cut -d ' ' -f 2)" >> ~/.bashrc
RUN echo "echo -e 'PIP \t'\$(pip -V | cut -d '(' -f 1 | cut -d ' ' -f 2-)" >> ~/.bashrc
RUN echo "echo -e '\t'\$(sed -n '/index-url/p' ~/.config/pip/pip.conf)" >> ~/.bashrc
RUN echo "echo -e 'OpenCV \t'\$(opencv_version)" >> ~/.bashrc
RUN echo "echo -n -e '\x1b[0m'" >> ~/.bashrc

RUN echo "if command -v nvidia-smi &> /dev/null" >> ~/.bashrc
RUN echo "then" >> ~/.bashrc
RUN echo "    echo -e -n '\x1b[0;33mCUDA\t' && echo -e \$(nvcc -V) | grep 'Cuda compilation tools' | cut -d ',' -f 3 | sed 's/ V//g' | cut -d ' ' -f 1" >> ~/.bashrc
RUN echo "    echo -e '\tcuDNN '\$(grep '#define CUDNN_' /usr/include/cudnn_version.h | cut -d ' ' -f 3 | sed -z 's/\n//' | sed -z 's/\n/./g') | sed 's/.(CUDNN_MAJOR.//'" >> ~/.bashrc
RUN echo "    echo -e '\x1b[0;32m' && nvidia-smi -L && echo -e '\x1b[0m'" >> ~/.bashrc
RUN echo "else" >> ~/.bashrc
RUN echo "echo -e -n '\n'" >> ~/.bashrc
RUN echo "fi" >> ~/.bashrc

# Clean the cache
RUN apt clean \
    && rm -rf /var/lib/apt/lists/* \
    && pip cache purge
# Set workspace
WORKDIR /root/workspace