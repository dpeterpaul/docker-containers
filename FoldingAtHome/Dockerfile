FROM ubuntu:18.04 as base
ENV DEBIAN_FRONTEND=noninteractive \
    TINI_VERSION=v0.18.0

ARG VERSION=7.5.1
ARG VERSION_MAJOR=7.5

ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini.asc /tini.asc

#Add needed nvidia environment variables for https://github.com/NVIDIA/nvidia-docker
ENV NVIDIA_DRIVER_CAPABILITIES compute,video,graphics,utility
ENV NVIDIA_VISIBLE_DEVICES all
ENV CUDA_VERSION 10.2.89
ENV CUDA_PKG_VERSION 10-2=$CUDA_VERSION-
ENV PATH /usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64
ENV NVIDIA_REQUIRE_CUDA "cuda>=10.2 brand=tesla,driver>=384,driver<385 brand=tesla,driver>=396,driver<397 brand=tesla,driver>=410,driver<411 brand=tesla,driver>=418,driver<419"

FROM nvidia/opencl:runtime-ubuntu18.04

ARG BUILD_DATE
ARG VCS_REF
ARG VERSION

WORKDIR /app

RUN echo "deb http://ftp.debian.org/debian testing main" >> /etc/apt/sources.list

# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get install --no-install-recommends -y \
      curl \
      gnupg \
      bzip2 \
      ca-certificates \
    && curl -o /tmp/fah.deb https://download.foldingathome.org/releases/public/release/fahclient/debian-stable-64bit/v${VERSION_MAJOR}/fahclient_${VERSION}_amd64.deb \
    && dpkg --force-depends --install /tmp/fah.deb \
    && gpg --keyserver hkp://ha.pool.sks-keyservers.net --recv-keys "595E85A6B1B4779EA4DAAEC70B588DFF0527A9B7" || \
      gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "595E85A6B1B4779EA4DAAEC70B588DFF0527A9B7" || \
      gpg --keyserver hkp://ipv4.pool.sks-keyservers.net --recv-keys "595E85A6B1B4779EA4DAAEC70B588DFF0527A9B7" || \
      gpg --keyserver hkp://pgp.mit.edu:80 --recv-keys "595E85A6B1B4779EA4DAAEC70B588DFF0527A9B7" ; \
      gpg --batch --verify /tini.asc /tini \
    && chmod +x /tini  \
    && curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub | apt-key add - \
    && echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64 /" > /etc/apt/sources.list.d/cuda.list \
    && echo "deb https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64 /" > /etc/apt/sources.list.d/nvidia-ml.list \
    && apt-get purge --autoremove -y curl \
    && rm -rf /var/lib/apt/lists/*


# hadolint ignore=DL3008
RUN mkdir -p /usr/share/doc/fahclient/ \
    && touch /usr/share/doc/fahclient/sample-config.xml \
    && apt-get update \
    && apt-get install --no-install-recommends -y \
      libcudnn7=$CUDNN_VERSION-1+cuda10.2 \
      ocl-icd-opencl-dev \
    && apt-mark hold libcudnn7 \
    && apt-get clean autoclean \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/ /var/lib/dpkg/ /var/lib/cache/ /var/lib/log/ \
    && groupadd -r fah \
    && useradd -r -g fah fah \
    && chown -R fah /app

# For libraries in the cuda-compat-* package: https://docs.nvidia.com/cuda/eula/index.html#attachment-a
RUN apt-get update && apt-get install -y --no-install-recommends \
        cuda-cudart-$CUDA_PKG_VERSION \
cuda-compat-10-2 && \
ln -s cuda-10.2 /usr/local/cuda && \
    rm -rf /var/lib/apt/lists/*

# Required for nvidia-docker v1
RUN echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf && \
    echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf

EXPOSE 36330 73963
USER fah

ENTRYPOINT ["/bin/tini", "--", "/usr/bin/FAHClient", "--web-allow=0/0:7396", "--allow=0/0:7396", "--smp=true"]
CMD ["--user=Anonymous", "--team=241561", "--power=full", "--gpu-usage=100", "--cpu-usage=100"]