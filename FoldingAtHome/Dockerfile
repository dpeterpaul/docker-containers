FROM phusion/baseimage:latest

ENV FAH_VERSION_MAJOR=7.5

RUN apt-get update && apt-get install --no-install-recommends -y \
    curl adduser bzip2 ca-certificates gcc-offload-nvptx &&\
    curl -o /tmp/fah.deb https://download.foldingathome.org/releases/public/release/fahclient/debian-stable-64bit/v7.5/latest.deb &&\
    mkdir -p /etc/fahclient/ &&\
    touch /etc/fahclient/config.xml &&\
    dpkg --install /tmp/fah.deb &&\
    apt-get remove -y curl &&\
    apt-get autoremove -y &&\
    rm --recursive --verbose --force /tmp/* /var/log/* /var/lib/apt/

# Web viewer
EXPOSE 7396

ENTRYPOINT ["FAHClient", "--web-allow=0/0:7396", "--allow=0/0:7396"]
CMD ["--user=Anonymous", "--team=241561", "--gpu=true", "--smp=true", "--power=full", "--gpu-usage=100", "--cpu-usage=100"]