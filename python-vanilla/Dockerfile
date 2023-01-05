# Specify which version of Ubuntu we want to run
ARG UBUNTU_VERSION=22.04

# Use ubuntu as base image; version specified in UBUNTU_VERSION 
# Julia version only!
# See reason for platform arg: https://stackoverflow.com/questions/71040681/qemu-x86-64-could-not-open-lib64-ld-linux-x86-64-so-2-no-such-file-or-direc
# FROM --platform=linux/amd64 ubuntu:${UBUNTU_VERSION} 
# For Julia try using arch64 linux (https://julialang.org/downloads/ see "Generic Linux on ARM")
# see also https://discourse.julialang.org/t/julia-on-m1-macs/55448/21
FROM ubuntu:${UBUNTU_VERSION} 

# After every FROM statement, ARGs are collected and made unavailable
# Need to (re)declare args
ARG UBUNTU_VERSION=22.04

# Specify which version of python we want to use
ARG PYTHON_VERSION=3.11
# Specify which system packages we want to install
# TODO: identify common packages
ARG PKGS="apt-transport-https bash ca-certificates gnupg jq software-properties-common curl git unzip zip python${PYTHON_VERSION} python3-pip python3-dev python3-virtualenv apt-utils build-essential tree gpg snapd shellcheck wget"
ARG JULIA_PKGS="wget"

# Commands to install julia
# wget https://julialang-s3.julialang.org/bin/linux/aarch64/1.8/julia-1.8.4-linux-aarch64.tar.gz
# tar zxvf julia-1.8.4-linux-aarch64.tar.gz
# then execute julia:
# /root/julia-1.8.4/bin/julia 

# Specify the target folder (in container) we will use for workspace
ARG WORK_DIR=/opt/finance-helper
# Specify name of image
ARG IMAGE_NAME="python_vanilla"

# Specify user (should be default anyway)
USER root

# TODO: have separate clauses instead of separate Dockerfiles to handle different 
# environments (Python, Julia, C, R)

# Apt Updates. I read somewhere that it's better than 
# apt-get.
# TODO: verify and link sources :')
RUN apt update && \
    apt install --no-install-recommends -y ${PKGS} && \
    apt upgrade -y && \
    apt autoremove --purge -y

# TODO: set up virtualenv; allows for clean separation and is best practice
# Probably still use pyenv since we are using ubuntu? Link supporting sources
# and doco

# TODO: set up to handle different cpu architectures. e.g. arch64

# Install Python using specified PYTHON_VERSION
RUN apt install python${PYTHON_VERSION} -y && \
    apt install python3-pip -y

# Ensure that running python3 or python in command line will 
# point to /usr/bin/python3.11 (the version we installed)
# RUN update-alternatives  --set python3 /usr/bin/python${PYTHON_VERSION}
# RUN update-alternatives  --set python /usr/bin/python${PYTHON_VERSION}

# Create working directory and then switch into it
RUN mkdir -p ${WORK_DIR}
WORKDIR ${WORK_DIR}

# Copy over requirements.txt file to install packages at build time
COPY ./requirements.txt .

# TODO: automatically clone git repo, make it a dynamic arg?
# TODO: handle git credentials. So we don't have annoying personal access
# token caching >:(

# TODO: set up jupyter with nice dark theme :)

# Install requirements from requirements.txt file
RUN pip install -r ./requirements.txt

# cleanup
RUN apt autoremove --purge -y && \
    find /opt /usr/lib -name __pycache__ -print0 | xargs --null rm -rf && \
    rm -rf /var/lib/apt/lists/*

# Create user ubuntu
# RUN useradd -ms /bin/bash ubuntu
# USER ubuntu

# TODO: automate attaching to running container :')

# On entrypoint run /bin/bash
ENTRYPOINT ["/bin/bash"]
