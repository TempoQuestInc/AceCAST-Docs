.. meta::
   :description: Version history of AceCast, click for more
   :keywords: Version, history, releases, AceCast, Documentation, TempoQuest, download, downloads

.. _releaseslink:

Releases
########

Jump to most recent AceCAST version: :ref:`latestlink`

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

.. list-table:: AceCAST GPU WRF vs NCAR CPU WRF Version Equivalency
   :widths: 40 40
   :header-rows: 1

   * - AceCAST Version
     - Corresponding WRF Version
   * - 1.0
     - 3.8.1
   * - 1.1
     - 3.8.1
   * - 1.2
     - 3.8.1
   * - 1.3
     - 3.8.1
   * - 2.0
     - 4.2.2
   * - 2.1
     - 4.2.2
   * - 3.0
     - 4.4.2
   * - 3.1
     - 4.4.2
   * - 3.2
     - 4.4.2
   * - 4.0
     - 4.6.0
   * - 4.3
     - 4.6.0

For any given version we will provide release notes and download information within its own 
subsection on this page. 

.. tip::
   Not sure what we mean by **supported namelist options**? Check out the 
   :ref:`namelistconfiguration` section.


.. _latestlink:

Version 4.3.1
=============

Skip to :ref:`v4_3_1_downloads_link`

Release Notes
-------------

**Performance**
***************

* This release delivers a major performance enhancement to the Thompson microphysics scheme on GPU. The main kernel has been restructured to take full advantage of 3D parallelization where possible, resulting in significantly faster execution for simulations using this microphysics scheme. Internal testing shows substantial speed improvements with no loss of numerical accuracy.
* Comprehensive performance optimizations were applied across multiple model components, resulting in overall runtime reductions exceeding 20% in most configurations and potentially significantly more.
* Added the joinwrf utility (in the standard run/ directory) to support the io_form_history = 102 option. This mode writes each MPI rank’s output to a separate file, significantly reducing parallel I/O overhead. The joinwrf utility can then be used to merge these patch files into standard WRF output. The generate_namelist_join.py script is included to create the joinwrf namelists automatically from a WRF namelist. For details on merging tiled WRF outputs, see the :ref:`JoinWRF` section.

New in v4.3.1
*************

* Added support for Blackwell GPUs.
* AceCAST is now available for trial on NVIDIA Grace Hopper and Grace Blackwell GPU-based aarch64 systems. To request access, please contact support@tempoquest.com.
* Improvements to the wind turbine drag parameterization scheme (windfarm_opt = 1) were made based on the results described in "Brief communication: A simple axial induction modification to the Weather Research and Forecasting Fitch wind farm parameterization" (see https://wes.copernicus.org/articles/9/1689/2024/wes-9-1689-2024.pdf). Note that this is a divergence from the standard WRF model implementation of this scheme.
* AceCAST now offers an experimental option allowing users to specify 8 or 10 soil layers in addition to the standard 4 soil layers when running the NoahMP land surface model. Early testing suggests that using a finer vertical soil structure can improve surface fluxes for light to moderate rainfall events, which can significantly improve boundary layer dynamics.
* Added support for a number of options associated with ideal test cases. This includes periodic boundary conditions (periodic_x = T, periodic_y = T), idealized land mask selection (ideal_xland = 1 or ideal_xland = 2) and the Coriolis perturbation option (pert_coriolis = T).

Improvements and Bug Fixes
**************************

* Fixed an issue where radar reflectivity and other diagnostics were not computed when output was directed to non-history streams. The diagnostic flag logic has been updated to check all active output alarms rather than only the history alarm, ensuring that diagnostics are properly generated for auxiliary history streams under runtime I/O configurations. This resolves cases where reflectivity fields appeared as zero outside of the primary history output interval.
* Resolved an indexing error in the NoahMP snowpack melt logic that could misapply snow compaction and melt tendencies in AceCAST-GPU runs. This fix improves the timing of snowmelt and associated surface fluxes, reducing localized cold biases in near-surface air temperature over melting snowpacks.

Licensing Changes
*****************

* AceCAST now requires that you point to the license file with the *RLM_LICENSE* environment variable rather than placing the license file in the run directory. For a full description of license usage check out the :ref:`Licenselink` page.
* When using I/O quilting, AceCAST now correctly counts only compute processes rather than both compute and I/O processes—when determining the total number of GPUs used for licensing purposes.

.. _v4_3_1_downloads_link:

Downloads
---------
 
* AceCAST version 4.3.1 for Linux x86-64: `AceCASTv4.3.1.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/distros/acecast-v4.3.1%2Blinux.x86_64.nvhpc25.9.tar.gz>`_

.. important::
   AceCAST v4.3.1 uses the NVHPC SDK version 25.9. Previous versions of AceCAST required older versions of the NVHPC SDK. Users will need to install this newer version of the NVIDIA HPC SDK with the new version of AceCAST. To do this please follow the instructions in the :ref:`installationguide`.

.. important::
   Check out the :ref:`installationguide` for further installation instructions.

.. tip::
   If you would like to download the package from the command line you can use the `wget` or `curl`
   commands with the download link url from above.

Version 4.0.2
=============

Skip to :ref:`v4_0_2_downloads_link`

Release Notes
-------------

.. important::
   AceCAST v4.0.2 uses the NVHPC SDK version 24.3. Previous versions of AceCAST required the NVHPC SDK version 21.9. Users will need to install this newer version of the NVIDIA HPC SDK with the new version of AceCAST. To do this please follow the instructions in the :ref:`installationguide`.

The AceCAST version 4.0.2 release includes major updates to implement the equivalent 
`CPU-WRF version 4.6.0 release <https://github.com/wrf-model/WRF/releases/tag/v4.6.0>`_. For reference, the previous version of AceCAST (version 3.2) implemented the 
`CPU-WRF version 4.4.2 release <https://github.com/wrf-model/WRF/releases/tag/v4.4.2>`_. If you 
would like more information regarding the WRF updates that were implemented in this new version of 
AceCAST, check out the 
`release notes for WRF versions 4.4.2 through 4.6.0 <https://github.com/wrf-model/WRF/releases>`_.

New in v4.0.2
*************

* Added support for Digital Filter Initialization (DFI). Digital filter initialization is a method to remove initial model imbalance as, for example, measured by the surface pressure tendency. This may be important when one is interested in the 0 – 6 hour simulation/forecast. It runs a digital filter during a short model integration, backward and forward, and then starts the forecast (WRF Users Guide).
* Added support for *mp_physics = 38* (Thompson Hail/Graupel/Aerosol Microphysics). Similar to option 28, but computes two-moment prognostics for graupel and hail and includes a predicted density graupel category.
* Added support for *use_aero_icbc. = .true.*.

Improvements and Bug Fixes
**************************

* Fixed an issue where the *output_ready_flag=1* option wasn't writing the wrfoutReady* files when using the parallel netcdf option (*io_form_history = 11*).
* Added a tiled implementation of all Thompson microphysics options ( *mp_physics = 8, 28, 38*) that will reduce the memory overheads of using these options.
* Reimplemented the gravity wave drag option (*gwd_opt =1*) to be consistent with the CPU WRF Common Community Physics Package (CCPP) implementation of the option.
* Reimplemented the MYNN PBL option (*bl_pbl_physics = 5*) to be consistent with the CPU WRF Common Community Physics Package (CCPP) implementation of the option. For more information regarding the major changes to MYNN please refer to https://github.com/wrf-model/WRF/pull/1788.
* Reimplemented the YSU PBL option (*bl_pbl_physics = 1*) to be consistent with the CPU WRF Common Community Physics Package (CCPP) implementation of the option.
* Reimplemented the revised MM5 surface layer option (*sf_sfclay_physics =1*) to be consistent with the CPU WRF Common Community Physics Package (CCPP) implementation of the option.


Known Issues
************

* The YSU PBL scheme fails randomly with a "*variable in data clause is partially present on the device*" error for some configurations.

.. _v4_0_2_downloads_link:

Downloads
---------
 
* AceCAST version 4.0.2 for Linux x86-64: `AceCASTv4.0.2.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/distros/acecast-v4.0.2%2Blinux.x86_64.haswell.nvhpc24.3.tar.gz>`_

.. important::
   Check out the :ref:`installationguide` for further installation instructions.

.. tip::
   If you would like to download the package from the command line you can use the `wget` or `curl`
   commands with the download link url from above.

Version 3.2.2
=============

Skip to :ref:`v3_2_2_downloads_link`

Release Notes
-------------

New in v3.2.2
*************

* Added support for observational nudging (obs_nudge_opt = 1) and all associated sub-options. Observational nudging is a method of nudging the model where point near observations are nudged based on model error at the observation site. For more information on observational nudging check out https://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.4/users_guide_chap5.html#obsnudge.
* Added support for the *multi_bdy_files = T* option. This option can be used to run the preprocessing components (ungrib, metgrid, real) to generate the AceCAST boundary conditions asynchronously during the execution of the WRF/AceCAST simulation itself. For more information on its usage see `WRF Docs - Use of Multiple Lateral Condition Files <https://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.4/users_guide_chap5.html#LBC>`_).
* Added support for the widely used NoahMP land surface option *sf_surface_physics = 4*. This includes support for all of the NoahMP-related sub-options including all valid choices for *dveg*, *opt_crs*, *opt_btr*, *opt_run*, *opt_sfc*, *opt_frz*, *opt_inf*, *opt_rad*, *opt_alb*, *opt_snf*, *opt_tbot*, *opt_stc*, *opt_gla*, *opt_rsf*, *opt_soil*, *opt_pedo*, *opt_crop*, *opt_irr*, *opt_irrm*, *opt_infdv*, *opt_tdrn*, and *noahmp_output* in the *&noah_mp* namelist section. The exceptions to this are that *opt_crop = 2* and *opt_run = 5* are not supported. Note that we made some important improvements to NoahMP that are outlined in the Improvements and Bug Fixes section below.
* Added support for the IEVA (Implicit Explicit Vertical Advection) option *zadvect_implicit = 1*. For grids with large aspect ratios (dx/dz >> 1) that permit explicit convection, the large time step is limited by the strongest updraft that occurs during integration. This results in time step often 20-30% smaller, or requires the use of w-filtering, such as latent-heat tendency limiting. Regions of large vertical velocities are also often very small relative to the domain. The IEVA scheme permits a larger time step by partitioning the vertical transport into an explicit piece, which uses the normal vertical schemes present in WRF, and a implicit piece which uses implicit transport (which is unconditionally stable). The combined scheme permits a larger time step than has been previously been used and reduced w-filtering. (Wicker and Skamarock, 2020, MWR)

Improvements and Bug Fixes
**************************

* The NoahMP implementation in WRF v4.2.2 has significant bugs that needed to be addressed (see https://forum.mmm.ucar.edu/threads/strange-t2-bias-in-newer-wrf-versions.12259/). Due to this we have decided to implement the newest NoahMP version for the WRF code which will likely be included in WRF version 4.5.2 or later. This includes bug fixes and a number of significant improvements that are important for anyone using the NoahMP surface layer scheme in AceCAST (*sf_surface_physics* option 4).
* Implemented a tiled version of the MYNN PBL schemes (both bl_pbl_physics = 5 and bl_pbl_physics = 6). The previous implementations had massive GPU memory requirements that were overly restrictive for users of these schemes. The new tiled implementation will dynamically determine tile sizes based on the available GPU memory at runtime.
* Fixed issue in Revised MM5 surface layer scheme (sf_sfclay_physics = 1) where in certain conditions the model would hang due to an infinite loop.
* Revised MM5 will now report an error and forcefully exit if it attempts to use an invalid index into a lookup table during the surface layer calculation. This occurs when the model is becoming unstable and unrealistic values are being passed through the model. Previously, in both WRF and AceCAST, this situation would have caused an obscure seg-fault with no explanation. We thought it would be helpful instead to report when this issue occurs and stop the simulation afterwards.
* AceCAST now reports where significant CFL violations occur, which can be very useful when users encounter issues with numerical instabilities in their simulations.
* Fixed a bug that was introduced in WRF v4.4.2 in the AFWA diagnostics that caused all of the CAPE-related fields to be zero. These fields are now calculated correctly.
* In version 3.0 we introduced optimizations that improved RRTMG performance. Due to a CUDA compiler bug this ended up causing the model to crash in many cases when running on GPUs with compute capabilities older than 8.0. These performance optimizations have been reverted in this version to ensure portability of the code on older GPUs and will be reintroduced in a future version of AceCAST when the CUDA compiler issues are resolved by NVIDIA.


Known Issues
------------

MYNN PBL Sub-Options
********************

Both the *icloud_bl = 0* and *bl_mynn_cloudpdf = 0* options fail when using the MYNN PBL option 
(*bl_pbl_physics = 5*). If these options are critical for your simulations please contact us at 
support@tempoquest.com to ensure that we prioritize fixing this issue.

.. _v3_2_2_downloads_link:

Downloads
---------
 
* AceCAST version 3.2.2 for Linux x86-64: `AceCASTv3.2.2.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/distros/acecast-v3.2.2%2Blinux.x86_64.haswell.tar.gz>`_

.. important::
   Check out the :ref:`installationguide` for further installation instructions.

.. tip::
   If you would like to download the package from the command line you can use the `wget` or `curl`
   commands with the download link url from above.

Version 3.1.0
=============

Skip to :ref:`v3_1_0_downloads_link`

Release Notes
-------------

New in v3.1.0
*************

* Added support for the Purdue-Lin microphysics *mp_physics=2*. This is a sophisticated scheme that has ice, snow and graupel processes, suitable for real-data high-resolution simulations.
* Added support for all AFWA diagnostics options. For more information on these options check out (http://www2.mmm.ucar.edu/wrf/users/docs/AFWA_Diagnostics_in_WRF.pdf).
* Added support for spectral nudging *grid_fdda=2*. See `WRF user guide - Analysis Nudging Runs <https://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.4/users_guide_chap5.html#gridnudge>`_ for more information.
* Added support for isotropic diffusion *mix_isotropic=1*.
* Added support for Morrison microphysics *mp_physics=10*. Double-moment ice, snow, rain and graupel for cloud-resolving simulations.
* Added support for the wind turbine drag parameterization scheme *windfarm_opt=1*. It represents sub-grid effects of specified turbines on wind and TKE fields. For more information on using this option see `WRF README.windturbine <https://github.com/wrf-model/WRF/blob/master/doc/README.windturbine>`_.
* Added support for restart runs *restart=T*.
* Added support for Morrison double-moment microphysics with CESM aerosols *mp_physics = 40*.
* Added support for the *insert_init_cloud = T* option, which turns on estimation of initial model clouds.
* Added support for *ra_call_offset = -1* (calls radiation before output).
* Added support for all user-specified values of the *blend_width* option. The *blend_width* option determines the number of grid points in the terrain blending zone from the coarse grid to the fine grid for nested domains.
* Added support for all aerosol input options to RRTMG *aer_opt=1*, *aer_opt=2* and *aer_opt=3*.
* AceCAST has been modified to enable use within the `UEMS forecasting framework <https://strc.comet.ucar.edu/software/uems/>`_. Please contact `support@tempoquest.com` for more information regarding using AceCAST in UEMS.
* AceCAST executables now link to the NVIDIA HPC SDK and CUDA libraries dynamically. Users who have already installed the NVIDIA HPC SDK v21.9 for AceCAST may need to update their environment setup scripts accordingly to ensure the correct libraries are found at runtime (see :ref:`nvhpc_install`). 

Improvements
************

* Using the runtime I/O field modifications with the *iofields_filename* option was incredibly slow when users had significant numbers of changes since the associated routines were called on every history interval unnecessarily. This is now done a single time at the start of the simulation removing nearly all overhead associated with this option.

Known Issues
------------

Illegal address during kernel execution in RRTMG
************************************************

A number of users have reported an issue where AceCAST fails with the following message:

.. code-block:: output

    WRF TILE   1 IS      1 IE    500 JS      1 JE    500
    WRF NUMBER OF TILES =   1
    an illegal memory access was encountered in ../UWisc/RRTMG_LW/rrtmg_lwrad_cuda.cu at line 698

We believe this may be a problem with the CUDA rutime/drivers and are investigating the issue. One 
thing that may help users in the meantime is to use different values for the RRTMG tile size by 
setting the *ACECAST_RRTMG_LW_NUM_TILES* environment variable and running again:

.. code-block:: bash

    # Example setting the number of tiles to 3
    export ACECAST_RRTMG_LW_NUM_TILES=3
    mpirun -n 4 ./gpu-launch.sh ./acecast.exe

We suggest trying tile sizes of anything between 1 and 20. In some cases this doesn't fix the issue.

MYNN PBL Sub-Options
********************

Both the *icloud_bl = 0* and *bl_mynn_cloudpdf = 0* options fail when using the MYNN PBL option 
(*bl_pbl_physics = 5*). If these options are critical for your simulations please contact us at 
support@tempoquest.com to ensure that we prioritize fixing this issue.


.. _v3_1_0_downloads_link:

Downloads
---------
 
* AceCAST version 3.1.0 for Linux x86-64: `AceCASTv3.1.0.tar.gz <https://tqi-public.s3.us-east-2.amazonaws.com/distros/acecast-v3.1.0%2Blinux.x86_64.haswell.tar.gz>`_

.. important::
   Check out the :ref:`installationguide` for further installation instructions.

.. tip::
   If you would like to download the package from the command line you can use the `wget` or `curl`
   commands with the download link url from above.

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
