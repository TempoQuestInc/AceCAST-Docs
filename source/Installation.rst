.. _Installation:

Installation
############

Prerequisites 
=============

Before attempting to install AceCAST users should ensure they have access to a compatible system with NVIDIA GPUs 
(see :ref:`requirementslink`).

Contact and User Support
========================

Please do not hesitate to contact support@tempoquest.com for any issues or questions that you may have during the
installation process.

Download AceCAST
================

To download AceCAST, navigate to the :ref:`releaseslink` page and follow the download instructions for the newest
release of AceCAST. Once the AceCAST tarball has been downloaded, unpack it using the **tar** command as follows.

::

    [acecast-user@localhost]$ tar -xf AceCASTv1.3+linux.x86_64.tar.gz 

Package Contents
================

Once unpacked you should see a new `AceCAST` directory with a file structure similar to this:

::

    AceCAST
    ├── benchmarks 
    │     - a directory intended to hold AceCAST benchmark test 
    │       case data
    ├── README
    ├── run
    │   ├── acecast-advisor.sh
    │   │     - script intended to help users determine namelist 
    │   │       support and computational scaling extents
    │   ├── acecast.exe
    │   │     - AceCAST GPU-WRF executable -- this is the GPU
    │   │       equivalent of wrf.exe
    │   ├── CCN_ACTIVATE.BIN
    │   ├── diffwrfs
    │   │     - utility for comparing WRF/AceCAST input and
    │   │       output files
    │   ├── GENPARM.TBL
    │   ├── gpu-launch.sh
    │   │     - MPI wrapper script for assigning GPUs to each
    │   │       MPI process
    │   ├── LANDUSE.TBL
    │   ├── ndown.exe
    │   │     - precompiled ndown.exe executable (see WRF 
    │   │       documentation) -- runs on CPU
    │   ├── ozone.formatted
    │   ├── ozone_lat.formatted
    │   ├── ozone_plev.formatted
    │   ├── README.namelist
    │   ├── real.exe
    │   │     - precompiled real.exe executable -- runs on CPU
    │   ├── RRTMG_LW_DATA
    │   ├── RRTMG_SW_DATA
    │   ├── SOILPARM.TBL
    │   ├── VEGPARM.TBL
    │   └── wrf_stats_ck
    │         - utility script for getting basic performance
    │           metrics of AceCAST/WRF runs
    └── scripts
        └── install_deps.sh
              - script for installing the shared libraries
                that are required to run the various 
                executables in this package

Many of these files should already look familiar to seasoned WRF users. We have added short annotations above describing
files and directories that we believe are worth noting.


Acquire a License
=================

AceCAST is a licensed software package and as such requires a valid license to run. A 60-day trial license can be acquired
by registering at the `TempoQuest Registration Page <https://tempoquest.com/binary-executable-for-64-bit-linux-x86/>`_. 
After registering you should recieve an email containing your trial license. We suggest placing this file in the 
`AceCAST/run` directory. If your 60-day trial has ended please contact support@tempoquest.com to request an extension 
and/or a quote.


Running the Installation Script
===============================

To ensure compatibility with the precompiled executables in the AceCAST distribution it is highly recommend to use the 
dependency installation script to install the various compilers and shared libraries required at runtime (found at 
`AceCAST/scripts/install_deps.sh`). This script installs the following components:

    - NVIDIA 20.7 (formerly PGI) compiler suite (includes NVIDIA-compiled, CUDA-aware OpenMPI build)
    - HDF5
    - NetCDF
    - Parallel NetCDF
    - Generates a script that ensures the proper runtime environment for running AceCAST

::

    Usage:  ./install_deps.sh [--install-secondary-packages-rpm] [--install-secondary-packages-deb]

This will prompt the user to specify an installation directory for these dependencies (default: `$HOME/tqi-build/20.7`). 
It will also generate a script (default: `$HOME/tqi-build/20.7/env.sh`) that should be sourced to setup your runtime 
environment correctly prior to running any of the executables contained in the AceCAST distribution (`i.e. real.exe, 
acecast.exe, wrf.exe, diffwrfs, etc.`).

.. important::
   This process can take up to an hour to complete and requires ~16GB of storage.

Example:

::

    [acecast-user@localhost]$ cd AceCAST/scripts/
    [acecast-user@localhost]$ ./install_deps.sh 
    Welcome to the AceCAST Package Installer
    Checking for supported architecture
    Supported architecture detected arch=Linux_x86_64
    Enter directory you would like to install NVIDIA HPC SDK and associated packages in:
    /home/ec2-user/tqi-build/20.7
    Installing AceCAST dependent packages in /home/ec2-user/tqi-build/20.7
    Installing NVIDIA HPC SDK version 20.7
    Downloading file - https://developer.download.nvidia.com/hpc-sdk/20.7/nvhpc_2020_207_Linux_x86_64_cuda_multi.tar.gz
    Extracting archive - nvhpc_2020_207_Linux_x86_64_cuda_multi.tar.gz
    .................................successfully extracted archive - nvhpc_2020_207_Linux_x86_64_cuda_multi.tar.gz
    Successfully patched SDK
    Success: Successfully installed NVIDIA HPC SDK version 20.7
    Building HDF5
    Downloading file - http://www.hdfgroup.org/ftp/HDF5/releases/hdf5-1.12/hdf5-1.12.0/src/hdf5-1.12.0.tar.bz2
    Extracting archive - hdf5-1.12.0.tar.bz2
    .successfully extracted archive - hdf5-1.12.0.tar.bz2
    Success: Successfully installed HDF5 version 1.12.0 in /home/ec2-user/tqi-build/20.7/hdf5
    Building NetCDF C Libraries
    Downloading file - https://github.com/Unidata/netcdf-c/archive/v4.7.4.tar.gz
    Extracting archive - v4.7.4.tar.gz
    .successfully extracted archive - v4.7.4.tar.gz
    Success: Successfully installed NETCDF-C version 4.7.4 in /home/ec2-user/tqi-build/20.7/netcdf
    Building NetCDF Fortran Libraries
    Downloading file - https://github.com/Unidata/netcdf-fortran/archive/v4.5.3.tar.gz
    Extracting archive - v4.5.3.tar.gz
    successfully extracted archive - v4.5.3.tar.gz
    Success: Successfully installed NETCDF-Fortran version 4.5.3 in /home/ec2-user/tqi-build/20.7/netcdf
    Building Parallel NetCDF Libraries
    Downloading file - https://parallel-netcdf.github.io/Release/pnetcdf-1.12.1.tar.gz
    Extracting archive - pnetcdf-1.12.1.tar.gz
    successfully extracted archive - pnetcdf-1.12.1.tar.gz
    Success: Successfully installed parallel-netcdf version 1.12.1 in /home/ec2-user/tqi-build/20.7/pnetcdf
    Success: Successfully Installed AceCAST Dependency Packages
    Environment setup script generated at /home/ec2-user/tqi-build/20.7/env.sh


We suggest running without the optional flags `--install-secondary-packages-rpm` or `--install-secondary-packages-deb`
initially. If the script reports any issues then continue to the `Installing with Secondary Dependencies`_ section.
Otherwise you can continue to the :ref:`Running AceCAST` page.


Installing with Secondary Dependencies
======================================

To run successfully, the installation script when run as-is (i.e. without the `--install-secondary-packages-rpm` or 
`--install-secondary-packages-deb` flags) requires a number of common packages that provide various utilities and 
libraries. Since they are so common it isn't unusual for these packages to already be available on many users' systems. 
When this is not the case these packages need to be installed with package repository managers such as `yum` or `apt-get`
depending on the OS is running. Notably, this also requires administrator privileges.

If you would like to indicate that these packages should be installed, first ensure you have sudo privilages to install
packages with `yum` or `apt-get` then run the `install_deps.sh` script with the appropriate flag:

::

    Usage for RPM-based Linux Distributions:      ./install_deps.sh --install-secondary-packages-rpm
    Usage for Debian-based Linux Distributions:   ./install_deps.sh --install-secondary-packages-deb


Troubleshooting
===============

If you are still having issues, please check out the :ref:`Troubleshooting` section.
    
