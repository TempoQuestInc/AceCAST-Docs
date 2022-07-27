.. meta::
   :description: Version history of AceCast, click for more
   :keywords: Version, history, releases, AceCast, Documentation, TempoQuest, download, downloads

.. _releaseslink:

Releases
########


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

.. note::
   Not sure what we mean by **supported namelist options**? Check out the 
   :ref:`namelistconfiguration` section.

.. note::
   We conceptualized this versioning scheme only after realizing it was necessary to do so while
   working on AceCAST version **2.0.0**. You may notice some inconsistencies prior to this version.

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

Version 1.3.0 and Older
=======================

Due to the major changes from AceCAST version *1.** to version *2.**, it is best to use the 
archived `acecast-v1 docs <https://acecast-docs.readthedocs.io/en/acecast-v1/>` version of the 
documentation.
