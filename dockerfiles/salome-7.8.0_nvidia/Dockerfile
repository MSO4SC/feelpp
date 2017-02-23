FROM ubuntu:16.04
RUN apt-get update && \
apt-get install -y \
python2.7-minimal \
bash-completion \
wget \
firefox \
sudo \
mesa-utils binutils module-init-tools iputils-ping net-tools \
libxmu6 libglu1-mesa
#libfreetype6 libgl2ps0 libfreeimage3 \

# install nvidia driver
# apt-get install nvidia-352 fails
# see http://gernotklingler.com/blog/howto-get-hardware-accelerated-opengl-support-docker/
# and https://github.com/gklingler/docker3d
COPY NVIDIA-Linux-x86_64-352.63.run /tmp/NVIDIA-DRIVER.run
RUN sh /tmp/NVIDIA-DRIVER.run -a -N --ui=none --no-kernel-module && \
rm /tmp/NVIDIA-DRIVER.run
# should install gmsh-tetgen from Lncmi Debian repo to have med 3.2.0 support
# alternative use Salome Universal binaries
RUN ln -s /usr/bin/python2.7 /usr/bin/python
COPY Salome-V7_8_0-public.run /tmp/Salome-V7_8_0-public.run
RUN mkdir -p /opt/ && \
cd /opt && \
sh /tmp/Salome-V7_8_0-public.run -t /opt/salome -d && \
rm /tmp/Salome-V7_8_0-public.run
#COPY dlim8.key /opt/salome/appli_V7_8_0/env.d
#COPY distene.sh /opt/salome/appli_V7_8_0/env.d
#RUN chmod go+x /opt/salome/appli_V7_8_0/end.d/distene.sh
RUN apt-get autoremove \
&& apt-get clean \
&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
#set up user so that we do not run as root
# Changing the password does not work on certain OS and is not needed
# echo "feelpp:docker" | chpasswd && \
RUN useradd -m -s /bin/bash -G sudo feelpp && \
echo "feelpp ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

ENV HOME /home/feelpp
ENV PATH /opt/salome/SALOME-7.8.0-OPENSOURCE-UB16.04:$PATH
# to run
# xhost local:root
# docker run -ti --rm -e DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -v $HOME/.Xauthority:/home/developer/.Xauthority --net=host --pid=host --ipc=host salome-7.8.0:nvidia [salome]
USER feelpp
CMD ["bash"]
