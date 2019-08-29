# This Dockerfile is used to build an ROS Melodic + Tensorflow GPU image based on Ubuntu 18.04
FROM nvidia/cuda:10.0-devel-ubuntu18.04
ARG DEBIAN_FRONTEND=noninteractive

LABEL maintainer "Rauf Yagfarov"
MAINTAINER Rauf Yagfarov "https://github.com/RocketFlash"

# Install sudo
RUN apt-get update && \
    apt-get install -y sudo apt-utils curl


# Add new sudo user
ARG user=road_signs_classifier
ARG passwd=qaz12345
ARG uid=1000
ARG gid=1000
ENV USER=$user
ENV PASSWD=$passwd
ENV UID=$uid
ENV GID=$gid
RUN useradd --create-home -m $USER && \
        echo "$USER:$PASSWD" | chpasswd && \
        usermod --shell /bin/bash $USER && \
        usermod -aG sudo $USER && \
        echo "$USER ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/$USER && \
        chmod 0440 /etc/sudoers.d/$USER && \
        usermod  --uid $UID $USER && \
        groupmod --gid $GID $USER
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8

# Install utilities
RUN apt-get update && \
    apt-get install -y \
    tmux \
    git \
    wget \
    nano \
    lsb-release \
    lsb-core

# Install pip
RUN apt-get update -y && \
    apt-get install -y python3 python3-pip

# Install python libraries
RUN pip3 install tensorflow-gpu keras matplotlib h5py scikit-learn albumentations image-classifiers opencv-python

# Install ROS
ENV APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE=1
RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list' && \
    apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654 && \
    apt-get update && apt-get install -y ros-melodic-ros-base && \
    apt-get install -y python-rosinstall python-rosinstall-generator python-wstool build-essential && \
    rosdep init

RUN pip3 install rospkg
RUN apt-get update && apt-get install -y \
    python-catkin-tools

# Expose Jupyter 
EXPOSE 8888

# Expose Tensorboard
EXPOSE 6006

# Setup environment
USER $USER
RUN HOME=/home/$USER
RUN rosdep fix-permissions && rosdep update
RUN echo "export PATH=/usr/local/cuda-10.0/bin${PATH:+:${PATH}}" >> ~/.bashrc
RUN echo "export LD_LIBRARY_PATH=/usr/local/cuda-10.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}" >> ~/.bashrc
RUN echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
RUN /bin/bash -c "source ~/.bashrc"

RUN mkdir -p /home/$USER/catkin_ws/src

