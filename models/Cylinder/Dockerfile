# Use NVIDIA Jetson CUDA base image with PyTorch
# FROM nvcr.io/nvidia/l4t-pytorch:r35.3.1-pth2.0-py3
FROM dustynv/l4t-pytorch:r36.2.0
# FROM nvcr.io/nvidia/l4t-jetpack:r36.3.0
# Set non-interactive mode for apt
ENV DEBIAN_FRONTEND=noninteractive
ENV CUMM_CUDA_ARCH_LIST="8.7"

# Install basic dependencies
RUN apt-get update && apt-get install -y \
    python3 python3-dev python3-pip git wget unzip \
    vim tmux build-essential ffmpeg libsm6 libxext6 \
    libboost-all-dev cmake ninja-build && \
    rm -rf /var/lib/apt/lists/*

# ENV CC=gcc-9
# ENV CXX=g++-9
# Upgrade pip
RUN pip3 install --upgrade pip

# Clone Cylinder3D repository
WORKDIR /root
RUN git clone --recursive --depth 1 https://github.com/xinge008/Cylinder3D
WORKDIR /root/Cylinder3D

# Install Python dependencies
RUN pip3 install numpy pyyaml cython tqdm strictyaml numba opencv-python-headless
RUN pip3 install pccm==0.4.16 ccimport==0.4.4 wheel fire pybind11
RUN pip3 uninstall -y cumm spconv || true
RUN pip3 list | grep cumm || echo " No cumm found before install"
RUN pip3 list | grep spconv || echo " No spconv found before install"
RUN pip3 install torch-scatter -f https://data.pyg.org/whl/torch-2.0.0+nv22.10.html


# Clone and install cumm
RUN git clone -b v0.4.11 --recursive https://github.com/FindDefinition/cumm.git /root/cumm
# RUN sed -i '/#include <cuda_bf16.hpp>/s/^/\/\/ /' /root/cumm/include/tensorview/dtypes.h && \
#     sed -i '/struct bfloat16/,/};/s/^/\/\/ /' /root/cumm/include/tensorview/dtypes.h
RUN cd /root/cumm && \
    export CUMM_CUDA_ARCH_LIST="8.7" && \
    pip3 install -e .
RUN pip3 show cumm
# RUN find /usr/local/lib/python3.10/dist-packages -type d -name 'cumm' -exec mkdir -p {}/include \;
# RUN python3 -c "from cumm import tensorview as tv; tv.builder.build()" || echo "JIT build fallback skipped"

# Clone and install spconv
RUN git clone --recursive -b v1.2.1 https://github.com/yiy054/spconv.git /root/spconv && \
    cd /root/spconv && \
    pip3 install -e .

# Command '['ninja']' returned non-zero exit status 1.
# RUN git clone https://github.com/FindDefinition/cumm.git /root/cumm
# WORKDIR /root/cumm
# RUN git checkout v0.3.7
# ENV CUMM_CUDA_ARCH_LIST="8.7"
# ENV CUMM_CUDA_VERSION="11.4"
# RUN pip install -e . 
# RUN python3 -c "import cumm"

# RUN git clone https://github.com/traveller59/spconv.git /root/spconv
# WORKDIR /root/spconv
# RUN git checkout v2.2.6
# RUN sed -i 's/, "cumm>=0.3.7"//g' pyproject.toml
# RUN pip install -e . 
# RUN python3 -c "import spconv"

# RUN ls /usr/local/lib/python3.10/dist-packages/cumm*/cumm/core_cc* || echo "Still missing core_cc"

# RUN python3 -c "import spconv.pytorch; print('[Check] spconv OK')"

# Set CUDA environment variable
ENV CUDA_ROOT=/usr/local/cuda

# Set entrypoint
WORKDIR /root/Cylinder3D
CMD ["/bin/bash"]
