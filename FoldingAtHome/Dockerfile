FROM phusion/baseimage:latest

ENV FAH_VERSION_MAJOR=7.5
ENV CUDA_VERSION 10.2.89
ENV CUDA_PKG_VERSION 10-2=$CUDA_VERSION-1
ENV PATH /usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility

RUN apt-get update &&\ 
    curl -o /tmp/fah.deb https://download.foldingathome.org/releases/public/release/fahclient/debian-stable-64bit/v7.5/latest.deb &&\
    curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub | apt-key add - && \
    echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64 /" > /etc/apt/sources.list.d/cuda.list && \
    echo "deb https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64 /" > /etc/apt/sources.list.d/nvidia-ml.list && \
    apt-get install --no-install-recommends -y \
    curl adduser libc6 bzip2 ca-certificates cuda-libraries-10-2-10.2.89-1 &&\
    mkdir -p /etc/fahclient/ &&\
    touch /etc/fahclient/config.xml &&\
    dpkg --install /tmp/fah.deb &&\
    apt-get autoclean &&\
    apt-get purge --autoremove remove -y curl &&\
    ldconfig -n /usr/local/cuda/lib64/stub &&\
    ln -s cuda-10.2 /usr/local/cuda &&\    
    rm --recursive --verbose --force /tmp/* /var/log/* /var/lib/apt/

RUN echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf && \
    echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf

# Web viewer
EXPOSE 7396

ENTRYPOINT ["FAHClient", "--web-allow=0/0:7396", "--allow=0/0:7396"]
CMD ["--user=Anonymous", "--team=241561", "--gpu=true", "--smp=true", "--power=full", "--gpu-usage=100", "--cpu-usage=100"]