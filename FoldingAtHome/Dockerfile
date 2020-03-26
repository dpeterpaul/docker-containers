FROM phusion/baseimage:latest

ENV FAH_VERSION_MAJOR=7.5
ENV PATH /usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility

RUN apt-get update &&\ 
    curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub | apt-key add - && \
    curl -o /tmp/cuda.deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-libraries-dev-10-2_10.2.89-1_amd64.deb &&\
    curl -o /tmp/fah.deb https://download.foldingathome.org/releases/public/release/fahclient/debian-stable-64bit/v7.5/latest.deb &&\
    apt-get install --no-install-recommends -y \
    curl adduser libc6 bzip2 ca-certificates ocl-icd-opencl-dev &&\
    mkdir -p /etc/fahclient/ &&\
    touch /etc/fahclient/config.xml &&\
    dpkg --install /tmp/fah.deb &&\
    dpkg --install --force-depends /tmp/cuda.deb &&\
    ldconfig -n /usr/local/cuda/lib64/stub &&\
    ln -s cuda-10.2 /usr/local/cuda &&\    
    rm --recursive --verbose --force /tmp/* /var/log/* /var/lib/apt/

RUN echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf &&\
    echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf

# Web viewer
EXPOSE 7396

ENTRYPOINT ["FAHClient", "--web-allow=0/0:7396", "--allow=0/0:7396"]
CMD ["--user=Anonymous", "--team=241561", "--gpu=true", "--smp=true", "--power=full", "--gpu-usage=100", "--cpu-usage=100"]