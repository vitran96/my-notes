# Pypi
Alternative [[Python|Python]] runtime.
Written in [[RPython]]

# Poetry
[[Dependencies management]] tool for [[Python]]
This help us track and managing dependencies between package like [[npm]]

# PyTorch
[[Python]] API / Implementation for [[Torch]]

## Toolkit version for [[CUDA]]

[[CUDA]] version name to build with [[Nvidia]] [[GPU]]

|nvcc tag|TORCH_CUDA_ARCH_LIST|GPU Arch|Year|eg. GPU|
|---|---|---|---|---|
|sm_50, sm_52 and sm_53|5.0 5.1 5.3|[Maxwell](https://en.wikipedia.org/wiki/Maxwell_(microarchitecture)) support|2014|GTX 9xx|
|sm_60, sm_61, and sm_62|6.0 6.1 6.2|[Pascal](https://en.wikipedia.org/wiki/Pascal_(microarchitecture)) support|2016|10xx, Pxxx|
|sm_70 and sm_72|7.0 7.2|[Volta](https://en.wikipedia.org/wiki/Volta_(microarchitecture)) support|2017|Titan V|
|sm_75|7.5|[Turing](https://en.wikipedia.org/wiki/Turing_(microarchitecture)) support|2018|most 20xx|
|sm_80, sm_86 and sm_87|8.0 8.6 8.7|[Ampere](https://en.wikipedia.org/wiki/Ampere_(microarchitecture)) support|2020|RTX 30xx, Axx[xx]|
|sm_89|8.9|[Ada](https://en.wikipedia.org/wiki/Ada_Lovelace_(microarchitecture)) support|2022|RTX xxxx 40xx L4xx|
|sm_90, sm_90a|9.0 9.0a|[Hopper](https://en.wikipedia.org/wiki/Hopper_(microarchitecture)) support|2022|H100|

# AutoGPTQ
[[Python]] [[LLM]] [[GPT]] tool

## Build with different [[Torch]] and [[CUDA]]

Build sample
```Dockerfile
ARG AUTO_GPTQ_VERSION=0.7.1
ARG CUDA_VERSION=12.1.0
ARG CONDA_VERSION=py310_24.9.2-0
ARG TORCH_VERSION=2.3.1+cu121
ARG TORCHVISION_VERSION=0.18.1+cu121
ARG TORCH_CUDA_ARCH_LIST="8.9"
ARG BUILD_CUDA_EXT=1

FROM nvcr.io/nvidia/cuda:${CUDA_VERSION}-devel-ubuntu22.04 AS auto_gptq_builder

ARG TORCH_VERSION
ARG TORCHVISION_VERSION
ARG AUTO_GPTQ_VERSION
ARG CONDA_VERSION
ARG TORCH_CUDA_ARCH_LIST
ARG BUILD_CUDA_EXT

ENV TORCH_CUDA_ARCH_LIST=${TORCH_CUDA_ARCH_LIST}

ENV BUILD_CUDA_EXT=${BUILD_CUDA_EXT}
ENV PATH="/usr/local/miniconda3/bin:${PATH}"
ENV AUTO_GPTQ_ZIP_NAME="autogptq.tar.gz"

RUN apt update \
    && apt install -y wget \
    && wget "https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh" -O miniconda_installer.sh \
    && bash miniconda_installer.sh -b -p /usr/local/miniconda3 \
    && rm -f miniconda_installer.sh \
    && /usr/local/miniconda3/bin/pip install --no-cache-dir numpy torch==${TORCH_VERSION} setuptools wheel torchvision==${TORCHVISION_VERSION} ninja gekko pandas --find-links https://download.pytorch.org/whl/torch_stable.html \
    && wget "https://github.com/AutoGPTQ/AutoGPTQ/archive/refs/tags/v${AUTO_GPTQ_VERSION}.tar.gz" -O ${AUTO_GPTQ_ZIP_NAME} \
    && tar -xvzf ${AUTO_GPTQ_ZIP_NAME} \
    && cd "AutoGPTQ-${AUTO_GPTQ_VERSION}" \
    && /usr/local/miniconda3/bin/python setup.py bdist_wheel \
    && apt remove -y wget \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && mv dist/*.whl /tmp/ \
    && cd .. \
    && rm -rf "AutoGPTQ-${AUTO_GPTQ_VERSION}" \
    && rm ${AUTO_GPTQ_ZIP_NAME}
```

