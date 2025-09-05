#FROM   ubuntu:16.04
#FROM    ubuntu:18.04
FROM    ubuntu:22.04

# Set keepalived version
ARG     KEEPALIVED_VERSION=2.3.4
# set docker version
ARG     DOCKER_VERSION=20.10.24
# set kubectl version
#ARG     KUBECTL_VERSION=1.15.3

# Install required packages
RUN     apt-get update \
    &&  apt-get upgrade -y \
    &&  apt-get install -y --no-install-recommends \
            python3 \
            python3-jinja2 \
            python3-ipy \
            python3-netifaces \
            python3-requests \
            vim \
            net-tools \
            iputils-ping \
            tzdata \
            telnet \
            curl \
            iptables \
            ipvsadm \
            openssl \
    &&  apt-get clean \
    &&  apt-get autoremove -y \
    &&  rm -rf \
            /var/lib/apt/lists/* \
            /tmp/* \
            /var/tmp/*

RUN     apt-get update \
    &&  apt-get install -y --no-install-recommends \
            autoconf \
            ipset \
            libnl-3-dev \
            libip6tc-dev \
            libip4tc-dev \
            gcc \
            libxtables-dev \
            libnfnetlink-dev \
            libssl-dev \
            libpopt-dev \
            make \
            musl-dev \
            libtool \
            ncurses-dev \
            libpci-dev \
            build-essential \
            libnl-genl-3-dev \
            powertop \
    &&  curl -o keepalived.tar.gz -SL http://keepalived.org/software/keepalived-${KEEPALIVED_VERSION}.tar.gz \
    &&  mkdir -p /tmp/keepalived-sources \
    &&  tar -xzf keepalived.tar.gz --strip 1 -C /tmp/keepalived-sources \
    &&  cd /tmp/keepalived-sources \
    &&  ./configure --disable-dynamic-linking \
    &&  make \
    &&  make install \
    &&  cd - \
    &&  mkdir -p /etc/keepalived \
    &&  cp -rf /usr/share/zoneinfo/PRC /etc/localtime \
    &&  apt-get clean \
    &&  apt-get autoremove -y \
    &&  rm -rf  \
                keepalived.tar.gz \
                /tmp/keepalived-sources \
                /var/lib/apt/lists/* \
                /tmp/* \
                /var/tmp/*

# Prepare various directories in /ka-data/
RUN     curl -LSs -o docker.tgz https://download.docker.com/linux/static/stable/x86_64/docker-${DOCKER_VERSION}.tgz \
##    &&  curl -LSs -o /usr/bin/kubectl https://dl.k8s.io/release/v${KUBECTL_VERSION}/bin/linux/amd64/kubectl \
    &&  curl -LSs -o /usr/bin/kubectl "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" \
    &&  tar -zxf docker.tgz \
    &&  cp -rf docker/docker /usr/bin/docker \
    &&  rm -rf docker* \
    &&  mkdir -p \
                /ka-data/scripts \
                /ka-templates

# Mount the configuration files, entry script and templates
# Templates
ADD     templates/keepalived.conf /ka-templates/keepalived.conf

# Configuration Files
ADD     scripts/check_haproxy.py /usr/local/bin/check_haproxy

# Entry Script
ADD     scripts/entry.py /usr/local/bin/entry

# Prepare various directories in /ka-data/
RUN     chmod +x \
                /usr/local/bin/check_haproxy \
                /usr/local/bin/entry \
                /usr/bin/docker \
                /usr/bin/kubectl

# Define working directory
WORKDIR /ka-data

# Define the default command as an entrypoint
ENTRYPOINT ["/usr/local/bin/entry"]

# Expose ports
# None (as it is using host interfaces it will not need any ports exposed)