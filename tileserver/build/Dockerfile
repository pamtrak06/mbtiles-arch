FROM ubuntu:16.04

#! mapnik2.2 don't like libgeos version from postgis2.1
#! libgeos-c1 (= 3.2.2-3ubuntu1) but 3.3.3-1.1~pgdg12.4+1 is to be installed
#! FROM helmi03/postgis:2.1

MAINTAINER Helmi <helmi03@gmail.com>

RUN apt-get update

# update ubuntu (16.04)
# Ubuntu Sources List Generator : https://repogen.simplylinux.ch/
#RUN echo "###### Ubuntu Main Repos" > /etc/apt/sources.list
#RUN echo "deb http://fr.archive.ubuntu.com/ubuntu/ xenial main universe" >> /etc/apt/sources.list
#RUN echo "###### Ubuntu Update Repos" >> /etc/apt/sources.list
#RUN echo "deb http://fr.archive.ubuntu.com/ubuntu/ xenial-security main universe " >> /etc/apt/sources.list
#RUN echo "deb http://fr.archive.ubuntu.com/ubuntu/ xenial-updates main universe " >> /etc/apt/sources.list
#RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe multiverse" > /etc/apt/sources.list
#RUN rm -rf /var/lib/apt/lists/*

# Installing Mapnik on Ubuntu >= (11.10) and < (14.04)
#RUN apt-get update
#RUN apt-get install -y -q software-properties-common python-software-properties python-pip libzmq-dev
#RUN echo "deb http://ppa.launchpad.net/mapnik/v3.0/ubuntu xenial-updates main " >> /etc/apt/sources.list
#RUN add-apt-repository -y ppa:mapnik/v2.2.0
#RUN apt-get update
#RUN apt-get install -y -q libmapnik libmapnik-dev mapnik-utils python-mapnik

RUN apt-get install -y -q git

# Installing Mapnik on Ubuntu (16.04)
# https://github.com/mapnik/mapnik/wiki/UbuntuInstallation
RUN git clone https://github.com/mapnik/mapnik mapnik-3.x --depth 10
WORKDIR mapnik-3.x
RUN git submodule update --init
RUN apt-get install -y python zlib1g-dev clang make pkg-config
RUN ./bootstrap.sh
# dependencies
RUN apt-get install -y software-properties-common python-software-properties
RUN add-apt-repository ppa:ubuntugis/ppa
RUN add-apt-repository ppa:cairo-dock-team/ppa
RUN apt-get update
RUN ./configure CUSTOM_CXXFLAGS="-D_GLIBCXX_USE_CXX11_ABI=0"
RUN make
RUN make test
RUN make install

WORKDIR /
#RUN pip install -q https://github.com/helmi03/TileStache/tarball/master
RUN pip install circus chaussette gevent
## when use PIL="decoder zip not available"
## Can use this workaround, but testing Pillow for now
## http://obroll.com/install-python-pil-python-image-library-on-ubuntu-11-10-oneiric/
#RUN pip uninstall -y PIL
#RUN pip install Pillow
#RUN yes | apt-get install -y ttf-mscorefonts-installer
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ADD circus.ini /etc/circus.ini
ADD tilestache.cfg /etc/tilestache/tilestache.cfg
ADD app.py /app.py
ADD start_tilestache.sh /usr/local/bin/start_tilestache

EXPOSE 9999

#!-- Docker have problem with upstart
#! ADD circus.cont /etc/init/circus.conf
#! CMD ["start", "circus"]

CMD ["start_tilestache"]
