FROM alpine
MAINTAINER MO

# Include dist
ADD disk/ /root/dist/

# Setup env and apt
RUN apk -U upgrade && \
    apk add bash curl git libc6-compat libzmq openjdk8-jre procps nodejs wget && \
    
    
# Get and install packages   
  ## ES
    cd /root/dist/ && \
    mkdir -p /usr/share/elasticsearch/ && \
    wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.6.9.tar.gz && \
    tar xvfz elasticsearch-5.6.9.tar.gz --strip-components=1 -C /usr/share/elasticsearch/ && \

   ## logstash
    git clone --depth=1 https://github.com/dtag-dev-sec/listbot /etc/listbot && \
    cd /root/dist/ && \
    mkdir -p /usr/share/logstash/ && \
    wget https://artifacts.elastic.co/downloads/logstash/logstash-5.6.9.tar.gz && \
    wget http://geolite.maxmind.com/download/geoip/database/GeoLite2-ASN.tar.gz && \
    tar xvfz logstash-5.6.9.tar.gz --strip-components=1 -C /usr/share/logstash/ && \
    /usr/share/logstash/bin/logstash-plugin install logstash-filter-translate && \
    /usr/share/logstash/bin/logstash-plugin install logstash-output-syslog && \
    tar xvfz GeoLite2-ASN.tar.gz --strip-components=1 -C /usr/share/logstash/vendor/bundle/jruby/1.9/gems/logstash-filter-geoip-4.3.1-java/vendor/ && \
  ## kibana
    mkdir -p /usr/share/kibana/ && \
    wget https://artifacts.elastic.co/downloads/kibana/kibana-5.6.9-linux-x86_64.tar.gz && \
    tar xvfz kibana-5.6.9-linux-x86_64.tar.gz --strip-components=1 -C /usr/share/kibana/ && \
    rm /usr/share/kibana/node/bin/node && \
    rm /usr/share/kibana/node/bin/npm && \
    ln -s /usr/bin/node /usr/share/kibana/node/bin/node && \
    ln -s /usr/bin/npm /usr/share/kibana/node/bin/npm && \
    
# Add and move files
    ##ES
    cd /root/dist/ && \
    mkdir -p /usr/share/elasticsearch/config && \
    cp elasticsearch.yml /usr/share/elasticsearch/config/ && \
    
    ## logstash
    mkdir -p /etc/logstash/conf.d && \
    cp logstash.conf /etc/logstash/conf.d/ && \
    cp elasticsearch-template-es5x.json /usr/share/logstash/vendor/bundle/jruby/1.9/gems/logstash-output-elasticsearch-7.4.2-java/lib/logstash/outputs/elasticsearch/ && \

   ## kibana
    cd /root/dist/ && \
    cp kibana.svg /usr/share/kibana/src/ui/public/images/kibana.svg && \
    cp kibana.svg /usr/share/kibana/src/ui/public/icons/kibana.svg && \
    cp elk.ico /usr/share/kibana/src/ui/public/assets/favicons/favicon.ico && \
    cp elk.ico /usr/share/kibana/src/ui/public/assets/favicons/favicon-16x16.png && \
    cp elk.ico /usr/share/kibana/src/ui/public/assets/favicons/favicon-32x32.png && \
    cp create_kibana_index.js /usr/share/kibana/src/core_plugins/elasticsearch/lib/ && \
    cd / && \

# Setup user, groups and configs
    #ES
    addgroup --gid 2000 tpot && \
    adduser --system --no-create-home --shell /bin/bash --uid 2000 --disabled-password --disabled-login --gid 2000 tpot && \
    chown -R tpot:tpot /usr/share/elasticsearch/ && \
    ## kibana
    sed -i 's/#server.basePath: ""/server.basePath: "\/kibana"/' /usr/share/kibana/config/kibana.yml && \
    sed -i 's/#kibana.defaultAppId: "discover"/kibana.defaultAppId: "dashboards"/' /usr/share/kibana/config/kibana.yml && \
    sed -i 's/#server.host: "localhost"/server.host: "0.0.0.0"/' /usr/share/kibana/config/kibana.yml && \
    sed -i 's/#elasticsearch.url: "http:\/\/localhost:9200"/elasticsearch.url: "http:\/\/elasticsearch:9200"/' /usr/share/kibana/config/kibana.yml && \
    /usr/share/kibana/bin/kibana 2>&1 | grep -m 1 "Optimization of bundles" && \
# Clean up
    rm -rf /root/* && \
    apt-get purge wget git -y && \
    apt-get autoremove -y && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
    

#start elk
CMD ["/usr/share/elasticsearch/bin/elasticsearch"]
CMD /usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/logstash.conf
CMD ["/usr/share/kibana/bin/kibana"]
CMD [ "/usr/local/bin/start.sh" ]
