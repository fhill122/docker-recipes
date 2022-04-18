FROM ros:melodic-ros-base-bionic

# apt with domestic mirror
RUN sed -i 's/archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list && \
    sed -i 's/ports.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list


# kalibr apt dependencies
RUN apt update && apt install -y \
    # what listed in kalibr wiki minus python-software-properties
    python-setuptools python-rosinstall ipython libeigen3-dev libboost-all-dev doxygen libopencv-dev \
    ros-melodic-vision-opencv ros-melodic-image-transport-plugins ros-melodic-cmake-modules \
    software-properties-common libpoco-dev python-matplotlib python-scipy \
    python-git python-pip ipython libtbb-dev libblas-dev liblapack-dev python-catkin-tools libv4l-dev \
    # python-igraph depenpencies for pip install
    # libtool autoconf byacc python-cairo \
    python-igraph python-cairo \
    # kalibr build required
    wget

# kalibr pip dependencie listed in doc, alternative for apt python-igraph
# RUN python -m pip install --upgrade --force pip && pip install python-igraph==0.8.3

# setup workspace
WORKDIR /workspace
SHELL ["/bin/bash", "-c"]
RUN mkdir src && source /opt/ros/melodic/setup.bash && \
    catkin init && \
    # copied from kalibr wiki, but why explicit extend ros base? we already sourced before
    catkin config --extend /opt/ros/melodic && \
    catkin config --merge-devel && \
    catkin config --cmake-args -DCMAKE_BUILD_TYPE=Release

# download
RUN cd src && git clone https://github.com/ethz-asl/Kalibr.git
# patch
RUN cd src/Kalibr && git checkout 445b9463abbfdc90498193c5fa20b20be70c036d && \
    sed -i 's/import ImageFile/from PIL import ImageFile/g' aslam_offline_calibration/kalibr/python/kalibr_bagcreater

# copied from kalibr wiki, but Release build arg is redundant here as already configured before
RUN catkin build -DCMAKE_BUILD_TYPE=Release -j4 && source devel/setup.bash
