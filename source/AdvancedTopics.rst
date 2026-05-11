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

If you want more detailed MPI communication profiling, enable the optional synchronization
timers:

.. code-block:: shell

   export ACECAST_TIMERS_MPI_SYNC=true

This inserts MPI barriers around selected HALO and nesting communication scopes so the
profiling output can separate time spent waiting for ranks to enter or exit a communication
phase from time spent inside the MPI calls themselves. It also enables HALO timing bins by
average message size.

This option is intended for profiling and roofline-style analysis. It can change runtime
performance because the added barriers reduce overlap and expose rank imbalance.

With timers enabled, AceCAST writes several complementary profiling views near the end of the
`rsl.error.0000` file:

* A fine-grained top-down call tree with per-region total time, call counts, and mean time.
* A dedicated MPI performance summary for halo and nesting communication phases.
* A compute-throughput summary showing per-domain and total updates-per-second metrics.
* A simplified top-down summary that groups time into broader categories such as
  initialization, I/O, MPI, dynamics, and physics.

Example timer output:

.. code-block:: output

   ==========================================================================================
                                 Fine-Grained Top-Down Profile
   ==========================================================================================

   Top-down timing profile:
       100.00% main, t_tot = 89.908511s, count = 1, t_mean = 89.908511s
         7.89% wrf_init, t_tot = 7.094729s, count = 1, t_mean = 7.094729s
         | 1.72% alloc_and_configure_domain, t_tot = 1.549919s, count = 1, t_mean = 1.549919s
         | 4.32% med_initialdata_input, t_tot = 3.884519s, count = 1, t_mean = 3.884519s
         |   4.03% process_input_input, t_tot = 3.625361s, count = 1, t_mean = 3.625361s
         |   0.00% init_imask_arrays, t_tot = 0.000541s, count = 1, t_mean = 0.000541s
         |   0.29% start_domain, t_tot = 0.258612s, count = 1, t_mean = 0.258612s
         |     0.06% start_domain_em_part1, t_tot = 0.051256s, count = 1, t_mean = 0.051256s
         |     0.09% start_domain_em_part2, t_tot = 0.077445s, count = 1, t_mean = 0.077445s
         |     | 0.08% phy_init, t_tot = 0.074896s, count = 1, t_mean = 0.074896s
         |     |   0.01% phy_init_part1, t_tot = 0.011831s, count = 1, t_mean = 0.011831s
         |     |   | 0.00% landuse_init, t_tot = 0.002328s, count = 1, t_mean = 0.002328s
         |     |   | 0.00% z2sigma, t_tot = 0.000052s, count = 1, t_mean = 0.000052s
         |     |   0.03% ra_init, t_tot = 0.026829s, count = 1, t_mean = 0.026829s
         |     |   | 0.00% ra_init_part1, t_tot = 0.001743s, count = 1, t_mean = 0.001743s
         |     |   | 0.02% oznini, t_tot = 0.015315s, count = 1, t_mean = 0.015315s
         |     |   | 0.01% lw_init, t_tot = 0.006120s, count = 1, t_mean = 0.006120s
         |     |   | 0.00% sw_init, t_tot = 0.003643s, count = 1, t_mean = 0.003643s
         |     |   0.03% bl_init, t_tot = 0.025141s, count = 1, t_mean = 0.025141s
         |     |   0.01% cu_init, t_tot = 0.010791s, count = 1, t_mean = 0.010791s
         |     |   | 0.01% kf_eta_init, t_tot = 0.010752s, count = 1, t_mean = 0.010752s
         |     |   0.00% shcu_init, t_tot = 0.000033s, count = 1, t_mean = 0.000033s
         |     |   0.00% mp_init, t_tot = 0.000151s, count = 1, t_mean = 0.000151s
         |     |   0.00% fg_init, t_tot = 0.000003s, count = 1, t_mean = 0.000003s
         |     |   0.00% fdob_init, t_tot = 0.000001s, count = 1, t_mean = 0.000001s
         |     0.14% start_domain_em_part3, t_tot = 0.129777s, count = 1, t_mean = 0.129777s
         |       0.12% HALO, t_tot = 0.112048s, count = 10, t_mean = 0.011205s
         |         0.00% halo_pack_y, t_tot = 0.002655s, count = 10, t_mean = 0.000266s
         |         0.12% halo_exch_y, t_tot = 0.105628s, count = 10, t_mean = 0.010563s
         |         0.00% halo_unpack_y, t_tot = 0.000666s, count = 10, t_mean = 0.000067s
       0.00% wrf_dfi, t_tot = 0.000000s, count = 1, t_mean = 0.000000s
       92.11% wrf_run, t_tot = 82.813754s, count = 1, t_mean = 82.813754s
         92.11% integrate_head_grid, t_tot = 82.813753s, count = 1, t_mean = 82.813753s
           ...

   (see 'fort.88' for the same tree with min/max/avg t_tot across all MPI ranks.)


   ==========================================================================================
                                        MPI Performance
   ==========================================================================================

   MPI Metrics (local rank 0, global totals and timing ranges across all ranks):

   |-----------------------------------------------------------------------------------------------------------|
   | HALO MPI Communication                                                                                    |
   |-----------------------------------------------------------------------------------------------------------|

   HALO Summary Metrics:
   | ----------------------------------- | ------------------------ | ---------------------------------------------------- |
   | Metric                              | Local Rank               | Global Total / Range                                 |
   | ----------------------------------- | ------------------------ | ---------------------------------------------------- |
   |                          Bytes Sent | 4.37 GB                  | 26.2 GB                                              |
   |                          Bytes Recv | 4.37 GB                  | 26.2 GB                                              |
   |                     Bytes Exchanged | 8.75 GB                  | 52.5 GB                                              |
   |                       Messages Sent | 2825                     | 16950                                                |
   |                       Messages Recv | 2825                     | 16950                                                |
   |                  Messages Exchanged | 5650                     | 33900                                                |
   |                        Avg Msg Size | 1.55 MB                  | 1.55 MB                                              |
   | ----------------------------------- | ------------------------ | ---------------------------------------------------- |
   |                          Scope Time | 17.4 s       (100%)      | 17.4 s           (r2)    .. 17.5 s           (r3)    |
   |                           Sync Wait | 0.317 s      (1.82%)     | 0.301 s          (r2)    .. 0.369 s          (r3)    |
   |                            MPI Time | 11.4 s       (65.71%)    | 11.3 s           (r3)    .. 17.0 s           (r1)    |
   |                      Post Sync Wait | 5.65 s       (32.47%)    | 0.519E-01 s      (r1)    .. 5.82 s           (r3)    |
   |                               MPI % | 65.7 %                   | 64.6 %           (r3)    .. 97.6 %           (r1)    |
   | ----------------------------------- | ------------------------ | ---------------------------------------------------- |
   |                         Exchange BW | 765 MB/s                 | 765 MB/s         (r0)    .. 1.03 GB/s        (r2)    |
   |                  Effective Phase BW | 502 MB/s                 | 501 MB/s         (r3)    .. 1.01 GB/s        (r2)    |
   |                MPI Time per Message | 2.03 ms/msg              | 1.50 ms/msg      (r2)    .. 2.03 ms/msg      (r0)    |
   | ----------------------------------- | ------------------------ | ---------------------------------------------------- |
     Global ranges show value (rank) for the ranks that produced the min/max.
     Scope Time spans halo_exch_x/halo_exch_y after communicator lookup.
     HALO Bytes Recv is assumed equal to Bytes Sent for symmetric halo exchanges.
     Sync Wait is the optional ACECAST_TIMERS_MPI_SYNC barrier time before request posting.
     Exchange BW = (Bytes Sent + Bytes Recv) / MPI Time.
     Effective Phase BW = (Bytes Sent + Bytes Recv) / (Sync Wait + MPI Time + Post Sync Wait).
     MPI % = MPI Time / (Sync Wait + MPI Time + Post Sync Wait).
     MPI Time per Message = MPI Time / (Messages Sent + Messages Recv).

   HALO Message Size Bins (sent + received):
     Receive-side HALO bin counts mirror send sizes for symmetric halo exchanges.
   | ---------------- | -------------------- | -------------------- |
   | Size Bin         | Local Rank           | Global Total         |
   | ---------------- | -------------------- | -------------------- |
   |         <= 1 KiB | 500                  | 3000                 |
   |         <= 4 KiB | 100                  | 600                  |
   |        <= 16 KiB | 104                  | 624                  |
   |        <= 64 KiB | 0                    | 0                    |
   |       <= 256 KiB | 278                  | 1668                 |
   |         <= 1 MiB | 2334                 | 14004                |
   |         <= 4 MiB | 1828                 | 10968                |
   |        <= 16 MiB | 506                  | 3036                 |
   |        <= 64 MiB | 0                    | 0                    |
   |       <= 256 MiB | 0                    | 0                    |
   |        > 256 MiB | 0                    | 0                    |
   | ---------------- | -------------------- | -------------------- |

   HALO Timing by Avg Message Size (local rank exchange-level bins):
     Each exchange is binned by (Bytes Sent + Bytes Recv) / (Messages Sent + Messages Recv).
     Times are summed for this MPI rank only.
     MPI BW is bidirectional: (Bytes Sent + Bytes Recv) / MPI Time.
   | ---------------- | ---------- | ---------- | ------------ | ------------ | ------------ | ------------ |
   | Avg Msg Size     | Exchanges  | Messages   | Bytes        | MPI Time     | MPI BW       | MPI Time/Msg |
   | ---------------- | ---------- | ---------- | ------------ | ------------ | ------------ | ------------ |
   |         <= 1 KiB |        250 |        500 | 0.00 GB      | 0.742E-03 s  | 0.00 GB/s    | 1.48 us/msg  |
   |         <= 4 KiB |         50 |        100 | 0.379 MB     | 0.488E-01 s  | 7.75 MB/s    | 488 us/msg   |
   |        <= 16 KiB |         52 |        104 | 1.12 MB      | 0.283E-02 s  | 396 MB/s     | 27.3 us/msg  |
   |        <= 64 KiB |          0 |          0 | 0.00 GB      | 0.00 s       | 0.00 GB/s    | 0.00 us/msg  |
   |       <= 256 KiB |        139 |        278 | 72.1 MB      | 0.756E-01 s  | 954 MB/s     | 272 us/msg   |
   |         <= 1 MiB |       1167 |       2334 | 1.30 GB      | 1.32 s       | 980 MB/s     | 567 us/msg   |
   |         <= 4 MiB |        914 |       1828 | 4.06 GB      | 4.89 s       | 830 MB/s     | 2.67 ms/msg  |
   |        <= 16 MiB |        253 |        506 | 3.32 GB      | 5.10 s       | 651 MB/s     | 10.1 ms/msg  |
   |        <= 64 MiB |          0 |          0 | 0.00 GB      | 0.00 s       | 0.00 GB/s    | 0.00 us/msg  |
   |       <= 256 MiB |          0 |          0 | 0.00 GB      | 0.00 s       | 0.00 GB/s    | 0.00 us/msg  |
   |        > 256 MiB |          0 |          0 | 0.00 GB      | 0.00 s       | 0.00 GB/s    | 0.00 us/msg  |
   | ---------------- | ---------- | ---------- | ------------ | ------------ | ------------ | ------------ |
   |              All |       2825 |       5650 | 8.75 GB      | 11.4 s       | 765 MB/s     | 2.03 ms/msg  |
   | ---------------- | ---------- | ---------- | ------------ | ------------ | ------------ | ------------ |

   |-----------------------------------------------------------------------------------------------------------|
   | Nesting MPI Communication                                                                                 |
   |-----------------------------------------------------------------------------------------------------------|

   Nesting Summary Metrics:
   | ----------------------------------- | ------------------------ | ---------------------------------------------------- |
   | Metric                              | Local Rank               | Global Total / Range                                 |
   | ----------------------------------- | ------------------------ | ---------------------------------------------------- |
   |                          Bytes Sent | 0.00 GB                  | 4.47 GB                                              |
   |                          Bytes Recv | 1.14 GB                  | 4.47 GB                                              |
   |                     Bytes Exchanged | 1.14 GB                  | 8.94 GB                                              |
   |                       Messages Sent | 0                        | 70                                                   |
   |                       Messages Recv | 14                       | 70                                                   |
   |                  Messages Exchanged | 14                       | 140                                                  |
   |                        Avg Msg Size | 81.7 MB                  | 63.9 MB                                              |
   | ----------------------------------- | ------------------------ | ---------------------------------------------------- |
   |                          Scope Time | 2.00 s       (100%)      | 1.74 s           (r2)    .. 2.00 s           (r3)    |
   |                     Entry Sync Wait | 0.255 s      (12.77%)    | 0.362E-04 s      (r2)    .. 0.256 s          (r3)    |
   |                 Descriptor MPI Time | 0.333E-03 s  (0.02%)     | 0.332E-03 s      (r1)    .. 0.335E-03 s      (r2)    |
   |                          Setup Time | 0.443E-03 s  (0.02%)     | 0.443E-03 s      (r0)    .. 0.521E-03 s      (r3)    |
   |                    Payload MPI Time | 1.48 s       (74.06%)    | 1.48 s           (r0)    .. 1.74 s           (r2)    |
   |                      Exit Sync Wait | 0.262 s      (13.13%)    | 0.606E-04 s      (r2)    .. 0.262 s          (r0)    |
   |                       Payload MPI % | 74.1 %                   | 74.1 %           (r0)    .. 99.9 %           (r2)    |
   | ----------------------------------- | ------------------------ | ---------------------------------------------------- |
   |                          Payload BW | 773 MB/s                 | 773 MB/s         (r0)    .. 1.95 GB/s        (r2)    |
   |                  Effective Scope BW | 572 MB/s                 | 572 MB/s         (r0)    .. 1.95 GB/s        (r2)    |
   |            Payload Time per Message | 106 ms/msg               | 30.3 ms/msg      (r1)    .. 108 ms/msg       (r3)    |
   | ----------------------------------- | ------------------------ | ---------------------------------------------------- |
     Global ranges show value (rank) for the ranks that produced the min/max.
     Scope Time spans rsl_lite_bcast_msgs/merge_msgs after communicator lookup.
     Entry Sync Wait is the optional barrier at nesting communication scope entry.
     Descriptor MPI Time is the metadata exchange before the payload MPI_Alltoallv.
     Setup Time includes size/displacement construction, message-size accounting, and allocation.
     Effective Scope BW = (Bytes Sent + Bytes Recv) / Scope Time.
     Payload BW = (Bytes Sent + Bytes Recv) / Payload MPI Time.
     Exit Sync Wait is the optional barrier after rsl_lite_bcast_msgs/merge_msgs.
     Payload MPI % = Payload MPI Time / Scope Time.
     Payload Time per Message = Payload MPI Time / (Messages Sent + Messages Recv).

   Nesting Message Size Bins (sent + received):
   | ---------------- | -------------------- | -------------------- |
   | Size Bin         | Local Rank           | Global Total         |
   | ---------------- | -------------------- | -------------------- |
   |         <= 1 KiB | 0                    | 0                    |
   |         <= 4 KiB | 0                    | 0                    |
   |        <= 16 KiB | 0                    | 0                    |
   |        <= 64 KiB | 0                    | 0                    |
   |       <= 256 KiB | 0                    | 0                    |
   |         <= 1 MiB | 0                    | 0                    |
   |         <= 4 MiB | 0                    | 28                   |
   |        <= 16 MiB | 0                    | 0                    |
   |        <= 64 MiB | 0                    | 0                    |
   |       <= 256 MiB | 14                   | 112                  |
   |        > 256 MiB | 0                    | 0                    |
   | ---------------- | -------------------- | -------------------- |


   ==========================================================================================
                                      Compute Performance
   ==========================================================================================

   Per-Domain Compute Throughput (local rank 0):

   | ----------------------- | ------------------ | ------------------ | ------------------ | ------------------ |
   |                  Metric |   d01              |   d02              |        All Domains |  Total (All Ranks) |
   | ----------------------- | ------------------ | ------------------ | ------------------ | ------------------ |
   |               # columns | 40000              | 62375              | 102.4 k            | 409.0 k            |
   |                # levels | 81                 | 81                 |                    |                    |
   |           # grid points | 3.240 M            | 5.052 M            | 8.292 M            | 33.13 M            |
   |             # timesteps | 13                 | 37                 |                    |                    |
   |    Total column updates | 520.0 k            | 2.308 M            | 2.828 M            | 11.29 M            |
   |        Total GP updates | 42.12 M            | 186.9 M            | 229.1 M            | 914.7 M            |
   | ----------------------- | ------------------ | ------------------ | ------------------ | ------------------ |
   |               Wall time | 7.40 s             | 26.3 s             | 33.7 s             | 33.7 s..33.8 s     |
   |         Comm scope time | 3.51 s             | 11.8 s             | 15.3 s             | 15.3 s..15.4 s     |
   |          Radiation time | 0.998 s            | 2.65 s             | 3.65 s             | 3.63 s..3.66 s     |
   | ----------------------- | ------------------ | ------------------ | ------------------ | ------------------ |
   |           Col Updates/s | 134 kupd/s         | 159 kupd/s         | 154 kupd/s         | 613 kupd/s         |
   | Col Updates/s (w/ Comm) | 70.3 kupd/s        | 87.7 kupd/s        | 83.8 kupd/s        | 334 kupd/s         |
   |  Col Updates/s (no rad) | 180 kupd/s         | 194 kupd/s         | 191 kupd/s         | 764 kupd/s         |
   |            GP Updates/s | 10.8 Mupd/s        | 12.9 Mupd/s        | 12.4 Mupd/s        | 49.7 Mupd/s        |
   |  GP Updates/s (w/ Comm) | 5.69 Mupd/s        | 7.10 Mupd/s        | 6.79 Mupd/s        | 27.1 Mupd/s        |
   |   GP Updates/s (no rad) | 14.6 Mupd/s        | 15.7 Mupd/s        | 15.5 Mupd/s        | 61.9 Mupd/s        |
   | ----------------------- | ------------------ | ------------------ | ------------------ | ------------------ |
     Numerator: #columns (or #grid points) x #solve_interface calls per domain.
     Excluded from all rows: initialization, history/restart I/O, lateral-BC
       reads, FDDA, nest interp/feedback (these run outside solve_interface).
     Base row denominator: solve_interface wall time MINUS HALO+Nesting communication scope
       time (i.e. compute-only: dynamics + physics + pack/unpack kernels).
     'w/ Comm' denominator: full solve_interface wall time (compute + communication scope).
     'no rad' denominator: base minus rad_driver_tim time.
     All Domains column: active domains summed on this MPI rank.
     Total (All Ranks) column: active domains summed across all MPI ranks.
     Timing rows show Total as the min..max range of the All Domains value across ranks.
     Update-rate totals sum the All Domains update rate across MPI ranks.


   ==========================================================================================
                                  Simplified Top-Down Profile
   ==========================================================================================

   Top-Down Profile Summary:
   | -------------------------------- | ------------ | --------- |
   |              Name                |   Time (s)   |  Time (%) |
   | -------------------------------- | ------------ | --------- |
   | WRF Total                        |    89.908511 |    100.00 |
   |     Initialization               |    16.031019 |     17.83 |
   |         Allocate                 |     3.936398 |      4.38 |
   |         Init I/O (Read)          |     9.196258 |     10.23 |
   |         Init I/O (Write)         |     0.000000 |      0.00 |
   |         HALO/Nesting Comm Scope  |     0.925088 |      1.03 |
   |         HALO/Nesting non-Comm    |     0.052485 |      0.06 |
   |         Other                    |     1.920790 |      2.14 |
   |     Integration                  |    73.877465 |     82.17 |
   |         I/O (Read)               |     0.159091 |      0.18 |
   |         I/O (Write)              |    36.466161 |     40.56 |
   |         HALO/Nesting Comm Scope  |    18.487557 |     20.56 |
   |         HALO/Nesting non-Comm    |     0.484992 |      0.54 |
   |         Compute/Other            |    18.279663 |     20.33 |
   |             Physics              |     6.290144 |      7.00 |
   |                 LW Radiation     |     0.936756 |      1.04 |
   |                 SW Radiation     |     2.679492 |      2.98 |
   |                 Surface Layer    |     0.110301 |      0.12 |
   |                 Land Surface     |     0.039150 |      0.04 |
   |                 PBL              |     1.303318 |      1.45 |
   |                 Cumulus          |     0.118034 |      0.13 |
   |                 Microphysics     |     0.449363 |      0.50 |
   |                 Physics Overhead |     0.653730 |      0.73 |
   |             Dynamics             |    13.517983 |     15.04 |
   |                 RK Setup         |     0.518204 |      0.58 |
   |                 Dry Dynamics     |     2.284024 |      2.54 |
   |                 Acoustic / Small |     6.485919 |      7.21 |
   |                 Scalar Transport |     3.362443 |      3.74 |
   |                 Dynamics BC/EOS  |     0.205207 |      0.23 |
   |                 Dyn/Phys Cplng   |     0.662185 |      0.74 |
   |             Residual             |     0.000000 |      0.00 |
   | -------------------------------- | ------------ | --------- |

   Projected overhead from timer usage:       0.01s (0.011544% of main),    328ns per call (average)
     MPI_WTICK() =    1.0000000000000001E-009

Enable NVTX Ranges for Nsight Systems
-------------------------------------

AceCAST v4.6.1 can optionally emit NVTX ranges from the same timer infrastructure so that the
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
