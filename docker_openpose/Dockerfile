FROM nvidia/cuda:10.0-cudnn7-devel

# user env
ENV USER openpose
ENV HOME /home/${USER}
ENV SHELL /bin/bash

# add user
RUN useradd -m ${USER}
# give sudo adm to USER
RUN gpasswd -a ${USER} sudo
# set password
RUN echo "${USER}:password" | chpasswd
# login shell
RUN sed -i.bak "s#${HOME}:#${HOME}:${SHELL}#" /etc/passwd

#get deps
RUN apt-get update && \
DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
python3-dev python3-pip git g++ wget make libprotobuf-dev protobuf-compiler libopencv-dev \
libgoogle-glog-dev libboost-all-dev libcaffe-cuda-dev libhdf5-dev libatlas-base-dev \
vim sudo tmux ffmpeg

#for python api
RUN pip3 install numpy opencv-python
ENV PYTHONPATH /usr/local/python/openpose 

#replace cmake as old version has CUDA variable bugs
RUN wget https://github.com/Kitware/CMake/releases/download/v3.14.2/cmake-3.14.2-Linux-x86_64.tar.gz && \
tar xzf cmake-3.14.2-Linux-x86_64.tar.gz -C /opt && \
rm cmake-3.14.2-Linux-x86_64.tar.gz
ENV PATH="/opt/cmake-3.14.2-Linux-x86_64/bin:${PATH}"


#get openpose
WORKDIR /openpose
RUN git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose.git .

#build it
WORKDIR /openpose/build
RUN cmake -DBUILD_PYTHON=true .. && make -j8
WORKDIR /openpose

USER ${USER}
