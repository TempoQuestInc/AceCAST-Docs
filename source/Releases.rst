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
