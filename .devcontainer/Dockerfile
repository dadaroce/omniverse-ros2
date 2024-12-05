# -----------------------------------------------------------------------------
# Maintener: Github @dadaroce
# -----------------------------------------------------------------------------


FROM ubuntu:22.04

# Arguments for building
ARG USER=omniverse
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG ROS_DISTRO=humble

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# -----------------------------------------------------------------------------
RUN apt-get update && apt-get install -y \
    apt-utils \
    build-essential \
    curl \
    git \
    nano \
    python3 \
    python3-argcomplete \
    python3-pip \
    software-properties-common \
    tar \
    wget \
    && apt-get autoremove && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*


# Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
RUN groupadd --gid $USER_GID $USER \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USER \
    && apt-get update && apt-get install -y sudo \
    && echo $USER ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USER \
    && chmod 0440 /etc/sudoers.d/$USER \
    && apt-get autoremove && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# -----------------------------------------------------------------------------
# Installing ROS2
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8
RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null \
    && apt-get update && apt-get install --no-install-recommends -y \
    ros-${ROS_DISTRO}-ros-base \
    ros-${ROS_DISTRO}-rviz2 \
    # TODO: Include any required dependency
    python3-colcon-common-extensions \
    # rosdep
    && pip3 install rosdep \ 
    && rosdep init \
    && rosdep update \
    # ROS2 source setup
    && pip3 install -U argcomplete \
    # Adds to bashrc the ros humble overlay sourcing.
    && echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> /home/$USER/.bashrc \
    && apt autoremove && apt clean -y \
    && rm -rf /var/lib/apt/lists/*

# -----------------------------------------------------------------------------
# Installing Omniverse SDK
WORKDIR /data/ 
RUN wget https://install.launcher.omniverse.nvidia.com/installers/omniverse-launcher-linux.AppImage \
    && chmod 777 /data/omniverse-launcher-linux.AppImage \
    && apt-get update && apt-get install --no-install-recommends -y \
    libnss3 \
    fuse \
    libatk1.0-0 \
    libatk-bridge2.0-0 \
    libgtk-3-0 \
    libasound2 \
    xdg-utils \
    # Create alias to launch the omniverse
    && echo "alias omniverse='/data/omniverse-launcher-linux.AppImage --no-sandbox'" >> /home/$USER/.bashrc \
    && apt autoremove && apt clean -y \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

CMD ["/bin/bash"]
