ARG PYTORCH="2.6.0"
ARG CUDA="12.6"
ARG CUDNN="9"

# FROM nvidia/cuda:11.1.1-cudnn8-devel-ubuntu20.04
FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-devel

# Install Python and pip


ENV TORCH_CUDA_ARCH_LIST="6.0 6.1 7.0 7.5 8.0 8.6+PTX" \
    TORCH_NVCC_FLAGS="-Xfatbin -compress-all" \
    CMAKE_PREFIX_PATH="$(dirname $(which conda))/../" \
    FORCE_CUDA="1"

# Avoid Public GPG key error
# https://github.com/NVIDIA/nvidia-docker/issues/1631
RUN rm  -f /etc/apt/sources.list.d/cuda.list \
    && rm -f /etc/apt/sources.list.d/nvidia-ml.list \
    && apt-key del 7fa2af80 \
    && apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/3bf863cc.pub \
    && apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/7fa2af80.pub

# (Optional, use Mirror to speed up downloads)
# RUN sed -i 's/http:\/\/archive.ubuntu.com\/ubuntu\//http:\/\/mirrors.aliyun.com\/ubuntu\//g' /etc/apt/sources.list && \
#    pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# Install the required packages
RUN apt-get update \
    && apt-get install -y ffmpeg libsm6 libxext6 git ninja-build libglib2.0-0 libsm6 libxrender-dev libxext6 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install MMEngine, MMCV and MMDetection
RUN pip install openmim && \
    mim install "mmengine" "mmcv>=2.0.0rc4, < 2.2.0" "mmdet>=3.0.0"

# Install MMDetection3D
RUN conda clean --all \
    && git clone https://github.com/open-mmlab/mmdetection3d.git -b dev-1.x /mmdetection3d \
    && cd /mmdetection3d \
    && pip install --no-cache-dir -e .

# In your Dockerfile, add these lines
RUN apt-get update && apt-get install -y \
    libgl1-mesa-glx \
    libgl1-mesa-dri \
    libosmesa6 \
    libosmesa6-dev \
    xvfb

# Set environment variables
ENV DISPLAY=:0
ENV OPEN3D_USE_OSMESA=1


WORKDIR /mmdetection3d