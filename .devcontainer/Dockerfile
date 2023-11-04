# syntax=docker/dockerfile:1

# FROM nvidia/cudagl:11.4.2-runtime-ubuntu20.04
# FROM nvcr.io/nvidia/cudagl:11.4.2-runtime-ubuntu20.04
FROM nvidia/opengl:1.2-glvnd-runtime-ubuntu22.04 as core
ENV ROS_DISTRO=humble
ENV DEBIAN_FRONTEND=noninteractive

## Install basic packages
RUN apt update && apt -y install --no-install-recommends \
  tzdata \
  locales \
  curl \
  lsb-release \
  ca-certificates \
  && rm -rf /var/lib/apt/lists/*

## Set locale to UTF-8
RUN locale-gen C.UTF-8 \
  && update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8

ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8

RUN curl -sSLo /etc/apt/keyrings/ros-archive-keyring.gpg https://raw.github.com/ros/rosdistro/master/ros.key \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" \
  | tee /etc/apt/sources.list.d/ros2-latest.list > /dev/null

## Install ROS core package
RUN apt update && apt -qy upgrade \
  && apt -qy install --no-install-recommends \
  ros-${ROS_DISTRO}-ros-core \
  && rm -rf /var/lib/apt/lists/*



FROM core as base

## Install development tools
RUN apt update && apt -qy install --no-install-recommends \
  ros-dev-tools \
  && rm -rf /var/lib/apt/lists/*

## Bootstrap rosdep
RUN rosdep init \
  && rosdep update --rosdistro ${ROS_DISTRO}

## Install basic package
RUN apt update && apt -qy install --no-install-recommends \
  ros-${ROS_DISTRO}-ros-base \
  python3-colcon-mixin \
  && rm -rf /var/lib/apt/lists/*

# Setup colcon mixin and metadata
RUN colcon mixin add default https://raw.github.com/colcon/colcon-mixin-repository/master/index.yaml \
  && colcon metadata add default https://raw.github.com/colcon/colcon-metadata-repository/master/index.yaml \
  && colcon metadata update



FROM base as desktop

## Install desktop package
RUN apt update && apt -qy install --no-install-recommends \
  ros-${ROS_DISTRO}-desktop \
  && rm -rf /var/lib/apt/lists/*



FROM base as desktop-full

## Install desktop-full package
RUN apt update && apt -qy install --no-install-recommends \
  ros-${ROS_DISTRO}-desktop-full \
  && rm -rf /var/lib/apt/lists/*



FROM desktop-full

## Install packages
RUN apt update && apt -y install --no-install-recommends \
  python-is-python3 \
  ros-${ROS_DISTRO}-xacro \
  ros-${ROS_DISTRO}-gazebo-ros-pkgs \
  ros-${ROS_DISTRO}-turtlebot3 \
  ros-${ROS_DISTRO}-turtlebot3-simulations \
  && rm -rf /var/lib/apt/lists/* \
  && unset DEBIAN_FRONTEND
