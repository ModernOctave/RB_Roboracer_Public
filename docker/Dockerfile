FROM ubuntu:22.04

RUN apt-get update && DEBIAN_FRONTEND=noninteractive TZ=Etc/UTC apt-get -y install tzdata

RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    git \
    wget

RUN apt-get update && apt-get install -y \
    python3 \
    python3-setuptools \
    python3-wheel \
    libpython3-dev

WORKDIR /root

# ============ ROS ============
# Set locale
RUN apt-get update && apt-get install -y locales \
    && locale-gen en_US en_US.UTF-8 \
    && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 \
    && export LANG=en_US.UTF-8

# Setup Sources
RUN apt-get update && apt-get install -y  software-properties-common \
    && add-apt-repository -y universe

RUN apt-get update && apt-get install -y curl \
    && curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null

# Install ROS
RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y ros-humble-ros-base ros-dev-tools

# ============ ORB_SLAM3 ============
# Install Pangolin
# RUN git clone --recursive -b v0.9.2 https://github.com/stevenlovegrove/Pangolin.git
COPY deps/Pangolin Pangolin
# ADD https://github.com/stevenlovegrove/Pangolin.git#v0.9.2 Pangolin
RUN apt-get update && apt-get install -y \
    libgl1-mesa-dev \
    libwayland-dev \
    libxkbcommon-dev \
    wayland-protocols \
    libegl1-mesa-dev \
    libc++-dev \
    libepoxy-dev \
    libglew-dev \
    libeigen3-dev \
    ninja-build \
    libjpeg-dev \
    libpng-dev \
    libavcodec-dev \
    libavutil-dev \
    libavformat-dev \
    libswscale-dev \
    libavdevice-dev
RUN cd Pangolin \
    && mkdir build \
    && cd build \
    && cmake .. \
    && make -j$(nproc) \
    && make install

# Install OpenCV & Eigen3
RUN apt-get update && apt-get install -y \
    libopencv-dev \
    libeigen3-dev

# Install ORB_SLAM3
# RUN git clone -b c++14_comp https://github.com/UZ-SLAMLab/ORB_SLAM3.git
COPY deps/ORB_SLAM3 ORB_SLAM3
# ADD https://github.com/UZ-SLAMLab/ORB_SLAM3.git#c++14_comp ORB_SLAM3
RUN apt-get update && apt-get install -y \
    libboost-all-dev \
    libssl-dev
RUN cd ORB_SLAM3 \
    # && sed -i 's/CXX11/CXX14/g' CMakeLists.txt \
    # && sed -i 's/c++11/c++14/g' CMakeLists.txt\
    && sed -i 's/OpenCV 4.4/OpenCV 4.2/g' CMakeLists.txt\
    # && sed -i 's/-Wall/-w/g' CMakeLists.txt\
    && chmod +x build.sh \
    && ./build.sh

# ============ Stable ============
RUN rosdep init && rosdep update

# Install ORB_SLAM3_RB5
RUN mkdir -p ~/ros2_ws/src
RUN echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc \
    echo "source ~/ros2_ws/install/setup.bash" >> ~/.bashrc
COPY src ros2_ws/src
RUN cd ~/ros2_ws \
    && . /opt/ros/humble/setup.sh \
    && rosdep install -i --from-path src --rosdistro humble -y \
    && colcon build --symlink-install

# ============ Dev ============
RUN apt-get update && apt-get install -y ros-humble-camera-ros