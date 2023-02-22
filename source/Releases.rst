.. meta::
   :description: Version history of AceCast, click for more
   :keywords: Version, history, releases, AceCast, Documentation, TempoQuest, download, downloads

.. _releaseslink:

Releases
########

Latest Version: :ref:`latestlink`

Release and Versioning Methodology
==================================

To ensure consistency and improve overall usability, AceCAST uses a pseudo-semantic versioning 
scheme (see more on semantic versioning at `<https://semver.org/>`_). Any given release version 
will have a version number `MAJOR.MINOR.PATCH`, which can be understood as follows:

* **MAJOR**: Changes in the **MAJOR** version indicate an upgrade to a new version of WRF. For
  example AceCAST versions `1.*.*` were all based on WRF version `3.8.1` while all AceCAST 
  versions `2.*.*` will be based on WRF version `4.2.2`.
* **MINOR**: Changes in the **MINOR** version indicate added support for new namelist options 
  or other important features. For example AceCAST version `1.3.0` added support for a number 
  of options such as `grid_fdda=1` and `fractional_seaice=1`, neither of which were available 
  in AceCAST version `1.2.0`.
* **PATCH**: Changes in the **PATCH** version indicate a bug fix related to a supported 
  namelist option or feature.

For any given version we will provide release notes and download information within its own 
subsection on this page. 

.. tip::
   Not sure what we mean by **supported namelist options**? Check out the 
   :ref:`namelistconfiguration` section.

.. .. note::
..    We conceptualized this versioning scheme only after realizing it was necessary to do so while
..    working on AceCAST version **2.0.0**. You may notice some inconsistencies prior to this version.

.. _latestlink:

Version 3.0.1
=============

Skip to :ref:`v3_0_1_downloads_link`

Release Notes
-------------

The AceCAST version 3.0.1 release includes major updates to implement the 
`CPU-WRF version 4.4.2 release <https://github.com/wrf-model/WRF/releases/tag/v4.4.2>`_, which is 
the newest release of WRF (as of Feb. 18th 2023). For reference, AceCAST version 2 implemented the
`CPU-WRF version 4.2.2 release <https://github.com/wrf-model/WRF/releases/tag/v4.2.2>`_. If you 
would like more information regarding the WRF updates that were implemented in this new version of 
AceCAST, check out the 
`release notes for WRF versions 4.2.2 through 4.4.2 <https://github.com/wrf-model/WRF/releases>`_.

In addition, AceCAST version 3.0.1 includes a number of new features and bug fixes that are outlined
below.

New in v3.0.1
*************

* Added support for full 3D diffusion option *diff_opt = 2*

* Added support for LES-specific options including *km_opt = 2*, *km_opt = 3* and *m_opt = 1*

* Added support for Rayleigh damping *damp_opt = 2*

* Added support for the "original" scalar advection options *moist_adv_opt = 0*, *chem_adv_opt = 0*, 
  *tracer_adv_opt = 0*, *scalar_adv_opt = 0* and *tke_adv_opt = 0*

* Added support for water and ice friendly aerosols option *wif_input_opt = 1* for use with 
  Thompson aerosol aware microphysics (*mp_physics = 28*)

* Added support for various accumulated diagnostic options including any user-specified values for
  *bucket_mm*, *bucket_J* and *prec_acc_dt* as well as support for *acc_phy_tend = 1*

* Added support for UA Noah LSM snow-cover physics option *ua_phys = .true.*

* Added support for using no microphysics option *mp_physics = 0*

Improvements
************

* Performance optimizations for RRTMG shortwave and longwave schemes (*ra_sw_physics = 4* and 
  *ra_lw_physics = 4*) as well as for WSM6 microphysics (*mp_physics = 6*). Although the impact
  of these optimizations will vary significantly from case to case, these optimizations resulted in
  overall speedups of up to 15% during our testing.

* Improvements to the performance profiling activated with the environment variable 
  *ACECAST_USE_TIMERS=true*. The top-down profile generated at the end of the rsl log files is 
  extremely useful but can be hard to interpret for anyone other than the developers of AceCAST.
  This option now outputs a "summary" of the timing profile which should help users understand where 
  the the time is being spent. Example (from rsl.error.0000 file):

.. code-block:: output

    Summary:
    | -------------------------------- | ------------ | --------- |
    |              Name                |   Time (s)   |  Time (%) |
    | -------------------------------- | ------------ | --------- |
    | WRF Total                        |   200.296238 |    100.00 |
    |     Initialization               |    46.051199 |     22.99 |
    |         Allocate                 |     3.210721 |      1.60 |
    |         I/O (Read)               |    41.070188 |     20.50 |
    |         I/O (Write)              |     0.000000 |      0.00 |
    |         HALO/Nesting (MPI)       |     0.136974 |      0.07 |
    |         HALO/Nesting (non-MPI)   |     0.021627 |      0.01 |
    |         Compute/Other            |     1.611689 |      0.80 |
    |     Integration                  |   154.244787 |     77.01 |
    |         I/O (Read)               |     0.769853 |      0.38 |
    |         I/O (Write)              |    42.757482 |     21.35 |
    |         HALO/Nesting (MPI)       |     5.807679 |      2.90 |
    |         HALO/Nesting (non-MPI)   |     3.958668 |      1.98 |
    |         Compute/Other            |   100.951104 |     50.40 |
    |             LW Radiation         |     4.589823 |      2.29 |
    |             SW Radiation         |     9.976138 |      4.98 |
    |             Surface Layer        |     0.489929 |      0.24 |
    |             Land Surface         |     1.183034 |      0.59 |
    |             PBL                  |     5.112687 |      2.55 |
    |             Cumulus              |     0.000000 |      0.00 |
    |             Microphysics         |     9.959394 |      4.97 |
    | -------------------------------- | ------------ | --------- |

    d01 2019-11-26_19:00:00 wrf: SUCCESS COMPLETE WRF


Bug Fixes
*********

* `WRF version 4.1.3 <https://github.com/wrf-model/WRF/releases/tag/v4.1.3>`_ included a bug fix 
  related to the single-scattering albedo and asymmetry input parameters in the RRTMG shortwave
  scheme (see `WRF PR#997 <https://github.com/wrf-model/WRF/commit/609f957bb05673d3007ddd5808e7e246b8aec239>`_). 
  This bug fix was not correctly implemented in AceCAST version 2, which was calculating these 
  values the same way that WRF versions 3.5.1 through 4.1.2 were. This resulted in a slight but 
  clear cold bias in areas with clouds when compared to simulations using newer versions of CPU-WRF.
  This issue has been fixed in this new version of AceCAST.

* Removed support for cloud overlap options *cldovrlp = 3* and *cldovrlp = 4*. It turned out that
  our GPU implementation was using *cldovrlp = 2* regardless of what the user specified in their
  namelist.

* A bug has been fixed where the model would hang at the start of a run when users attempted to use
  I/O quilting.

* A bug has been fixed in Thompson Microphysics (*mp_physics = 8*) where, with rare but specific 
  patch decompositions, AceCAST did not allocate enough memory for some variables, which caused an 
  *Illegal address during kernel execution* error.

Known Issues
------------

YSU PBL Performance
*******************

AceCAST version 3.0.1 introduced changes to the YSU PBL scheme (*bl_pbl_physics = 1*) that degraded 
the performance. This PBL scheme isn't particularly expensive but this performance issue may offset 
some of the performance improvements from other schemes introduced in this version of AceCAST. This
is a widely used option and we intend on addressing the performance in the near future.

Using WRF Restart Files
***********************

AceCAST will fail if you attempt to do a restart run using a restart file that was generated using 
CPU-WRF rather than another AceCAST run. This is a rare situation but users can avoid this issue by 
setting the *force_use_old_data = .true.* option in the *&time_control* section of the namelist.

MYNN PBL Sub-Options
********************

Both the *icloud_bl = 0* and *bl_mynn_cloudpdf = 0* options fail when using the MYNN PBL option 
(*bl_pbl_physics = 5*). If these options are critical for your simulations please contact us at 
support@tempoquest.com to ensure that we prioritize fixing this issue.


.. _v3_0_1_downloads_link:

Downloads
---------
 
* AceCAST version 3.0.1 for Linux x86-64: `AceCASTv3.0.1.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/distros/acecast-v3.0.1%2Blinux.x86_64.haswell.tar.gz>`_

.. important::
   Check out the :ref:`installationguide` for further installation instructions.

.. tip::
   If you would like to download the package from the command line you can use the `wget` or `curl`
   commands with the download link url from above.

Version 2.1.0
=============

Release Notes
-------------

AceCAST version 2.1.0 includes a number of critical bug fixes as well as support for new options.

New in v2.1.0
*************

* Added support for Tiedtke cumulus physics scheme (*cu_physics = 6*). Note that this completes
  AceCAST's support for all options associated with the *CONUS* physics suite 
  (*physics_suite = 'conus'*).

* Added support for SST Updates (*sst_update = 1*). This option can be critical for longer 
  simulations where sea surface temperatures and a number of other surface fields vary enough that
  they should be updated throughout the simulation. For more information 
  `WRF Docs -- SST Update <https://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.2/users_guide_chap5.html#sst_update>`_
  for more information.

* Added environment variable *ACECAST_NPROC_X*, which can be used to control the MPI domain 
  decomposition at runtime. In many cases this option can be used to significantly improve MPI
  communication patterns in multi-gpu runs and can reduce overall runtimes by up to 15% in our 
  experience internally (we suggest starting with *ACECAST_NPROC_X=1*).

* Added environment variable *ACECAST_ALIGN_OPT_LEVEL*, which can be used to control if memory
  dimensions should be aligned to improve memory access at the cost of extra memory overhead. 
  Setting *ACECAST_ALIGN_OPT_LEVEL=0* will typically reduce the memory overhead of a simulation by 
  up to 20% but will reduce the performance as well and is only recommended for users that are 
  highly constrained by GPU memory capacity.

Bug Fixes
*********

* AceCAST dynamically determines a tile size when calculating the RRTMG radiation components to 
  reduce the massive memory overhead that they require (see :ref:`rrtmg_mem_util_issue`). The tile
  size was not being calculated correctly, which caused AceCAST to use significantly more memory 
  than was necessary (up to 100% or more in some cases). This issue has been fixed.

* Fixed issue where AceCAST failed when using the *fractional_seaice = 1* option with any surface
  layer option other than Revised MM5 (*sf_sfclay_physics = 1*).

* Even though it was working as intended, the `acecast-advisor.sh` script was previously printing 
  the incorrect *AceCAST Version* and *WRF Compatibility Version* when using the *support check* 
  tool. It should now print the correct versions.

Downloads
---------
 
* AceCAST version 2.1.0 for Linux x86-64: `AceCASTv2.1.0.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/distros/acecast-v2.1.0%2Blinux.x86_64.haswell.tar.gz>`_

.. important::
   Check out the :ref:`installationguide` for further installation instructions.

.. tip::
   If you would like to download the package from the command line you can use the `wget` or `curl`
   commands with the download link url from above.

Known Issues
------------

SSA Calculation in RRTMG
************************

`WRF version 4.1.3 <https://github.com/wrf-model/WRF/releases/tag/v4.1.3>`_ included a bug fix 
related to the single-scattering albedo and asymmetry input parameters in the RRTMG shortwave
scheme (see `WRF PR#997 <https://github.com/wrf-model/WRF/commit/609f957bb05673d3007ddd5808e7e246b8aec239>`_). 
This bug fix was not correctly implemented in AceCAST version 2, which is calculating these 
values the same way that WRF versions 3.5.1 through 4.1.2 were. This results in a slight but 
clear cold bias in areas with clouds when compared to simulations using newer versions of CPU-WRF.

MYNN PBL Sub-Options
********************

Both the *icloud_bl = 0* and *bl_mynn_cloudpdf = 0* options fail when using the MYNN PBL option 
(*bl_pbl_physics = 5*). If these options are critical for your simulations please contact us at 
support@tempoquest.com to ensure that we prioritize fixing this issue.

Version 2.0.0
=============

Release Notes
-------------

This is the first release of our highly anticipated upgraded version of AceCAST based on WRF 
version 4.2.2. This involved a massive rework of the entire code base due to the significant 
changes between WRF versions 3.8.1 and 4.2.2. For a comprehensive list of supported options, check 
out the :ref:`nmlsupporttbl` page.

Downloads
---------

 
* AceCAST version 2.0.0 for Linux x86-64: `AceCASTv2.0.0.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/distros/acecast-v2.0.0%2Blinux.x86_64.haswell.tar.gz>`_

.. important::
   Check out the :ref:`installationguide` for further installation instructions.

.. tip::
   If you would like to download the package from the command line you can use the `wget` or `curl`
   commands with the download link url from above.

Known Issues
------------

SSA Calculation in RRTMG
************************

`WRF version 4.1.3 <https://github.com/wrf-model/WRF/releases/tag/v4.1.3>`_ included a bug fix 
related to the single-scattering albedo and asymmetry input parameters in the RRTMG shortwave
scheme (see `WRF PR#997 <https://github.com/wrf-model/WRF/commit/609f957bb05673d3007ddd5808e7e246b8aec239>`_). 
This bug fix was not correctly implemented in AceCAST version 2, which is calculating these 
values the same way that WRF versions 3.5.1 through 4.1.2 were. This results in a slight but 
clear cold bias in areas with clouds when compared to simulations using newer versions of CPU-WRF.

.. _rrtmg_mem_util_issue:

GPU Memory Utilization Issue
****************************

The RRTMG radiation options (*ra_sw_physics=4*, *ra_lw_physics=4*) require a significant amount of 
GPU memory that would typically be highly restictive when users are running with large grids. To 
mitigate this issue we use a *tiled* version of these RRTMG routines, which break down the grid 
into smaller chunks that fit into the available GPU memory and perform the radiation calculations 
for each of these chunks sequentially. **Due to a minor integer overflow issue, this dynamic tile 
size calculation doesn't currently work for larger grid sizes.** This issue does not effect the 
results of any simulations but does significantly limit the grid sizes that can be used for any 
given GPU. This issue will be resolved in the new version of AceCAST.

Fractional Seaice Issue
***********************

AceCAST fails with the following message when using the *fractional_seaice = 1* option together 
with the *sf_sfclay_physics = 2* (eta similarity) or *sf_sfclay_physics = 5* (MYNN) surface layer 
options:

.. code-block:: output

    -------------- FATAL CALLED ---------------
    FATAL CALLED FROM FILE:  module_surface_driver.G  LINE:    4936
    error -- routine not yet implemented
    -------------------------------------------

If you encounter this issue you can turn off the fractional seaice option (*fractional_seaice = 0*) 
or use it with the *sf_sfclay_physics=1* surface layer option (Revised MM5). This issue will be 
resolved in the next release of AceCAST.

Incorrect Version Messaging in the AceCAST Advisor Script
*********************************************************

There is currently a bug in the `acecast-advisor.sh` script where the `AceCAST Version` is `1.2` 
rather than `2.0.0` and the `WRF Compatibility Version` is `3.8.1` rather than `4.2.2`. The script 
works correctly and the incorrect versions in the output can be ignored.

Version 1.3 and Older
=====================

Due to the major changes from AceCAST version *1.** to version *2.**, it is best to use the 
archived `acecast-v1 docs <https://acecast-docs.readthedocs.io/en/acecast-v1/>`_ version of the 
documentation.
