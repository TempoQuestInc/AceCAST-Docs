.. meta::
   :description: Advanced Topics, click for more
   :keywords: Running, Usage, MPI, input, environment, AceCast, Documentation, TempoQuest, 

.. _NVHPC OpenACC Environment Variables:
   https://docs.nvidia.com/hpc-sdk/archive/21.9/compilers/openacc-gs/index.html#env-vars

.. _Advanced Topics:

Advanced Topics
###############

.. _GPU Mapping:

GPU Mapping with MPI and the GPU Launch Script
==============================================

As discussed in the :ref:`Running AceCAST` section, `acecast.exe` is launched with MPI using the 
`gpu-launch.sh` wrapper script. The purpose of the `gpu-launch.sh` script is to assign unique GPUs 
to each MPI process, which requires setting the environment variable `ACC_DEVICE_NUM` (see 
`NVHPC OpenACC Environment Variables`_) to the intended GPU device ID for each process 
independently. This is done by determining the local MPI rank of the current process and assigning 
a unique GPU to it based on what GPUs are available on that node. All of this is done automatically 
by launching the `acecast.exe` executable with `mpirun` and the `gpu-launch.sh` MPI wrapper script.

.. tabs::

    .. tab:: Usage

        .. code-block:: output

            Usage: mpirun [MPIRUN_OPTIONS] gpu-launch.sh [--gpu-list GPU_LIST] acecast.exe

                MPIRUN_OPTIONS: use "mpirun --help" for more information

                --gpu-list GPU_LIST (optional):
                
                        This option can be used to specify which GPUs to use for running acecast. If 
                        running on multiple nodes then the list applies to all nodes. GPU_LIST should 
                        be a comma-separated list of non-negative integers or ranges (inclusive) 
                        corresponding to GPU device IDs. Examples:
                            
                            --gpu-list 0,1,3 
                            --gpu-list 0-2,4,6 
                        
                        If this option is not provided then it is assumed that all detected GPUs are 
                        available for use and GPU_LIST will be determined automatically using the 
                        nvidia-smi utility.
                        
                        Note: GPU_LIST can also be set using the ACECAST_GPU_LIST environment variable

    .. tab:: Example 1

        .. code-block:: shell

            mpirun -np 3 ./gpu-launch.sh --gpu-list 0,1,3 ./acecast.exe


