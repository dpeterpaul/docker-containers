FROM ubuntu:latest

ENV FAH_VERSION_MAJOR=7.5
ENV DEBIAN_FRONTEND=noninteractive

RUN groupadd -g 9999 folder && \
    useradd -r -b /home -u 9999 -g folder folder

RUN mkdir /home/folder && chown folder:folder /home/folder && chmod 700 /home/folder

ARG IMAGE_NAME
FROM ${IMAGE_NAME}:10.2-base-ubuntu18.04
LABEL maintainer "NVIDIA CORPORATION <cudatools@nvidia.com>"

ENV CUDA_VERSION 10.2.89
ENV CUDA_PKG_VERSION 10-2=$CUDA_VERSION-1
ENV NCCL_VERSION 2.5.6

RUN apt-get update && apt-get install -y --no-install-recommends \
    cuda-libraries-$CUDA_PKG_VERSION \
cuda-nvtx-$CUDA_PKG_VERSION \
libcublas10=10.2.2.89-1 \
libnccl2=$NCCL_VERSION-1+cuda10.2 && \
    apt-mark hold libnccl2 && \
    rm -rf /var/lib/apt/lists/*


RUN apt-get update && apt-get install --no-install-recommends -y \
        curl adduser bzip2 ca-certificates &&\
        curl -o /tmp/fah.deb https://download.foldingathome.org/releases/public/release/fahclient/debian-stable-64bit/v7.5/latest.deb &&\
        mkdir -p /etc/fahclient/ &&\
        touch /etc/fahclient/config.xml &&\
        dpkg --install /tmp/fah.deb &&\
        apt-get remove -y curl &&\
        apt-get autoremove -y &&\
        rm --recursive --verbose --force /tmp/* /var/log/* /var/lib/apt/

# Web viewer
EXPOSE 7396

USER folder

WORKDIR /home/folder

ENTRYPOINT ["FAHClient", "--web-allow=0/0:7396", "--allow=0/0:7396"]
CMD ["--user=Anonymous", "--team=241561", "--gpu=true", "--smp=true", "--power=full", "--gpu-usage=100", "--cpu-usage=100"]