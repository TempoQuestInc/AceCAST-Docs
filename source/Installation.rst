.. _installationlink:

Installation
============

(Under Construction)

Below is a list of required dependencies for AceCAST. Please be aware that AceCAST 
comes with a script that installs these dependencies for you, keep reading for details:

Required Dependencies Overview
------------------------------
* NVIDIA HPC SDK (2020, Version 20.7)
* HDF5 (Version 1.12.0)
* NETCDF-C (Version 4.7.4)
* NETCDF-Fortran (Version 4.5.3)
* parallel-netcdf (Version 1.12.1)

.. admonition:: Note
   
   Any of these packages *can* already be installed on the system.

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
 

Installation Instructions
-------------------------

* First, click `here <https://tempoquest.com/acecast-registration/>`_ to be redirected to the AceCAST download page.
* Now click the big button that says "*AceCAST v1.2 Binary Executable for 64-Bit Linux x86 or Power 9*".
* This will redirect to a registration page. To download AceCAST, free registration is required. Fill out the page and click submit at the bottom of the page.
* Be sure to review and accept the License Agreement (at the bottom of the page, click the small box to the left of "*I have read and agreed to the License Agreement*").
* Once submitted, you will be sent a link through the email that provided in the registration form from which AceCAST can be downloaded either for Linux-x86-64 or Linux Power9.
* The email will be from "*samm.elliott@tempoquest.com*"
* Next, copy the download link in the email or click the download that applies to your system to begin the download (either Linux x86-64 or Linux Power9). In a terminal, on linux x86-64 issue the command::

	$ wget -c https://tqi-s3bucket-testing.s3.us-east-2.amazonaws.com/distros/AceCASTv1.2%2Blinux.x86_64.tar.gz

This will download the AceCAST tarball. Be sure to also download the license file which came as an attachment to the email. Why? See :ref:`license <Licenselink>` for more information.

* Next, uncompress the download by typing the command::

	$ tar -xf AceCASTv1.2+linux.x86_64.tar.gz

* Once uncompressed (noted by lack of ...tar.gz extension), navigate to the AceCASTv1.2 folder that was just made by typing::

	$ cd ./AceCASTv1.2

In this folder (henceforth denoted as a directory)  should be 6 items (3 directories and 3 files):
	* run directory
	* benchmarks directory
	* scripts directory
	* README.namelist_support file
	* README file
	* RELEASE_NOTES file

* Breakdown
	* **Run:** A directory which contains all WRF/AceCAST binary executables and miscellaneous runtime files (should look very similar to standard WRF run directory)
	* **Benchmarks:** A directory that contains standard, validated test cases for helping users to get started with AceCAST
	* **Scripts:** A directory that contains a script which will install the required dependencies to run AceCAST
	* **README.namelist_support:** A text file that contains a list of currently supported namelist options (can also be found :ref:`here <toolslink>`)
	* **README:** A text file with some additional information relevent to AceCAST
	* **RELEASE_NOTES:** A text file that contains release notes (can also be found :ref:`here <releaseslink>`) 

* Next, navigate to the scripts directory by typing::

	$ cd ./Scripts

* In this directory is a shell script called "*install_deps.sh*" which will install all the required dependencies mentioned earlier. This script will prompt to specify an installation directory for these dependencies (defaults to: ~/tqi-build) and generates a script acecast_env.sh that should be used to setup your runtime environment correctly for acecast.exe to link with these dependencies properly.


* Power9 users, please issue the following commands before running the dependency installation script::

	$ module purge
	$ export TPFLAGS=-tp=pwr9

* Then, install the dependencies by running the script::

	$ ./install_deps.sh

.. admonition:: Note

   This process can take up to an hour to complete and requires ~16GB of storage.

Optional
--------

* Secondary dependency installation for RPM-based and Debian-based Linux distributions using the yum and apt-get utilities. Although this *isn't necessary* for most users where these secondary dependencies are already installed, this may be useful on systems where these are not available. This functionality should be particularly useful for those using cloud-based resources.
        
	* Usage for RPM-based Linux Distributions::

	      $ ./install_deps.sh --install-secondary-packages-rpm

	* Usage for Debian-based Linux Distributions::

	      $ ./install_deps.sh --install-secondary-packages-deb

.. admonition:: Note

   Using these options requires sudo (root) access.













