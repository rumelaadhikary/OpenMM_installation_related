# OpenMM_installation_related
# This is a documentation to compile GCC, Python3 and SWIG, DOXYGEN and OpenMM from scratch
#********************************************************
# Compile GCC 7.5.0 (c++11 compiler)
$ unset LIBRARY_PATH CPATH C_INCLUDE_PATH PKG_CONFIG_PATH CPLUS_INCLUDE_PATH INCLUDE

# GCC depends on GMP, MPFR, MPC and ISL. Work for each of them one by one
#************************************************************
# /path/to/prefix = /home/avisek/sumitava/my_install_new/gcc (CMCD cluster)
#************************************************************
# GMP
$ wget ftp://gcc.gnu.org/pub/gcc/infrastructure/gmp-4.3.2.tar.bz2
$ tar -xvf gmp-4.3.2.tar
$ cd gmp-4.3.2
$ ./configure --disable-shared --enable-static --prefix=/path/to/prefix
$ make && make check && make install

# MPFR
$ wget ftp://gcc.gnu.org/pub/gcc/infrastructure/mpc-0.8.1.tar.gz
$ tar -xvf mpfr-2.4.2.tar
$ cd mpfr-2.4.2
$ ./configure --disable-shared --enable-static --prefix=/path/to/prefix --with-gmp=/path/to/prefix
$ make && make check && make install

# MPC
$ wget ftp://gcc.gnu.org/pub/gcc/infrastructure/mpc-0.8.1.tar.gz
$ tar zxvf mpc-0.8.1.tar.gz
$ cd mpc-0.8.1
$ ./configure --disable-shared --enable-static --prefix=/path/to/prefix --with-gmp=/path/to/prefix --with-mpfr=/path/to/prefix
$ make && make check && make install

# ISL
$ tar -xvf isl-0.18.0.tar.gz
$ cd isl-0.18.0
$  ./configure --disable-shared --enable-static --prefix=/path/to/prefix --with-gmp=/path/to/prefix
$ make && make install

# Now it's the time to compile GCC

$ cd gcc
$ mkdir build && cd build
$../configure --prefix=/path/to/prefix --enable-std=c++11 --enable-shared --disable-bootstrap --disable-libstdcxx-pch --enable-languages=all --enable-threads=posix --with-gmp=/path/to/prefix --with-mpfr=/path/to/prefix --with-mpc=/path/to/prefix --with-libisl=/path/to/prefix --disable-multilib --disable-werror
$ make -j20
$ make install

$ export CC=/path/to/prefix/bin/gcc
$ export CXX=/path/to/prefix/bin/g++
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/path/to/prefix/lib64
# *********************************************************

#********************************************************
# Python3 installation
$ tar -xvf Python-3.6.2.tar.xz
$ cd Python-3.6.2
$  ./configure --prefix=build-location/python3 --exec-prefix=build-location/python3 --enable-shared
$ make -j 10
$ make install

# Library linking
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:build-location/lib
#*********************************************************
# Cmake installation
get the tar file
$ tar -xvf cmake-3.15.4.tar.gz
$ ./bootstrap --prefix=/path/to/prefix/cmake
$ make -j 10
$ make install

#*********************************************************
# DOXYGEN compilation with installed gcc and Python

$ tar -xvf doxygen-vx.x.x.tar.gz
$ cd doxygen-vx.x.x
$ cmake ../ -DCMAKE_INSTALL_PREFIX=/home/avisek/my_install/doxygen-v1.8.16_python_3.6 -DPYTHON_EXECUTABLE=/home/avisek/my_install/Python-v3.6.6/bin/python3
$ make -j 10
$ make install

#**********************************************************
# SWIG compilation
$ tar -xvf swig-master.tar.gz
$ cd swig-master
$ ./configure --prefix=/path/to/prefix/swig --with-python=build-location/python3/bin/python3
$ make -j 10
$ make install

#**********************************************************
# OpenMM compilation
$ tar -xvf openmm-master.tar.gz
$ cd openmm-master
$ mkdir build
$ cd build
$ /path/to/prefix/cmake/bin/cmake ../ -DPYTHON_EXECUTABLE=build-location/python3/bin/python3 -DCMAKE_INSTALL_PREFIX=/path/to/prefix/openmm -DSWIG_EXECUTABLE=/path/to/prefix/swig/bin/swig -DCUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda -DOPENMM_BUILD_SHARED_LIB=ON -DOPENMM_BUILD_STATIC_LIB=ON -DOPENMM_BUILD_C_AND_FORTRAN_WRAPPERS=ON -DBUILD_TESTING=ON -DOPENMM_BUILD_CUDA_LIB=ON -DOPENMM_BUILD_OPENCL_LIB=OFF -DOPENMM_BUILD_CPU_LIB=ON -DOPENMM_BUILD_AMOEBA_PLUGIN=ON -DOPENMM_BUILD_RPMD_PLUGIN=ON -DOPENMM_BUILD_DRUDE_PLUGIN=ON -DOPENMM_BUILD_PME_PLUGIN=ON
$ make -j 20
$ make install
$ make PythonInstall
$ export PATH=$PATH:build-location/python3/lib/python3/dist-packages/simtk
$ make test

# Copy simtk folder to the my_install/openmm folder
$ cp -r build/python/build/lib.linux-x86_64-cpython-39/simtk /path/to/prefix/openmm
