FROM alpine

# Include dist
ADD dist/ /root/dist/

# change timezone
RUN apk add tzdata &&\
    ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime &&\
    echo "Asia/Shanghai" > /etc/timezone
#test1
RUN echo `date`
#test2 
RUN python -c "import time,datetime;print datetime.datetime.fromtimestamp(time.time()).isoformat()+'Z'"

# Get and install dependencies & packages
RUN apk -U --no-cache add \
                       bash \
                       build-base \
                       git \
                       gmp-dev \
                       libcap \
                       libffi-dev \
                       mpc1-dev \
                       mpfr-dev \
                       openssl \
                       openssl-dev \
                       python \
                       python-dev \
                       py-mysqldb \
                       py-pip \
                       py-requests \
                       py-setuptools && \

# Setup user
    addgroup -g 2000 cowrie && \
    adduser -S -s /bin/ash -u 2000 -D -g 2000 cowrie && \

# Install cowrie
    mkdir -p /home/cowrie && \
    cd /home/cowrie && \
    git clone --depth=1 https://github.com/micheloosterhof/cowrie -b 1.5.3 && \
    cd cowrie && \
    mkdir -p log && \
    pip install --upgrade pip && \
    pip install py-bcrypt &&\
    pip install --upgrade -r requirements.txt && \

# Setup configs
    setcap cap_net_bind_service=+ep /usr/bin/python2.7 && \
    cp /root/dist/userdb.txt /home/cowrie/cowrie/etc/userdb.example && \
    cp /root/dist/base.py /home/cowrie/cowrie/src/cowrie/commands/base.py  && \
    cp /root/dist/output.py /home/cowrie/cowrie/src/cowrie/core/output.py &&\
    cp /root/dist/cowrie.cfg /home/cowrie/cowrie/cowrie.cfg && \
    chown cowrie:cowrie -R /home/cowrie/* /usr/lib/python2.7/site-packages/twisted/plugins && \


# Start Cowrie once to prevent dropin.cache errors upon container start caused by read-only filesystem
    su - cowrie -c "export PYTHONPATH=/home/cowrie/cowrie:/home/cowrie/cowrie/src && \
                    cd /home/cowrie/cowrie && \
                    /usr/bin/twistd --uid=2000 --gid=2000 -y cowrie.tac --pidfile cowrie.pid cowrie &" && \
    sleep 10 && \

# Clean up
    apk del --purge build-base \
                    git \
                    gmp-dev \
                    libcap \
                    libffi-dev \
                    mpc1-dev \
                    mpfr-dev \
                    openssl-dev \
                    python-dev \
                    py-mysqldb \
                    py-pip && \
    rm -rf /root/* && \
    rm -rf /var/cache/apk/* && \
    rm -rf /home/cowrie/cowrie/cowrie.pid

# Start cowrie
ENV PYTHONPATH /home/cowrie/cowrie:/home/cowrie/cowrie/src
WORKDIR /home/cowrie/cowrie
USER cowrie:cowrie
CMD ["/usr/bin/twistd", "--nodaemon", "-y", "cowrie.tac", "--pidfile", "/tmp/cowrie/cowrie.pid", "cowrie"]
