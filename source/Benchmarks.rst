.. meta::
   :description: Benchmarks topics for AceCast, click for more
   :keywords: benchmarks, easter100, easter500, easter1500, AceCast, Documentation, TempoQuest

.. _Benchmarks:

Benchmarks
##########

It is recommended to use the test cases provided on this page for getting started with AceCAST.

NCAR Standard Benchmark Test Cases
==================================

The *CONUS 12km* and *CONUS 2.5km* benchmarks provided by NCAR are widely used for evaluating the 
performance of WRF on various systems. For more information check out the `WRF V4.4 Benchmarks Page <https://www2.mmm.ucar.edu/wrf/users/benchmark/v44/benchdata_v44.html>`_.

CONUS 12km
**********

This is a small benchmark inteded for running on a single GPU. It is a 12-hour simulation over the
continental United States (CONUS) on a 425x300 grid at a 12km resolution. 

* Download (500 MB): `conus12km.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/datasets/v2/conus12km.tar.gz>`_

CONUS 2.5km
***********

This is a large benchmark inteded for running on larger numbers of GPUs. It is a 6-hour simulation 
over the continental United States (CONUS) on a 1501x1201 grid at a 2.5km resolution. 

* Download (2.3 GB): `conus2.5km.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/datasets/v2/conus2.5km.tar.gz>`_


TQI-Provided Benchmark Test Cases
=================================

Easter 100
**********

* 2km horizontal resolution, 100x100x51 grid, 2 days simulation initialized with HRRR
* Very small domain intended for quick validation testing.
* Note that this case is only for testing and should not be used for performance benchmarking due to its small domain size.
* Download (20 MB): `easter100.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/datasets/easter100.tar.gz>`_
	
Easter 500
**********

* 2km horizontal resolution, 500x500x51 grid, 2 days simulation initialized with HRRR
* Larger domain intended for benchmarking on small numbers of GPUs (ex. 1-4 V100 GPUs)
* Download (10 GB): `easter500.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/datasets/easter500.tar.gz>`_

Easter 1500
***********

* 1km horizontal resolution, 1500x1500x51 grid, 1 hour simulation initialized with HRRR
* Large, high resolution domain intended for performance benchmarking on larger numbers of GPUs (ex. 8-32 V100 GPUs)
* Download (2.5 GB): `easter1500.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/datasets/easter1500.tar.gz>`_




