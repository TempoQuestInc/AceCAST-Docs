.. _toolslink:

Tools
=====

Under Construction
------------------

* Namelist Support table

.. include:: _templates/Namelist_Supported_Options.txt

**Note:** Most options can be turned off with a value of 0, where applicable. 


Benchmarks
----------

Once AceCAST has been downloaded, there will be a folder called benchmarks.

* It is recommended to use the test cases provided in the benchmarks directory for getting started with AceCAST.
	* easter100:
		* 2km horizontal resolution, 100x100x51 grid, 2 days simulation initialized with HRRR
		* Very small domain intended for quick validation testing.
		* Note that this case is only for testing and should not be used for performance benchmarking due to its small domain size.
	
	* easter500:
		* 2km horizontal resolution, 500x500x51 grid, 2 days simulation initialized with HRRR
		* Larger domain intended for benchmarking on small numbers of GPUs (ex. 1-4 V100 GPUs)

	* easter1500 (not included by default):
		* 1km horizontal resolution, 1500x1500x51 grid, 1 hour simulation initialized with HRRR
		* Large, high resolution domain intended for performance benchmarking on larger numbers of GPUs (ex. 8-32 V100 GPUs)
		* You must ensure minimum 11G space to simulate this case
		* not included in distribution package
		* download `here <https://tqi-s3bucket-testing.s3.us-east-2.amazonaws.com/distros/easter1500.tar.gz>`_ 


AceCAST Advisor
---------------
* The AceCAST Advisor script provides various tools to assist AceCAST users with configuring and running their AceCAST simulations. The primary functionalities are selected with the --tool flag and are summarized below. This should be the primary tool for users to transition their namelists to take advantage of AceCAST.

* usage: acecast-advisor.sh [OPTIONS]
	* OPTIONS:
		* -t, --tool <support-check|scaling-advisor> [Default: none]
		* -n, --namelist-file <FILE> [Default: ./namelist.input]
		* -v, --verbose
		* -h, --help

* Tools

	#. Namelist Support Check

		* The namelist support checking tool is used to ensure that all options in a given namelist are supported by AceCAST. The script will tell the user which options are not supported and what supported alternatives the user should consider to adapt their namelist for running with AceCAST.

	#. Scaling Advisor
	
		* The scaling advisor tool is used to give AceCAST users a general sense of how many GPUs they should use to run their simulations. The scaling characteristics of AceCAST are relatively complex making this a challenging task otherwise. This tool will determine a minimum and maximum number of GPUs that the user should consider initially for a given namelist and GPU hardware specification.

.. admonition:: Note

    When using the scaling advisor tool the script should be run on the intended runtime machine and runtime environment. Otherwise the script will not be able to determine critical information about the user's GPU specifications.

* For more information and examples for using the AceCAST Advisor utility run::
	
	$ ./run/acecast-advisor.sh --help


* diffwrfs

* wrfstats

* configuration
