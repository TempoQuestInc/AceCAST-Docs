.. meta::
   :description: Tools for AceCast, click for more
   :keywords: Tools, Support, Namelist, Benchmarks, Scaling, Advisor, AceCast, Documentation, TempoQuest

.. _toolslink:

Tools
=====

* Namelist Support table

.. include:: _templates/Namelist_Supported_Options.txt

**Note:** Most options can be turned off with a value of 0, where applicable. 
**Note:** AceCAST supports the same output as WRF which includes but is not limited to netCDF and GRIB.

.. raw:: html
   
   <iframe src="https://docs.google.com/forms/d/e/1FAIpQLSfVZ_7KQSJ7pUrHPk3aKYvTqDklBm9g1bmnCRkou-zfmreLSA/viewform?embedded=true" 
    width="900" height="470" frameborder="0" marginheight="0" marginwidth="0">Loading…</iframe>

Benchmarks
----------

Once AceCAST has been downloaded, there will be a folder called benchmarks in ../AceCASTv1.2/run directory.

* It is recommended to use the test cases provided in the benchmarks directory for 
  getting started with AceCAST.
	
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
		* You must ensure minimum 11GB space to simulate this case
		* This case is not included in the distribution package
		* download `here <https://tqi-s3bucket-testing.s3.us-east-2.amazonaws.com/distros/easter1500.tar.gz>`_ 
