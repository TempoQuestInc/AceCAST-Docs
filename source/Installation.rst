.. _installationlink:

Installation
============

(Under Construction)

Provided below is a list of required library dependencies for AceCAST. Please check to see if these libraries have already 
been installed on your system. If they have not been installed, TempoQuest provides a library dependency installation script 
(../scripts/install_deps.sh) in the AceCAST download file that installs these libraries for you. Please use the dependency 
installation script to ensure compatibility with the AceCAST binary executable. Further instructions are provided below if
needed:

Required Dependencies Overview
------------------------------
* NVIDIA HPC SDK (formerly PGI) compiler suite which includes NVIDIA-compiled, CUDA-aware OpenMPI build (2020, Version 20.7)
* HDF5 (Version 1.12.0)
* NETCDF-C (Version 4.7.4)
* NETCDF-Fortran (Version 4.5.3)
* parallel-netcdf (Version 1.12.1)

.. admonition:: Note
   
	Even if you have these libraries and versions already installed on your system, you will still have to run the dependency 
	installation script because this script not only installs the required library dependencies, but it also generates a script that 
	ensures a proper runtime environment for running AceCAST (env.sh).




Optional secondary packages
---------------------------

* yum
* apt-get
* git
* tar
* wget
* gcc
* make
* which 
* etc...

.. admonition:: Note

   Most linux-based systems already have these optional packages pre-installed.


Library dependency check
------------------------

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


Installation Instructions
-------------------------

* First, click `here <https://tempoquest.com/acecast-registration/>`_ to be redirected to the AceCAST download page.
* Now click the big button that says "*AceCAST v1.2 Binary Executable for 64-Bit Linux x86 or Power 9*".
* This will redirect to a registration page. To download AceCAST, free registration is required. 
* The purpose of this registration form is for TempoQuest to track the types of users that are downloading AceCAST, and 
   so TempoQuest can e-mail you the AceCAST download and AceCAST license file. 
* Please fill in the required fields and review the license agreement. 
* Once you have read the license agreement, make sure to click the box located at the lower left corner of the page to acknowledge that you have read and understand the license agreement. 
* After clicking the box, an orange “Submit” button will appear. 
* Click on the “Submit” button. Fill out the page and click submit at the bottom of the page.
* Once submitted, you will receive an e-mail from "*samm.elliott@tempoquest.com*" that contains an attachment/download link which has the 
  the AceCAST download file for Linux-x86-64 or Linux Power9. 
* This e-mail also contains another attachment, an AceCAST license file (acecast-trial.lic). 
* The AceCAST license file is required for AceCAST to run properly because this file is checked by the acecast.exe executable during runtime to ensure the user has a valid license.
* Next, copy the download link in the email or click the download that applies to your system to begin the download (either Linux x86-64 or Linux Power9). In a terminal, on linux x86-64 issue the command::

	$ wget -c https://tqi-s3bucket-testing.s3.us-east-2.amazonaws.com/distros/AceCASTv1.2%2Blinux.x86_64.tar.gz

This will download the AceCAST tarball. Be sure to also download the license file which came as an attachment to the email. Why? See :ref:`license <Licenselink>` for more information.

* Next, uncompress the download by typing the command::

	$ tar -xvzf AceCASTv1.2+linux.x86_64.tar.gz

* Once uncompressed (noted by lack of ...tar.gz extension), navigate to the AceCASTv1.2 folder that was just made by typing::

	$ cd ./AceCASTv1.2

In this folder (henceforth denoted as a directory) should be 6 items (3 directories and 3 files)::
#. benchmarks
	* Location: ../AceCASTv1.2/benchmarks
	* A directory that contains standard, validated test cases for helping users get started with AceCAST.
#. README
	* Location: ../AceCASTv1.2/
	* A text file that contains brief tutorial instructions about how to install and run AceCAST, and some helpful 
	  recommendations for best practices.
	* To view this file, enter the following in the command line::
	
	  vi README

#. README.namelist_support
	* Location: ../AceCASTv1.2/
	* A text file that contains a list of currently supported namelist options for this version release (can also be found :ref:`here <toolslink>`).
	* To view this file, enter the following in the command line::

	  vi README.namelist_support

#. RELEASE_NOTES
	* Location: ../AceCASTv1.2/
	* A text file that contains information about what is included in the AceCAST release such as newly added namelist 
          options and WRF features, and any bug fixes (can also be found :ref:`here <releaseslink>`).
	* To view this file, enter the following in the command line::

	   vi RELEASE_NOTES

#. run
	* Location: ../AceCASTv1.2/run
	* A directory that contains all WRF/AceCAST binary executables and miscellaneous runtime files (it should look very 
	  similar to a standard WRF run directory).
#. scripts
	* Location: ../AceCASTv1.2/scripts
	* A directory that contains a script to install and build the required library dependencies to run AceCAST. 
	  This script also installs an environment script that ensures a proper runtime environment is created when running AceCAST.


* Next, navigate to the scripts directory by typing::

	$ cd ./Scripts

* In this directory is a shell script called "*install_deps.sh*" which will install all the required dependencies mentioned earlier. 
  This script will prompt to specify an installation directory for these dependencies (defaults to: ~/tqi-build) and also generates a 
  script, acecast_env.sh, that should be used to setup your runtime environment correctly for acecast.exe to link with these 
  dependencies properly.

* Power9 users, please issue the following commands before running the dependency installation script::

	$ module purge
	$ export TPFLAGS=-tp=pwr9

* Then, please make sure you are in the ../AceCASTv1.2/scripts directory and install the dependencies by running the command::

	$ ./install_deps.sh

.. admonition:: Note

   This process can take up to an hour to complete and requires ~16GB of storage.

*If the installation was successful, you should see a message in the terminal similar to: 

	* "Successfully Installed AceCAST Dependency Packages."

* Please ensure that the installation script created the environment scripts (env.sh) as well:: 
	
	$ cd ../tqi-build/20.7

* Once in this directory, type::

	  $ ls

* You should see an env.sh script in this directory. If you see this script and the message in the terminal, 
  then AceCAST was installed successfully. 


Optional
--------

* Secondary dependency installation for Red Hat Package Manager (RPM)-based and Debian-based Linux distributions using the yum and 
  apt-get utilities. Although this *isn't necessary* for most users where these secondary dependencies are already installed, 
  this may be useful on systems where these are not available. This functionality should be particularly useful for those using 
  cloud-based resources.
        
	* Usage for RPM-based Linux Distributions::

	      $ ./install_deps.sh --install-secondary-packages-rpm

	* Usage for Debian-based Linux Distributions::

	      $ ./install_deps.sh --install-secondary-packages-deb

.. admonition:: Note

   Using these options requires sudo (root) access.

* If you have any questions or issues during installation, please reach out to us at support@tempoquest.com 




*Before invoking the following commands below, please make sure you are in the ../AceCASTv1.2/scripts directory.










