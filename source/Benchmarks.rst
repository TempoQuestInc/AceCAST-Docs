.. meta::
   :description: Benchmarks topics for AceCast, click for more
   :keywords: benchmarks, easter100, easter500, easter1500, AceCast, Documentation, TempoQuest

.. _Benchmarks:

Benchmarks
##########

* It is recommended to use the test cases provided in the benchmarks directory for 
  getting started with AceCAST.
	
	* easter100:
		* 2km horizontal resolution, 100x100x51 grid, 2 days simulation initialized with HRRR
		* Very small domain intended for quick validation testing.
		* Note that this case is only for testing and should not be used for performance benchmarking due to its small domain size.
                * Input Files: `easter100.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/datasets/v2/easter100.tar.gz>`_
	
	* easter500:
		* 2km horizontal resolution, 500x500x51 grid, 2 days simulation initialized with HRRR
		* Larger domain intended for benchmarking on small numbers of GPUs (ex. 1-4 V100 GPUs)
		* Input Files: `easter500.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/datasets/v2/easter500.tar.gz>`_

	* easter1500 (not included by default):
		* 1km horizontal resolution, 1500x1500x51 grid, 1 hour simulation initialized with HRRR
		* Large, high resolution domain intended for performance benchmarking on larger numbers of GPUs (ex. 8-32 V100 GPUs)
		* You must ensure minimum 11GB space to simulate this case
		* This case is not included in the distribution package
		* download `here <https://tqi-s3bucket-testing.s3.us-east-2.amazonaws.com/distros/easter1500.tar.gz>`_ 
		* Input Files: `easter1500.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/datasets/v2/easter1500.tar.gz>`_




