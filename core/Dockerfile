FROM ubuntu:18.04
ARG USERID=1000
ARG GROUPID=1000
ENV DEBIAN_FRONTEND noninteractive

# Prerequisites
RUN apt update && apt install -y \
    locales \
    rsyslog \
    ssl-cert \
    postfix \
    dovecot-imapd \
    jq && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    echo "#!/bin/sh\nexit 0" > /usr/sbin/policy-rc.d

RUN locale-gen en_GB en_GB.UTF-8 && dpkg-reconfigure locales

# Postfix configuration
ADD ./config/postfix /etc/postfix/
RUN cat /etc/postfix/master-additional.cf >> /etc/postfix/master.cf

# Dovecot configuration
COPY ./config/dovecot /etc/dovecot/conf.d/
COPY ./config/rsyslog.conf /etc/rsyslog.conf

# Copy boot scripts
COPY boot /
COPY boot.d /boot.d
RUN chmod 755 /boot /boot.d/*

# Nice place for your settings
VOLUME ["/mail_settings"]

# Volume to store email
VOLUME ["/vmail"]

# Add user vmail that ownes mail
RUN groupadd -g $GROUPID vmail
RUN useradd -g vmail -u $USERID vmail -d /vmail -m

EXPOSE 25 143 587 993
CMD /boot; service postfix start; service dovecot start; rsyslogd -n
