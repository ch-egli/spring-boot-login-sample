FROM debian:jessie
MAINTAINER Christian Egli <christian.egli@gmx.net>

# install jdk-8 and supervisor
#
RUN echo 'deb http://ftp.debian.org/debian jessie-backports main' >> /etc/apt/sources.list
RUN apt-get update && apt-get install -y supervisor \
  && apt-get -t jessie-backports install -y openjdk-8-jdk

# install nano, python, nginx and curl
#
RUN apt-get install -y nano \
  && apt-get install -y python \
  && apt-get install -y nginx \
  && apt-get install -y curl

# install certbot (let's encrypt)
#
RUN echo 'deb http://ftp.debian.org/debian stretch main' >> /etc/apt/sources.list
RUN apt-get update && apt-get install -y -t stretch python-cffi-backend python-cryptography python-openssl
RUN apt-get install -y certbot -t jessie-backports

# is necessary for nano to work properly
#
ENV TERM xterm

# add cron script for backup
#
# RUN echo "07 01 * * * root  /root/backup.sh" >> /etc/crontab

# supervisor: create directory for logs and copy config file
#
RUN mkdir -p /var/log/supervisor
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf 

EXPOSE 80 443

# install logbook application
#
COPY sample-login-0.1.0.jar /sample-login.jar

# configure nginx
#
RUN unlink /etc/nginx/sites-enabled/default
RUN mkdir /var/www/test.snoopfish.ch
COPY test.snoopfish.ch.conf /etc/nginx/conf.d/test.snoopfish.ch.conf
COPY test.snoopfish.ch.conf.init /etc/nginx/conf.d/test.snoopfish.ch.conf.init

RUN apt-get install -y cron

COPY renew-certificate.sh /root/renew-certificate.sh
RUN chmod 775 /root/renew-certificate.sh
RUN echo "13 2 * * * root  /root/renew-certificate.sh" >> /etc/crontab

# see https://stackoverflow.com/questions/43323754/cannot-make-remove-an-entry-for-the-specified-session-cron
RUN sed -i '/session    required     pam_loginuid.so/c\#session    required   pam_loginuid.so' /etc/pam.d/cron

CMD ["/usr/bin/supervisord"]
