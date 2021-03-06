FROM debian:buster

ENV DEBIAN_FRONTEND noninteractive

# Prerequisites
RUN apt-get update && apt-get install -y \
    locales \
    rsyslog \
    amavisd-new \
    spamassassin \
    clamav-daemon \
    libnet-dns-perl \
    libmail-spf-perl \
    pyzor \
    razor \
    arj bzip2 cabextract cpio file gzip lhasa nomarch pax unrar-free unzip zip p7zip-full xzdec rpm2cpio \
    jq && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN locale-gen en_GB en_GB.UTF-8 && dpkg-reconfigure locales

RUN adduser clamav amavis && adduser amavis clamav

# Set up razor and pyzor
RUN su - amavis -s /bin/bash && razor-admin -create && razor-admin -register && pyzor discover

COPY ./config/conf.d /etc/amavis/conf.d
COPY ./config/rsyslog.conf /etc/rsyslog.conf

# Nice place for your settings
VOLUME ["/mail_settings"]

# Copy boot scripts
COPY boot /
COPY boot.d /boot.d
RUN chmod 755 /boot /boot.d/*

ENV AMAVIS=true

EXPOSE 10024
CMD ./boot; freshclam && /etc/init.d/clamav-freshclam start && /etc/init.d/clamav-daemon start; amavisd-new; rsyslogd -n
