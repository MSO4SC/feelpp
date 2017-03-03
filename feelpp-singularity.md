# Feel++ on CentOS (or RH) 6 with Singularity
## Singularity installation
`Linux localhost.localdomain 2.6.32-642.15.1.el6.x86_64 #1 SMP Fri Feb 24 14:31:22 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux`

(Run everything as root)
* Install dev tools: `yum groupinstall "Development Tools"`
* clone the repo: `git clone https://github.com/singularityware/singularity.git`
* start the build:
```
cd singularity
./autogen.sh
./configure --prefix=/usr/local --sysconfdir=/etc
make
make install
```

## Feelpp image creation
* create a 6GiB image, big enough to contain feelpp-toolboxes, in a common place that is readable by users:
```
mkdir /singularity
singularity create --size 6000 /singularity/feelpp.img
```

* import the current feelpp-toolboxes from dockerhub (it will download roughly 6 GiB so it might take a while): `singularity --verbose import/singularity/feelpp.img docker://feelpp/feelpp-toolboxes:latest`

* start a singularity shell without bindings and with write support:
`singularity shell -c -w /singularity/feelpp.img`
* move the feelpp files away from the $HOME dir, since singularity will bind the users actual $HOME over that:
`mv /home/feelpp /singularity/feelpp`
* `exit` from the container

## running the toolboxes
An example runscript:
```
#!/bin/sh

# Set Feel++ env vars
# write output files to the feel dir in users $HOME
unset FEELPP_REPOSITORY
export FEELPP_HOME=/usr/local
export OPENBLAS_NUM_THREADS=1
export OPENBLAS_VERBOSE=0
export FEELPP_DEP_INSTALL_PREFIX=
export CC=
export CXX=clang++
export LD_LIBRARY_PATH=${FEELPP_DEP_INSTALL_PREFIX}/lib:${FEELPP_DEP_INSTALL_PREFIX}/lib/paraview-5.0:$LD_LIBRARY_PATH
export PKG_CONFIG_PATH=${FEELPP_DEP_INSTALL_PREFIX}/lib/pkgconfig:$PKG_CONFIG_PATH
export PYTHONPATH=${FEELPP_DEP_INSTALL_PREFIX}/lib/python2.7/site-packages:${FEELPP_DEP_INSTALL_PREFIX}/lib/paraview-5.0/site-packages:$PYTHONPATH
export MANPATH=${FEELPP_DEP_INSTALL_PREFIX}/share/man:$MANPATH

mpirun -v -display-map -np 4 feelpp_toolbox_fluid_2d --config-file /opt/feelpp/Testcases/CFD/lid-driven-cavity/cfd2d.cfg

```
Save this as runscript.sh, make it runnable and then run as:
`singularity exec /singularity/feelpp.img ./runscript.sh`
