.. meta::
   :description: Troubleshooting for AceCast, click for more
   :keywords: Help, error, library, check, AceCast, Documentation, TempoQuest

.. _Troubleshooting:

Troubleshooting
###############

#. General 

	* Please ensure that the installation script created the environment script (env.sh):: 
	
		$ cd ../tqi-build/20.7

	* Once in this directory, type::

	  	$ ls

	* You should see an env.sh script in this directory. If you see this script and the message in the terminal, 
  	  then AceCAST was installed successfully. 


#. The install_deps.sh script keeps failing 

Provided below is a list of required library dependencies for AceCAST. Please check to see if these libraries have already 
been installed on your system. Please use the dependency installation script to ensure compatibility with the AceCAST 
binary executable:

* NVIDIA HPC SDK (formerly PGI) compiler suite which includes NVIDIA-compiled, CUDA-aware OpenMPI build (2020, Version 20.7)
* HDF5 (Version 1.12.0)
* NETCDF-C (Version 4.7.4)
* NETCDF-Fortran (Version 4.5.3)
* parallel-netcdf (Version 1.12.1)

Even if you have these libraries and versions already installed on your system, you will still have to run the dependency 
installation script because this script not only installs the required library dependencies, but it also generates a script that 
ensures a proper runtime environment for running AceCAST (env.sh).
   
Typically, most linux-based systems already have these optional packages pre-installed but you will also find it useful to have:

* yum
* apt-get
* git
* tar
* wget
* gcc
* make
* which 
* etc...

Library dependency check

Run the following commands listed below to see if your system already has the required library versions and ensure that the 
required library versions were installed properly on your system.

#. Check to see if HDF5 (Version 1.12.0) is installed. Enter the following in the command line and press enter::

	$ h5dump -version

   *  This command tells you the HDF5 version that is installed on your system and should return::

	$ h5dump: Version 1.12.0


#. Check to see if netCDF-C (Version 4.7.4) is installed. Enter the following in the command line and press enter::

	$ nc-config -version

   * This command tells you the netCDF-C version that is installed on your system and should return::

	$ netCDF 4.7.4

#. Check to see if netCDF-Fortran (Version 4.5.3) is installed. Enter the following in the command line and press enter::
	
	$ nf-config -version

   * This command tells you the netCDF-Fortran version that is installed on your system and should return::

	$ netCDF-Fortran 4.5.3

#. Check to see if NVIDIA (Version 20.7) is installed. Enter the following in the command line and press enter::

	$ pgfortran -V

   * This command tells you the NVIDIA compiler version that is installed on your system and should return::

	$ pgfortran (aka nvfortran) 20.7-0 LLVM 64-bit target on x86-64 
	  Linux -tp haswell 
          PGI Compilers and Tools
          Copyright (c) 2020, NVIDIA CORPORATION.  All rights reserved.

#. Check to see if PnetCDF (Version 1.12.1) is installed. Enter the following in the command line and press enter::

	$ pnetcdf_version

   * This command tells you the PnetCDF version that is installed on your system and should return::

	$ PnetCDF Version:        1.12.1


* If you are using Power9, please issue the following commands before running the dependency installation script::

	$ module purge
	$ export TPFLAGS=-tp=pwr9


Don't see your issue addressed? Let's have a :ref: `discussion <supportlink>` about it!















