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

.. _performanceprofiling:

Performance Profiling
=====================

AceCAST includes an internal timer infrastructure that can be enabled at runtime to produce
detailed timing output in the RSL logs. This is useful for understanding where time is being
spent in the model, especially for multi-GPU runs where communication and scaling behavior can
be as important as raw kernel performance.

Enable Internal Timers
----------------------

To enable the internal timers, export the following environment variable before launching
AceCAST:

.. code-block:: shell

   export ACECAST_USE_TIMERS=true

With timers enabled, AceCAST writes several complementary profiling views near the end of the
`rsl.error.0000` file:

* A fine-grained top-down call tree with per-region total time, call counts, and mean time.
* A dedicated MPI performance summary for halo and nesting communication phases.
* A compute-throughput summary showing per-domain and total updates-per-second metrics.
* A simplified top-down summary that groups time into broader categories such as
  initialization, I/O, MPI, dynamics, and physics.

Example timer output:

.. code-block:: output

   Timing for main: time 2019-11-26_12:55:12 on domain   1:    0.23964 elapsed seconds
   Timing for main: time 2019-11-26_12:56:24 on domain   1:    0.23654 elapsed seconds
   Timing for main: time 2019-11-26_12:57:36 on domain   1:    0.23726 elapsed seconds
   Timing for main: time 2019-11-26_12:58:48 on domain   1:    0.23867 elapsed seconds
   Timing for main: time 2019-11-26_13:00:00 on domain   1:    0.23738 elapsed seconds
    mediation_integrate.G         1700 DATASET=HISTORY
    mediation_integrate.G         1701  grid%id             1  grid%oid
               6
   Timing for Writing wrfout_d01_2019-11-26_13_00_00 for domain        1:    2.63004 elapsed seconds
   Timing for Writing auxhist3_d01_2019-11-26_13_00_00 for domain        1:    0.00001 elapsed seconds
   Timing for Writing auxhist15_d01_2019-11-26_13_00_00 for domain        1:    0.00001 elapsed seconds
   Timing for Writing auxhist22_d01_2019-11-26_13_00_00 for domain        1:    0.00001 elapsed seconds
   Timing for Writing auxhist23_d01_2019-11-26_13_00_00 for domain        1:    0.00000 elapsed seconds




   ==========================================================================================
                                 Fine-Grained Top-Down Profile
   ==========================================================================================

   Top-down timing profile:
       100.00% main, t_tot = 25.250872s, count = 1, t_mean = 25.250872s
         25.29% wrf_init, t_tot = 6.384966s, count = 1, t_mean = 6.384966s
         | 3.61% alloc_and_configure_domain, t_tot = 0.911055s, count = 1, t_mean = 0.911055s
         | 15.85% med_initialdata_input, t_tot = 4.003109s, count = 1, t_mean = 4.003109s
         |   10.56% process_input_input, t_tot = 2.667613s, count = 1, t_mean = 2.667613s
         |   0.00% init_imask_arrays, t_tot = 0.000480s, count = 1, t_mean = 0.000480s
         |   5.29% start_domain, t_tot = 1.335010s, count = 1, t_mean = 1.335010s
         |     0.20% start_domain_em_part1, t_tot = 0.051267s, count = 1, t_mean = 0.051267s
         |     4.78% start_domain_em_part2, t_tot = 1.207877s, count = 1, t_mean = 1.207877s
         |     | 4.78% phy_init, t_tot = 1.207052s, count = 1, t_mean = 1.207052s
         |     |   0.05% phy_init_part1, t_tot = 0.011393s, count = 1, t_mean = 0.011393s
         |     |   | 0.01% landuse_init, t_tot = 0.002815s, count = 1, t_mean = 0.002815s
         |     |   | 0.00% z2sigma, t_tot = 0.000052s, count = 1, t_mean = 0.000052s
         |     |   0.11% ra_init, t_tot = 0.027249s, count = 1, t_mean = 0.027249s
         |     |   | 0.00% ra_init_part1, t_tot = 0.000888s, count = 1, t_mean = 0.000888s
         |     |   | 0.07% oznini, t_tot = 0.017299s, count = 1, t_mean = 0.017299s
         |     |   | 0.02% lw_init, t_tot = 0.005145s, count = 1, t_mean = 0.005145s
         |     |   | 0.02% sw_init, t_tot = 0.003906s, count = 1, t_mean = 0.003906s
         |     |   0.05% bl_init, t_tot = 0.011991s, count = 1, t_mean = 0.011991s
         |     |   0.00% cu_init, t_tot = 0.000874s, count = 1, t_mean = 0.000874s
         |     |   0.00% shcu_init, t_tot = 0.000024s, count = 1, t_mean = 0.000024s
         |     |   4.58% mp_init, t_tot = 1.155377s, count = 1, t_mean = 1.155377s
         |     |   | 0.02% aerosol_init, t_tot = 0.005560s, count = 1, t_mean = 0.005560s
         |     |   | 0.00% misc_init, t_tot = 0.000039s, count = 1, t_mean = 0.000039s
         |     |   | 1.35% zero_lookup_tables, t_tot = 0.341676s, count = 1, t_mean = 0.341676s
         |     |   | 0.24% table_ccnAct, t_tot = 0.061696s, count = 1, t_mean = 0.061696s
         |     |   | 0.00% table_Efrw, t_tot = 0.000408s, count = 1, t_mean = 0.000408s
         |     |   | 0.01% table_Efsw, t_tot = 0.001300s, count = 1, t_mean = 0.001300s
         |     |   | 0.03% table_dropEvap, t_tot = 0.006463s, count = 1, t_mean = 0.006463s
         |     |   | 0.00% radar_init, t_tot = 0.000168s, count = 1, t_mean = 0.000168s
         |     |   | 1.43% qr_acr_qg, t_tot = 0.361681s, count = 1, t_mean = 0.361681s
         |     |   | 0.10% qr_acr_qs, t_tot = 0.024485s, count = 1, t_mean = 0.024485s
         |     |   | 0.50% freezeH2O, t_tot = 0.126299s, count = 1, t_mean = 0.126299s
         |     |   | 0.00% qi_aut_qs, t_tot = 0.000821s, count = 1, t_mean = 0.000821s
         |     |   | 0.69% final_device_update, t_tot = 0.175266s, count = 1, t_mean = 0.175266s
         |     |   0.00% fg_init, t_tot = 0.000001s, count = 1, t_mean = 0.000001s
         |     |   0.00% fdob_init, t_tot = 0.000001s, count = 1, t_mean = 0.000001s
         |     0.30% start_domain_em_part3, t_tot = 0.075765s, count = 1, t_mean = 0.075765s
         |       0.23% HALO, t_tot = 0.058393s, count = 10, t_mean = 0.005839s
         |         0.01% halo_pack_y, t_tot = 0.003446s, count = 10, t_mean = 0.000345s
         |         0.11% halo_exch_y, t_tot = 0.027051s, count = 10, t_mean = 0.002705s
         |         0.00% halo_unpack_y, t_tot = 0.000594s, count = 10, t_mean = 0.000059s
         |         0.00% halo_pack_x, t_tot = 0.000631s, count = 10, t_mean = 0.000063s
         |         0.07% halo_exch_x, t_tot = 0.017980s, count = 10, t_mean = 0.001798s
         |         0.00% halo_unpack_x, t_tot = 0.000566s, count = 10, t_mean = 0.000057s
       0.00% wrf_dfi, t_tot = 0.000000s, count = 1, t_mean = 0.000000s
       74.71% wrf_run, t_tot = 18.865876s, count = 1, t_mean = 18.865876s
         74.71% integrate_head_grid, t_tot = 18.865875s, count = 1, t_mean = 18.865875s
           ...

   (see 'fort.88' for the same tree with min/max/avg t_tot across all MPI ranks.)


   ==========================================================================================
                                        MPI Performance
   ==========================================================================================

   MPI Metrics (local rank 0, global averages and maxima across all ranks):

   MPI Metrics: HALO
   | ----------------------- | ------------------ | ------------------ | ------------------ |
   | Metric                  | Local              | Global Avg         | Global Max         |
   | ----------------------- | ------------------ | ------------------ | ------------------ |
   |              Bytes Sent | 2.530 GB           | 2.533 GB           | 2.537 GB           |
   |                Messages |   5620             |   5620             |   5620             |
   |            Avg Msg Size | 0.4501 MB          | 0.4507 MB          |                    |
   | ----------------------- | ------------------ | ------------------ | ------------------ |
   |              First Done | 3.416 s            | 3.347 s            | 3.497 s            |
   |               Wait Tail | 2.867 s            | 2.532 s            | 2.887 s            |
   |                MPI Time | 6.283 s            | 5.879 s            | 6.283 s            |
   | ----------------------- | ------------------ | ------------------ | ------------------ |
   |            Transport BW | 0.7406 GB/s        | 0.7568 GB/s        |                    |
   |            Effective BW | 0.4026 GB/s        | 0.4309 GB/s        |                    |
   |           Transport Lat | 607.8 us/msg       | 595.6 us/msg       | 622.3 us/msg       |
   |             MPI Latency | 1.118 ms/msg       | 1.046 ms/msg       | 1.118 ms/msg       |
   | ----------------------- | ------------------ | ------------------ | ------------------ |
   |             Imbalance % |                    | 43.07 %            |                    |
   | ----------------------- | ------------------ | ------------------ | ------------------ |
     Transport BW/Lat derived from 'First Done'.
     Imbalance %% = Wait Tail / MPI Time (global sums).

   MPI Metrics: Nesting
   | ----------------------- | ------------------ | ------------------ | ------------------ |
   | Metric                  | Local              | Global Avg         | Global Max         |
   | ----------------------- | ------------------ | ------------------ | ------------------ |
   |              Bytes Sent | 0.000 GB           | 0.000 GB           | 0.000 GB           |
   |                Messages |   0                |   0                |   0                |
   |            Avg Msg Size | 0.000 MB           | 0.000 MB           |                    |
   | ----------------------- | ------------------ | ------------------ | ------------------ |
   |               Alltoallv | 0.000 s            | 0.000 s            | 0.000 s            |
   |             Pre-Barrier | 0.000 s            | 0.000 s            | 0.000 s            |
   |                MPI Time | 0.000 s            | 0.000 s            | 0.000 s            |
   | ----------------------- | ------------------ | ------------------ | ------------------ |
   |            Transport BW | 0.000 GB/s         | 0.000 GB/s         |                    |
   |            Effective BW | 0.000 GB/s         | 0.000 GB/s         |                    |
   |           Transport Lat | 0.000 us/msg       | 0.000 us/msg       | 0.000 us/msg       |
   |             MPI Latency | 0.000 us/msg       | 0.000 us/msg       | 0.000 us/msg       |
   | ----------------------- | ------------------ | ------------------ | ------------------ |
   |             Imbalance % |                    | 0.000 %            |                    |
   | ----------------------- | ------------------ | ------------------ | ------------------ |
     Transport BW/Lat derived from 'Alltoallv'.
     Imbalance %% = Pre-Barrier / MPI Time (global sums).


   ==========================================================================================
                                      Compute Performance
   ==========================================================================================

   Per-Domain Compute Throughput (local rank 0):

   | ----------------------- | ------------------ | ------------------ |
   |                  Metric |   d01              |  Total (All Ranks) |
   | ----------------------- | ------------------ | ------------------ |
   |               # columns | 31800              | 127.5 k            |
   |                # levels | 50                 |                    |
   |           # grid points | 1.590 M            | 6.375 M            |
   |             # timesteps | 50                 |                    |
   |        Total GP updates | 79.50 M            | 318.8 M            |
   | ----------------------- | ------------------ | ------------------ |
   |               Wall time | 13.36 s            |                    |
   |                MPI time | 6.238 s            |                    |
   |          Radiation time | 0.5452 s           |                    |
   | ----------------------- | ------------------ | ------------------ |
   |           Col Updates/s | 223.1 kupd/s       | 847.3 kupd/s       |
   |  Col Updates/s (w/ MPI) | 119.0 kupd/s       | 476.8 kupd/s       |
   |  Col Updates/s (no rad) | 241.6 kupd/s       | 957.3 kupd/s       |
   |            GP Updates/s | 11.15 Mupd/s       | 42.37 Mupd/s       |
   |   GP Updates/s (w/ MPI) | 5.948 Mupd/s       | 23.84 Mupd/s       |
   |   GP Updates/s (no rad) | 12.08 Mupd/s       | 47.86 Mupd/s       |
   | ----------------------- | ------------------ | ------------------ |
     Numerator: #columns (or #grid points) x #solve_interface calls per domain.
     Excluded from all rows: initialization, history/restart I/O, lateral-BC
       reads, FDDA, nest interp/feedback (these run outside solve_interface).
     Base row denominator: solve_interface wall time MINUS HALO+Nesting MPI
       time (i.e. compute-only: dynamics + physics + pack/unpack kernels).
     'w/ MPI' denominator: full solve_interface wall time (compute + MPI).
     'no rad' denominator: base minus rad_driver_tim time.
     'Total (All Ranks)' column: per-rank values summed across all MPI ranks.


   ==========================================================================================
                                  Simplified Top-Down Profile
   ==========================================================================================

   Top-Down Profile Summary:
   | -------------------------------- | ------------ | --------- |
   |              Name                |   Time (s)   |  Time (%) |
   | -------------------------------- | ------------ | --------- |
   | WRF Total                        |    25.250872 |    100.00 |
   |     Initialization               |     6.384973 |     25.29 |
   |         Allocate                 |     0.911055 |      3.61 |
   |         Init I/O (Read)          |     2.667613 |     10.56 |
   |         Init I/O (Write)         |     0.000000 |      0.00 |
   |         HALO/Nesting (MPI)       |     0.045031 |      0.18 |
   |         HALO/Nesting (non-MPI)   |     0.013362 |      0.05 |
   |         Other                    |     2.747912 |     10.88 |
   |     Integration                  |    18.865869 |     74.71 |
   |         I/O (Read)               |     0.211539 |      0.84 |
   |         I/O (Write)              |     5.277924 |     20.90 |
   |         HALO/Nesting (MPI)       |     6.238715 |     24.71 |
   |         HALO/Nesting (non-MPI)   |     0.352421 |      1.40 |
   |         Compute/Other            |     6.785270 |     26.87 |
   |             Physics              |     2.746135 |     10.88 |
   |                 LW Radiation     |     0.518591 |      2.05 |
   |                 SW Radiation     |     0.005723 |      0.02 |
   |                 Surface Layer    |     0.037927 |      0.15 |
   |                 Land Surface     |     0.057163 |      0.23 |
   |                 PBL              |     0.275263 |      1.09 |
   |                 Cumulus          |     0.467612 |      1.85 |
   |                 Microphysics     |     0.958975 |      3.80 |
   |                 Physics Overhead |     0.424880 |      1.68 |
   |             Dynamics             |     5.212110 |     20.64 |
   |                 RK Setup         |     0.179965 |      0.71 |
   |                 Dry Dynamics     |     0.660739 |      2.62 |
   |                 Acoustic / Small |     1.865666 |      7.39 |
   |                 Scalar Transport |     2.163329 |      8.57 |
   |                 Dynamics BC/EOS  |     0.098769 |      0.39 |
   |                 Dyn/Phys Cplng   |     0.243640 |      0.96 |
   |             Residual             |     0.000000 |      0.00 |
   | -------------------------------- | ------------ | --------- |

   Projected overhead from timer usage:       0.01s (0.046117% of main),    277ns per call (average)
     MPI_WTICK() =    1.0000000000000001E-009

   d01 2019-11-26_13:00:00 wrf: SUCCESS COMPLETE WRF

Enable NVTX Ranges for Nsight Systems
-------------------------------------

AceCAST v4.6.0 can optionally emit NVTX ranges from the same timer infrastructure so that the
main model phases appear directly on NVIDIA profiler timelines.

To enable NVTX range emission, set both of the following environment variables before launching
AceCAST under Nsight Systems:

.. code-block:: shell

   export ACECAST_USE_TIMERS=true
   export ACECAST_USE_NVTX_RANGES=true

Example usage with Nsight Systems:

.. code-block:: shell

   export ACECAST_USE_TIMERS=true
   export ACECAST_USE_NVTX_RANGES=true

   mpirun -n 1 ./gpu-launch.sh nsys profile --trace=cuda,nvtx --sample=none --cpuctxsw=none -o run-profile --force-overwrite true ./acecast.exe

This mode is intended for profiling runs rather than production throughput measurements. When
used together, the RSL timer output and the Nsight Systems timeline provide both high-level
performance summaries and time-correlated GPU activity for deeper investigation.

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
``namelist.join.wrfout_dXX`` file for each WRF domain. These namelists contain
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
``wrfout_d01_YYYY-MM-DD_HH:MM:SS``.

Example Workflow
----------------

1. Run WRF with distributed output enabled:

   .. code-block:: fortran

      &time_control
        ...
        io_form_history = 102
        ...
      /

      &domains
        ...
        nproc_x = 2
        nproc_y = 2
        ...
      /

   .. note::
      The ``generate_namelist_join.py`` script requires that ``nproc_x`` and ``nproc_y`` are specified in the ``&domains`` section of the WRF namelist. For AceCAST runs, these parameters define the 2D grid of GPUs that the domain is decomposed onto. This information is needed by the script to create the correct joiner namelists.

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

- The ``joinwrf`` program can operate on other output streams, but the ``generate_namelist_join.py`` script only considers the standard history output stream when creating the joinwrf namelists.
- The tiling layout and processor decomposition used during model execution
  must match the settings embedded in the generated joiner namelists.

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

