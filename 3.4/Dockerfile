FROM alpine:3.4

MAINTAINER nimmis <kjell.havneskold@gmail.com>

COPY root/. /

RUN echo "@community http://dl-4.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories && \
    echo "@testing http://dl-4.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories && \
    echo "@edge http://dl-4.alpinelinux.org/alpine/edge/main" >> /etc/apk/repositories && \
    # read packages and update
    apk update && apk upgrade && \
    # fix old apk-tools
    apk add --upgrade apk-tools@edge && \
    # add packages
    apk add ca-certificates rsyslog logrotate runit@community && \
    # Make info file about this build
    mkdir -p /etc/BUILDS/ && \
    printf "Build of nimmis/alpine-micro:3.3, date: %s\n"  `date -u +"%Y-%m-%dT%H:%M:%SZ"` > /etc/BUILDS/alpine-micro && \
    # install extra from github, including replacement for process 0 (init)
    # add extra package for installation
    apk add curl && \
    cd /tmp && \
    # Install utils and init process
    curl -Ls https://github.com/nimmis/docker-utils/archive/master.tar.gz | tar xfz - && \
    ./docker-utils-master/install.sh && \
    rm -Rf ./docker-utils-master && \
    # Install backup support
    curl -Ls https://github.com/nimmis/backup/archive/master.tar.gz | tar xfz - && \
    ./backup-master/install.sh all && \
    rm -Rf ./backup-master && \
    # remove extra packages
    apk del curl && \
    # fix container bug for syslog
    sed  -i "s|\*.emerg|\#\*.emerg|" /etc/rsyslog.conf && \
    sed -i 's/$ModLoad imklog/#$ModLoad imklog/' /etc/rsyslog.conf && \
    sed -i 's/$KLogPermitNonKernelFacility on/#$KLogPermitNonKernelFacility on/' /etc/rsyslog.conf && \
    # disable root login
    sed -i 's/^root\:\:/root\:\!\:/' /etc/shadow  && \
    # remove cached info
    rm -rf /var/cache/apk/*

# Expose backup volume
VOLUME /backup

# Set environment variables.
ENV HOME /root

# Define working directory.
WORKDIR /root

# Define default command.
CMD ["/boot.sh"]

