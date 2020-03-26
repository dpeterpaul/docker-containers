FROM phusion/baseimage:latest

ENV FAH_VERSION_MAJOR=7.5
ENV PATH /usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility

RUN apt-get update &&\ 
    curl -o /tmp/fah.deb https://download.foldingathome.org/releases/public/release/fahclient/debian-stable-64bit/v7.5/latest.deb &&\
    apt-get install --no-install-recommends -y \
    curl adduser libc6 bzip2 ca-certificates ocl-icd-opencl-dev libnvidia-compute-435 &&\
    mkdir -p /etc/fahclient/ &&\
    touch /etc/fahclient/config.xml &&\
    dpkg --install /tmp/fah.deb &&\
    ldconfig -n /usr/local/cuda/lib64/stub &&\
    apt-get auto-remove -y curl &&\
    rm --recursive --verbose --force /tmp/* /var/log/* /var/lib/apt/

# Web viewer
EXPOSE 7396

ENTRYPOINT ["FAHClient", "--web-allow=0/0:7396", "--allow=0/0:7396"]
CMD ["--user=Anonymous", "--team=241561", "--gpu=true", "--smp=true", "--power=full", "--gpu-usage=100", "--cpu-usage=100"]