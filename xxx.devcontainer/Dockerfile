
FROM osrf/ros:jazzy-desktop
#FROM osrf/ros:jazzy-ros-base

ARG USERNAME=rosdev
ARG USER_UID=1000
ARG USER_GID=$USER_UID

ENV ROS_DISTRO=jazzy

# Install some dependencies packages
RUN apt update -q \
&& apt upgrade -q -y \
&& apt install -y --no-install-recommends \
    sudo                          \
    ninja-build                   \
    rsync                         \
    gdb                           \
    gdbserver                     \
    ros-jazzy-ros-gz                        \
    ros-jazzy-ros-gz-bridge                 \
    ros-jazzy-ros-gz-sim                    \
    ros-jazzy-gz-ros2-control               \
    ros-jazzy-foxglove-bridge               \
    ros-jazzy-joint-state-publisher         \
    ros-jazzy-xacro                         \
    ros-jazzy-teleop-twist-keyboard         \
    ros-jazzy-teleop-twist-joy              \
    software-properties-common              \
    ros-jazzy-twist-mux                     \
    ros-jazzy-ros2-control                  \
    ros-jazzy-ros2-controllers              \
    ros-jazzy-twist-stamper                 \
    python3-pip                             \
    xauth                                   \
&& apt clean \
&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Delete user if it exists in container (e.g Ubuntu Noble: ubuntu)
RUN if id -u $USER_UID ; then userdel `id -un $USER_UID` ; fi

# Create and switch to the user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME
RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y python3-pip
ENV SHELL /bin/bash

# Create workspace so that user own this directory
RUN mkdir -p /home/$USERNAME/ros2_ws/src
WORKDIR /home/$USERNAME/ros2_ws

# Copy configuration files
RUN echo 'source /opt/ros/'$ROS_DISTRO'/setup.bash' >> /home/$USERNAME/.bashrc \
&& echo 'source /home/'$USERNAME'/ros2_ws/install/setup.bash' >> /home/$USERNAME/.bashrc

# [Optional] Set the default user. Omit if you want to keep the default as root.
USER $USERNAME
# Setup entrypoint
COPY ./ros_entrypoint.sh /
ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["/bin/bash"]
