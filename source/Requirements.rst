.. meta::
   :description: System requirements for AceCast, click for more
   :keywords: Requirements, system, information, CUDA, GPU, AceCast, Documentation, TempoQuest

.. _requirementslink:

System Requirements
===================

* OS & Host Architecture:

	* This distribution of AceCAST targets generic x86-64 and Power9 Linux systems. Note: Support is not guaranteed for any 
	  particular Linux distribution but this release has been tested successfully on a variety of distributions when using the 
	  recommended installation methods.

.. admonition:: Note

	This AceCAST distribution targets generic x86-64 and Power9 Linux systems. Support is not guaranteed for any particular Linux 
	distribution, but this release has been tested successfully on a variety of distributions when using the recommended installation 
	methods.


* CUDA-Capable GPU and CUDA Toolkit:

	* This AceCAST distribution is compatible with GPU compute capabilities 3.5, 6.0, 7.0 and 8.0 and requires a CUDA runtime 
	  driver installation compatible with the CUDA 11.0 toolkit (i.e. driver versions >= 450.36.06 -- tip: you can find your version by  
	  running nvidia-smi). Power9 version is compiled with CUDA 10.1 for compatibility with older system drivers. This should already 
	  be installed on most systems with GPUs by default. If it is not, please refer to the `documentation <https://docs.nvidia.com/cuda/index.html>`_ for 
	  installing the necessary cuda components.


.. admonition:: Note

    TQI extensively tested the model on Intel (Haswell and Skylake) CPUs with NVIDIA V100 GPUs on a CentOS Linux version 7 platform. 
    The IBM Power9 version is extensively tested on the U.S. Department of Energy Summit supercomputer.


