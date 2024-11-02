##############
# Base image #
##############


FROM registry.gitlab.rtc.ru/ros_team/docker_common_images/ros:noetic as base

ARG CATKIN_WORKSPACE_DIR=${CATKIN_WORKSPACE_DIR}
WORKDIR ${CATKIN_WORKSPACE_DIR}

SHELL ["/bin/bash", "-c"]

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
  && apt-get -y install \
  git \
  python3-catkin-tools \
  python3-osrf-pycommon \
  && rm -rf /var/lib/apt/lists/*

# Add additional installation instructions here...

ENV DEBIAN_FRONTEND=dialog


#####################
# Development image #
#####################
FROM base as dev

ARG USERNAME_=variator
ARG UID=${UID}
ARG GID=${GID}

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
  && apt-get install -y \
  iperf3 \
  iputils-ping \
  net-tools \
  python3-vcstool \
  && rm -rf /var/lib/apt/lists/*

RUN apt-get update \
  && apt-get install -y \
  ros-${ROS_DISTRO}-rviz \
  ros-${ROS_DISTRO}-rqt-common-plugins \
  libpulse-dev \
  && rm -rf /var/lib/apt/lists/*

# Install additional developer tools here...

ENV DEBIAN_FRONTEND=dialog

RUN addgroup --gid ${GID} ${USERNAME_} \
  && adduser --disabled-password --gecos '' --uid ${GID} --gid ${GID} ${USERNAME_} \
  && echo ${USERNAME_} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/${USERNAME_} \
  && chown -R ${UID}:${GID} /home/${USERNAME_} \
  && chown -R ${UID}:${GID} ${CATKIN_WORKSPACE_DIR}

# COPY "/.vscode" "${CATKIN_WORKSPACE_DIR}/.vscode"


USER ${USERNAME_}


#####################
# Built image #
#####################
FROM dev as build

ARG CATKIN_WORKSPACE_DIR="/home/variator/catkin_ws"

ENV DEBIAN_FRONTEND=noninteractive

COPY ./src/ ${CATKIN_WORKSPACE_DIR}/src/

WORKDIR ${CATKIN_WORKSPACE_DIR}

RUN sudo apt-get update 
RUN sudo apt-get install python3-rosdep -y
# RUN sudo rosdep init


RUN sudo apt-get install make gcc g++  python-is-python3 -y
RUN sudo apt-get install ros-noetic-rqt-controller-manager -y
# RUN rosdep update 
# RUN rosdep install --from-paths src --ignore-src -r -y 
# RUN source /opt/ros/noetic/setup.bash \
#   && catkin build 


COPY ./docker/.bashrc /home/variator/

ENV DEBIAN_FRONTEND=dialog

USER ${USERNAME_}