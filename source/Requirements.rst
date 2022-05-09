.. meta::
   :description: System requirements for AceCast, click for more
   :keywords: Requirements, system, information, CUDA, GPU, AceCast, Documentation, TempoQuest

.. _requirementslink:

System Requirements
===================

* OS & Host Architecture:

	* This distribution of AceCAST targets generic x86-64 Linux systems. Note: Support is not guaranteed for any 
	  particular Linux distribution but this release has been tested successfully on a variety of distributions when using the 
	  recommended installation methods.

* CUDA-Capable GPU and CUDA Toolkit:

	* This AceCAST distribution is compatible with GPU compute capabilities 3.5, 6.0, 7.0 and 8.0 and requires a CUDA runtime 
	  driver installation compatible with the CUDA 11.0 toolkit (i.e. driver versions >= 450.36.06 -- tip: you can find your version by  
	  running nvidia-smi).


.. admonition:: Note

    TQI extensively tested the model on Intel (Haswell and Skylake) CPUs with NVIDIA V100 GPUs on a CentOS Linux version 7 platform. 
