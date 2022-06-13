FROM nvidia/cuda:11.7.0-devel-ubuntu22.04

### Set environment variables
# Change default Shell to bash
# SHELL ["/bin/bash", "-c"]
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
        zlib1g-dev \
        libsm6 \
        libxext6 \
        libxrender-dev \
        libgl1-mesa-glx \
        libswscale-dev \
        libtbb2 \
        libtbb-dev \
        libjpeg-dev \
        libpng-dev \
        libtiff-dev \
        libavformat-dev \
        libpq-dev \
        libxine2-dev \
        libglew-dev \
        libtiff5-dev \
        libjpeg-dev \
        libavcodec-dev \
        libavformat-dev \
        libavutil-dev \
        libpostproc-dev \
        libswscale-dev \
        libeigen3-dev \
        libtbb-dev \
        libgtk2.0-dev

# Compile OpenCV-CUDA
RUN cd /opt/ \
    # Download and unzip OpenCV and opencv_contrib and delte zip files
    && wget https://github.com/opencv/opencv/archive/$OPENCV_VERSION.zip \
    && unzip $OPENCV_VERSION.zip \
    && rm $OPENCV_VERSION.zip \
    && wget https://github.com/opencv/opencv_contrib/archive/$OPENCV_VERSION.zip \
    && unzip ${OPENCV_VERSION}.zip \
    && rm ${OPENCV_VERSION}.zip \
    # Create build folder and switch to it
    && mkdir /opt/opencv-${OPENCV_VERSION}/build && cd /opt/opencv-${OPENCV_VERSION}/build \
    # Cmake configure
    && cmake \
        -DOPENCV_EXTRA_MODULES_PATH=/opt/opencv_contrib-${OPENCV_VERSION}/modules \
        -DWITH_CUDA=ON \
        -DCMAKE_BUILD_TYPE=RELEASE \
        # Install path will be /usr/local/lib (lib is implicit)
        -DCMAKE_INSTALL_PREFIX=/usr/local \
        .. \
    # Make
    && make -j"$(nproc)" \
    # Install to /usr/local/lib
    && make install \
    && ldconfig \
    # Remove OpenCV sources and build folder
    && rm -rf /opt/opencv-${OPENCV_VERSION} && rm -rf /opt/opencv_contrib-${OPENCV_VERSION}

# Set workspace
WORKDIR /root/workspace