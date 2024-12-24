FROM ubuntu:focal

# RUN echo "deb http://old-releases.ubuntu.com/ubuntu/ lunar-updates main universe multiverse" > /etc/apt/sources.list && \
#     echo "deb http://old-releases.ubuntu.com/ubuntu/ lunar main universe multiverse" >> /etc/apt/sources.list

# RUN apt-get update && \
#     apt-get install -y software-properties-common gnupg curl && \
#     add-apt-repository "deb http://dev.ubuntu.com/ubuntu focal universe"
    # apt-get dist-upgrade -y
ARG DEBIAN_FRONTEND=noninteractive
# Get Ubuntu updates
USER root
RUN apt-get update && \
    apt-get upgrade -y -o Dpkg::Options::="--force-confold" && \
    apt-get -y install locales sudo && \
    echo "C.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen

# Set locale environment
ENV LC_ALL=C.UTF-8 \
    LANG=C.UTF-8 \
    LANGUAGE=C.UTF-8

COPY set-home-permissions.sh /etc/my_init.d/set-home-permissions.sh

# Set up user so that we do not run as root
# See https://github.com/phusion/baseimage-docker/issues/186
# Disable forward logging
# Add script to set up permissions of home directory on myinit
# Run ldconfig so that /usr/local/lib is in the default search
# path for the dynamic linker.
RUN groupadd docker
RUN useradd -m -s /bin/bash -G sudo,docker fenics && \
    echo "fenics:docker" | chpasswd && \
    echo "fenics ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers && \
    echo "cat /home/fenics/WELCOME" >> /home/fenics/.bashrc && \
    chmod +x /etc/my_init.d/set-home-permissions.sh && \
    ldconfig

WORKDIR /tmp
    # install non-python dependencies - splitting these up reduces the size of individual image layers
# RUN apt-get update && \
# apt-get -y --with-new-pkgs \
#     -o Dpkg::Options::="--force-confold" -o Dpkg::Options::="--force-confdef" upgrade && \
# apt-get -y install \
#                    git \
#                    gcc \
#                    g++ \
#                    gfortran \
#                    cmake \
#                    openmpi-bin \
#                    libopenmpi-dev \
#                    flex \
#                    libblas-dev \
#                    liblapack-dev \
#                    swig3.0 \
#                    libeigen3-dev \
#                    libboost-dev \
#                    libboost-filesystem-dev \
#                    libboost-program-options-dev \
#                    libboost-iostreams-dev \
#                    libboost-timer-dev \
#                    libvtk7-dev \
#                    libvtk7-java \
#                    libvtk7-qt-dev \
#                    libhdf5-openmpi-dev \
#                    gmsh \
#                    environment-modules && \
# apt-get clean && \
# rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN apt-get -y install python3 fenics build-essential
# install all python dependencies
RUN apt-get -y install \
                   python3-pip \
                   python3-vedo \ 
                   python3-scipy \
                   python3-torch \
                   python3-matplotlib

# RUN apt-get install libgl1-mesa-dev libgl1-mesa-glx xvfb -y --no-install-recommends \
#   && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false 

RUN apt-get clean && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

USER root
# COPY set_xvfb.sh /home/fenics/
# RUN chmod 777 /home/fenics/set_xvfb.sh
COPY pip.conf /home/fenics/.config/pip/
USER fenics
ENV FENICS_HOME /home/fenics
RUN touch $FENICS_HOME/.sudo_as_admin_successful && \
    mkdir $FENICS_HOME/shared
VOLUME /home/fenics/shared

# Print something nice on entry.
COPY WELCOME $FENICS_HOME/WELCOME

WORKDIR /home/fenics
USER fenics

# RUN /home/fenics/set_xvfb.sh
ENTRYPOINT ["/bin/bash","-l","-c"]
CMD ["/bin/bash","-i"]
