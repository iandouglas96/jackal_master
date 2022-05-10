FROM nvidia/cudagl:11.1.1-devel-ubuntu20.04

RUN apt-get update \
 && DEBIAN_FRONTEND="noninteractive" TZ="US/Eastern" apt-get install -y \
    tzdata \
 && apt-get clean

# Tools I find useful during development
RUN apt-get update \
 && apt-get install -y \
        build-essential \
        cmake \
        cppcheck \
        gdb \
        git \
        libbluetooth-dev \
        libcwiid-dev \
        libgoogle-glog-dev \
        libspnav-dev \
        libusb-dev \
        lsb-release \
        mercurial \
        python3-dbg \
        python3-empy \
        python3-pip \
        python3-venv \
        software-properties-common \
        sudo \
        vim \
        wget \
	curl \
 && apt-get clean

# Add a user with the same user_id as the user outside the container
# Requires a docker build argument `user_id`
ARG user_id
env USER dcist
RUN useradd -U --uid ${user_id} -ms /bin/bash $USER \
 && echo "$USER:$USER" | chpasswd \
 && adduser $USER sudo \
 && echo "$USER ALL=NOPASSWD: ALL" >> /etc/sudoers.d/$USER

# Commands below run as the developer user
USER $USER

# Clone all the gazebo models so that you don't download them every time
# docker is run
RUN mkdir -p /home/$USER/.gazebo && git clone https://github.com/osrf/gazebo_models /home/$USER/.gazebo/models

RUN sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list' \ && sudo /bin/sh -c 'curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add -' \
 && sudo apt-get update \
 && sudo apt-get install -y \
    python3-catkin-tools \
    python3-rosdep \
    python3-rosinstall \
    ros-noetic-desktop-full

RUN sudo rosdep init \
 && sudo apt-get clean

RUN rosdep update

RUN sudo apt-get install -y geany tmux dbus-x11

# install pytorch
RUN sudo sh -c 'pip3 install torch==1.8.2+cu111 torchvision==0.9.2+cu111 torchaudio==0.8.2 -f https://download.pytorch.org/whl/lts/1.8/torch_lts.html'

RUN sudo apt-get install -y \
    ros-noetic-nmea-msgs \
    ros-noetic-nmea-navsat-driver \
    ros-noetic-rosserial \
    ros-noetic-rosserial-server \
    ros-noetic-teleop-twist-joy \
    ros-noetic-lms1xx \
    libfmt-dev \
    cmake-curses-gui

# When running a container start in the developer's home folder
WORKDIR /home/$USER

RUN echo 'export PS1="\[$(tput setaf 2; tput bold)\]\u\[$(tput setaf 7)\]@\[$(tput setaf 3)\]\h\[$(tput setaf 7)\]:\[$(tput setaf 4)\]\W\[$(tput setaf 7)\]$ \[$(tput sgr0)\]"' >> ~/.bashrc
RUN echo 'source ~/.bashrc_host' >> ~/.bashrc

RUN /bin/sh -c 'echo ". /opt/ros/noetic/setup.bash" >> ~/.bashrc' \
 && /bin/sh -c 'echo ". /usr/share/gazebo/setup.sh" >> ~/.bashrc' \
 && /bin/sh -c 'echo "source ~/user_ws/devel/setup.bash" >> ~/.bashrc'
