FROM nvidia/cuda:11.1-devel-ubuntu20.04

# Change default Shell to bash
SHELL ["/bin/bash", "-c"]


### Set environment variables
# Set Timezone
ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Seoul
# Set X Server remote address
ENV DISPLAY=dev.loopback.kr:0
# Set python encoding type
ENV PYTHONIOENCODING=UTF-8


### Change settings
# Change Timezone
# RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# Change bash shell prompt color of root account
RUN sed -i 's/    xterm-color) color_prompt=yes;;/    #xterm-color) color_prompt=yes;;\n    xterm-color|*-256color) color_prompt=yes;;/' /root/.bashrc
# Change original green color of bash shell prompt to red color
RUN sed -i 's/    PS1=\x27${debian_chroot:+(\$debian_chroot)}\\\[\\033\[01;32m\\\]\\u@\\h\\\[\\033\[00m\\\]:\\\[\\033\[01;34m\\\]\\w\\\[\\033\[00m\\\]\\\$ \x27/    PS1=\x27\${debian_chroot:+(\$debian_chroot)}\\\[\\033\[01;31m\\\]\\u@\\h\\\[\\033\[00m\\\]:\\\[\\033\[01;34m\\\]\\w\\\[\\033\[00m\\\]\\\$ \x27/' /root/.bashrc
# Change Ubuntu repository address to Kakao server in Republic of Korea
RUN sed -i 's/http:\/\/archive.ubuntu.com/https:\/\/mirror.loopback.kr/g' /etc/apt/sources.list
RUN sed -i 's/http:\/\/security.ubuntu.com/https:\/\/mirror.loopback.kr/g' /etc/apt/sources.list
RUN sed -i 's/http:\/\/extras.ubuntu.com/https:\/\/mirror.loopback.kr/g' /etc/apt/sources.list


### Dockerfile build arguments
ARG OPENCV_VERSION=4.4.0


# Install essential packages
RUN apt update \
    && apt install -y \
    # Install build tools, build dependencies and python
	    tzdata \
        beep \
        python3-pip \
        build-essential \
        cmake \
        git \
        unzip \
        libsm6 \
        libxext6 \
        libxrender-dev \
        libgl1-mesa-glx \
        wget \
        yasm \
        pkg-config \
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
        zlib1g-dev \
        libjpeg-dev \
        libavcodec-dev \
        libavformat-dev \
        libavutil-dev \
        libpostproc-dev \
        libswscale-dev \
        libeigen3-dev \
        libtbb-dev \
        libgtk2.0-dev \
        pkg-config \
        python3-dev \
        python3-numpy \
    && apt clean \
    && rm -rf /var/lib/apt/lists/*

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

# Configure default Pypi repository address and default progress bar style (For Rep. of Korea)
RUN mkdir -p ~/.config/pip \
    && echo -e \
"[global]\n"\
"index-url=https://mirror.loopback.kr/pypi/simple/\n"\
"trusted-host=mirror.loopback.kr\n"\
"progress-bar=on"\
        > ~/.config/pip/pip.conf \
    && pip3 install -U pip

WORKDIR /root/workspace

# Add Bash Alias
RUN echo 'alias l="ls -alh --full-time"' >> ~/.bashrc
RUN echo 'alias cls="clear"' >> ~/.bashrc