FROM ros:noetic-perception

ENV DEBIAN_FRONTEND=noninteractive
COPY bashrc /etc/bash.bashrc
RUN chmod a+rwx /etc/bash.bashrc
ENV SHELL /bin/bash
SHELL ["/bin/bash", "-c"]

# install packages
RUN apt-get update && apt-get install -q -y --no-install-recommends \
    dirmngr \
    gnupg2 \
    && rm -rf /var/lib/apt/lists/*

ENV ROS_DISTRO noetic

# Setup non-root admin user
ARG USERNAME=admin
ARG USER_UID=1000
ARG USER_GID=1000

# Install prerequisites
RUN apt-get update && apt-get install -y \
        sudo \
&& rm -rf /var/lib/apt/lists/* \
&& apt-get clean

RUN apt-get update && apt-get install -y \
        ros-noetic-diagnostic-updater \
        ros-noetic-gazebo-ros \
        ros-noetic-rqt* \
        ros-noetic-rviz \
        ros-noetic-urdf* \
&& rm -rf /var/lib/apt/lists/* \
&& apt-get clean

RUN apt-get update && apt-get install -y \
        ros-noetic-gazebo-plugins \
&& rm -rf /var/lib/apt/lists/* \
&& apt-get clean

RUN apt-get update && apt-get upgrade -y && apt-get install -y \
        git clang gdb gdbserver\
&& rm -rf /var/lib/apt/lists/* \
&& apt-get clean

# Create the 'admin' user if not already exists
RUN if [ ! $(getent passwd ${USERNAME}) ]; then \
        groupadd --gid ${USER_GID} ${USERNAME} ; \
        useradd --uid ${USER_UID} --gid ${USER_GID} -m ${USERNAME} ; \
    fi

# Update 'admin' user
RUN echo ${USERNAME} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/${USERNAME} \
    && chmod 0440 /etc/sudoers.d/${USERNAME} \
    && adduser ${USERNAME} video && adduser ${USERNAME} sudo

# Copy scripts
RUN mkdir -p /usr/local/bin/scripts
COPY *entrypoint.sh /usr/local/bin/scripts/
RUN  chmod +x /usr/local/bin/scripts/*.sh

USER ${USERNAME} 
WORKDIR /home/${USERNAME}
RUN mkdir -p catkin_ws/src

ENV USERNAME=${USERNAME}
ENV USER_GID=${USER_GID}
ENV USER_UID=${USER_UID}

ENV NVIDIA_VISIBLE_DEVICES \
    ${NVIDIA_VISIBLE_DEVICES:-all}
ENV NVIDIA_DRIVER_CAPABILITIES \
    ${NVIDIA_DRIVER_CAPABILITIES:+$NVIDIA_DRIVER_CAPABILITIES,}graphics