# gateone Dockerfile
FROM ubuntu
# Ensure everything is up-to-date
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update --fix-missing && apt-get -y upgrade

# Install dependencies
RUN apt-get install -y \
    libkrb5-dev \
    python-pip \
    python-pil \
    python-imaging \
    python-setuptools \
    python-mutagen \
    python-pam \
    python-dev \
    telnet \
    wget \
    openssh-client && \
    apt-get -y clean && \
    apt-get -q -y autoremove

RUN pip install --upgrade futures tornado cssmin slimit psutil &&\
    pip install tornado==2.4.1 &&\
    pip install  kerberos

#clean up
RUN apt-get autoremove --purge -y && \
    apt-get clean && \
    rm -rf /root/* /var/lib/apt/lists/* /tmp/* /var/tmp/*

#install
RUN wget https://github.com/downloads/liftoff/GateOne/gateone_1.1-1_all.deb && \
    dpkg -i dpkg -i gateone_1.1-1_all.deb

#start
CMD ["python","/opt/gateone/gateone.py"]
