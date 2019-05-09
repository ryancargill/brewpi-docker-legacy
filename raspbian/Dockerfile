FROM armhf/debian:stretch
MAINTAINER Ryan Cargill

# expose port 80, the default web server port
EXPOSE 80
# expose port 81, will proxy to 80 with password protection
EXPOSE 81

# install apt dependencies, build failing at apt-get update
RUN apt-get update && apt-get install -y \
  build-essential \
  python-dev \
  python-pip \
  git \
  php7.0-fpm \
  php7.0-mbstring \
  php7.0-xml \
  nginx \
  tzdata \
  nano \
  vim \
  dfu-util \
  apache2-utils \
  sudo

RUN sudo apt-get install -y arduino-core

# upgrade pip
RUN sudo pip install --upgrade pip

# install pip dependencies
RUN sudo pip install --upgrade pyserial psutil simplejson configobj gitpython

# remove existing content in web server root
RUN rm -rf /var/www/html

# clone brewpi repositories for legacy branches
RUN sudo git clone -b master --single-branch https://github.com/ryancargill/brewpi-script /home/brewpi
RUN sudo git clone -b master --single-branch https://github.com/ryancargill/brewpi-www /var/www/html

# clone the tag 0.2.0 as this has the arduino config code
RUN sudo git clone -b master --single-branch https://github.com/ryancargill/brewpi-tools /root/brewpi-tools && \
    cd /root/brewpi-tools && \
    sudo git checkout tags/0.2.0 -b tags/0.2.0

# copy nginx conf to nginx default
RUN mv /etc/nginx/sites-available/default /etc/nginx/sites-available/default.bak
COPY nginx-conf.txt /etc/nginx/sites-available/default

# set permissions on html directory
RUN sudo chown -R www-data:www-data /var/www/html; chmod g+s /var/www/html

# Copy entrypoint script
COPY entrypoint.sh /entrypoint.sh
RUN sudo chmod +x /entrypoint.sh

# Copy script that periodically checks the script status
COPY watcher.sh /watcher.sh
RUN sudo chmod +x /watcher.sh

WORKDIR /home/brewpi/

# this will always run
ENTRYPOINT ["/entrypoint.sh"]

# this will be run after entrypoint, unless overridden by the user
CMD ["/watcher.sh"]
