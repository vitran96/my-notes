[[Python]] [[LLM]] [[GPT]] tool

# Build with different [[Torch]] and [[CUDA]]

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

