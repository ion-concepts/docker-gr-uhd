# Use phusion/baseimage as base image. To make your builds
# reproducible, make sure you lock down to a specific version, not
# to `latest`! See
# https://github.com/phusion/baseimage-docker/blob/master/Changelog.md
# for a list of version numbers.
#FROM phusion/baseimage:0.9.22
FROM armv7/armhf-ubuntu_core:16.04

ENV uhd_tag release_003_009_007
ENV threads 4

MAINTAINER ianb@ionconcepts.com version: 0.1

WORKDIR /opt
RUN echo "deb http://ports.ubuntu.com/ubuntu-ports/ xenial main universe" > /etc/apt/sources.list
RUN apt-get update && apt-get dist-upgrade -yf && apt-get clean && apt-get autoremove
RUN apt-get install -y git wget zip unzip \
 cmake build-essential git-core cmake g++ python-dev swig pkg-config net-tools\
 libboost-all-dev python-lxml python-sip-dev python-requests \
 libzmq3-dev python-mako python-pysnmp4 libusb-1.0-0 libusb-1.0-0-dev \
 inetutils-ping libncurses5-dev

RUN git clone https://github.com/EttusResearch/uhd.git
WORKDIR /opt/uhd/host
RUN git checkout ${uhd_tag}

RUN mkdir build \
 && cd build \
 && cmake ../ -DENABLE_B100=0  -DENABLE_B200=1 -DENABLE_E100=0 -DENABLE_E300=0 -DENABLE_EXAMPLES=1 -DENABLE_DOXYGEN=0 -DENABLE_MANUAL=0 -DENABLE_MAN_PAGES=0 -DENABLE_OCTOCLOCK=0 -DENABLE_ORC=0 -DENABLE_USRP1=0 -DENABLE_USRP2=1 -DENABLE_UTILS=1 -DENABLE_X300=1 \
 && make -j${threads} \
 && make install \
 && ldconfig

RUN /usr/local/lib/uhd/utils/uhd_images_downloader.py
