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

Version 1.3 and Older
=====================

Due to the major changes from AceCAST version *1.** to version *2.**, it is best to use the 
archived `acecast-v1 docs <https://acecast-docs.readthedocs.io/en/acecast-v1/>`_ version of the 
documentation.
