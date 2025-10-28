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

.. _JoinWRF:

JoinWRF: Combining Decomposed WRF Output
========================================

When WRF is run with distributed I/O (`io_form_history = 102`), each processor writes
a separate *tile* file for every output time (e.g., ``wrfout_d01_0001``, ``wrfout_d01_0002``, etc.).
These tiled outputs must be merged into a single, continuous NetCDF file per domain
to enable post-processing and visualization. The **JoinWRF** utility performs this step.

Overview
--------

AceCAST provides a pre-built ``joinwrf`` executable located in the ``acecast/run`` directory.
This program operates in serial mode only and does **not** require MPI. It merges the
distributed history output files from a single domain into one unified file.

A helper script, ``generate_namelist_join.py``, is provided to automatically generate
the appropriate joiner namelists for each domain using the configuration from the
existing ``namelist.input`` file.

Preparing the Joiner Namelists
------------------------------

To create the joiner namelists, run the helper script from your WRF run directory:

.. code-block:: console

   python ./generate_namelist_join.py
   Successfully generated namelist.join.wrfout_d01

This command reads your ``namelist.input`` file and produces a corresponding
``namelist.join.wrfout_dXX`` file for each active domain. These namelists contain
the decomposition parameters required by ``joinwrf`` to correctly reconstruct the output.

Running the JoinWRF Program
---------------------------

Once the joiner namelists have been generated, execute the following loop
to merge all domain outputs:

.. code-block:: console

   for NL in namelist.join.wrfout_d*; do
       ./joinwrf < $NL
   done

Each invocation of ``joinwrf`` will read the tiled NetCDF files (e.g.,
``wrfout_d01_0001``, ``wrfout_d01_0002``, etc.) and write a joined file such as
``wrfout_d01_joined_YYYY-MM-DD_HH:MM:SS``.

Example Workflow
----------------

1. Run WRF with distributed output enabled:

   .. code-block:: fortran

      &io
        io_form_history = 102
      /

   This produces separate tile files per processor for each history interval.

2. Generate the joiner namelists:

   .. code-block:: console

      python ./generate_namelist_join.py
      Successfully generated namelist.join.wrfout_d01

3. Merge all domain outputs into single files:

   .. code-block:: console

      for NL in namelist.join.wrfout_d*; do
          ./joinwrf < $NL
      done

4. Verify the merged result:

   .. code-block:: console

      ncdump -h wrfout_d01_joined_YYYY-MM-DD_HH:MM:SS | head

   The file should now contain the full domain dimensions rather than per-tile subdomains.

Limitations
-----------

- The ``joinwrf`` program operates **only** on history output files.
- The tiling layout and processor decomposition used during model execution
  must match the settings embedded in the generated joiner namelists.
- The tool assumes consistent precision between WRF output and the joiner build
  (single-precision by default in AceCAST).

References
----------

- NCAR CISL Presentation: *Running WRF on Yellowstone – Post-processing and Joiner Utility*,  
  NCAR Mesoscale and Microscale Meteorology Laboratory, 2015.  
  Available at: https://www2.mmm.ucar.edu/wrf/src/cisl_presentation.pdf


.. _IO Quilting:

.. Asynchronous I/O Using I/O Quilting
.. ===================================
..
.. Depending on the simulation configuration, I/O can make up a large portion of AceCAST's overall
.. runtime. There isn't much that can be done to improve reading input files, but on any given output
.. interval, there is no need for AceCAST to wait for history files to be written before continuing.
.. By using a feature called *I/O quilting*, AceCAST can utilize otherwise idle CPU cores to perform
.. history writes while the GPUs continue running the simulation.
..
.. Although I/O quilting can be configured any way the user likes, we suggest using one "I/O process"
.. per GPU. To do this you will need to set the following namelist options:
..
.. .. code-block:: fortran
..
..    &time_control
..     ...
..     io_form_history = 11
..     ...
..     /
..    ...
..    &domains
..     ...
..     nproc_x = 2
..     nproc_y = 2
..     ...
..     /
..    ...
..    &io_quilt
..     nio_tasks_per_group = 1
..     nio_groups = 4
..     /
..    ...
..






