.. meta::
   :description: Create a namelist for AceCast, click for more
   :keywords: Namelist, Create, AceCast, Documentation, TempoQuest

.. _CPU WRF Namelist Options:
   https://www2.mmm.ucar.edu/wrf/users/docs/user_guide_v4/v4.2/users_guide_chap5.html#Namelist

.. _namelistconfiguration:

Namelist Configuration
######################

AceCAST vs WRF Overview
=======================

As we have discussed previously, AceCAST is simply a GPU-accelerated implementation of the 
CPU-based WRF model that is widely used by the community. We strive to make AceCAST a simple drop-in 
replacement for CPU WRF to keep things as simple as possible for our users. This means it uses the
same input files (`wrfinput*, wrfbdy*, etc.`) and configuration files (`namelist.input`) and 
produces the same output files (`wrfout*, wrfrst*, etc.`) as CPU WRF. It will even produce the same
results in the output fields (within acceptable floating-point error tolerances) for simulations 
using the same input and configuration files as CPU WRF.

The one caveat to the statement above is that AceCAST does not support every single namelist option
that is available in CPU WRF (see `CPU WRF Namelist Options`_). WRF has a huge number of features 
but many of these namelist options are not used by the majority of WRF users. To ensure the quality 
and the maintainability of AceCAST, we prioritize supporting the namelist options that have been 
requested by our users.

Since AceCAST limits the namelist options that are otherwise available in CPU WRF (of which there 
are thousands), we have developed a number of protections and tools to make sure it is clear to 
users which options are available as well as to prevent the user from using namelist options that 
aren't supported in AceCAST.


General Recommendations for Users
=================================

If you are simply looking to see which options are available in AceCAST, we recommend skipping to 
the :ref:`nmlcfgsupporttableapp` subsection below.

If you already have a namelist (from your CPU WRF configurations or elsewhere) and would like to 
see if the namelist is supported or not, check out the :ref:`nmlcfgnmlsupporttool` subsection 
below.

If you are interested in using AceCAST but the options specified in your namelist aren't currently
supported by AceCAST and there aren't any acceptable alternatives, we would greatly appreciate if 
you could contact us at support@tempoquest.com to discuss the possibility of supporting those 
options in future releases.


.. _nmlcfgsupporttableapp:

Interactive Namelist Support Table App
======================================

For a complete list of supported namelist options available in AceCAST, we highly recommend 
checking out the :ref:`nmlsupporttbl` tool. 

Most options can be turned off with a value of 0, where applicable. AceCAST supports the same output 
as WRF which includes but is not limited to netCDF and GRIB.

.. _nmlunsupported:

.. raw:: html
   
   <iframe src="https://docs.google.com/forms/d/e/1FAIpQLSfVZ_7KQSJ7pUrHPk3aKYvTqDklBm9g1bmnCRkou-zfmreLSA/viewform?embedded=true" 
    width="900" height="470" frameborder="0" marginheight="0" marginwidth="0">Loading…</iframe>


.. _nmlcfgnmlsupporttool:

AceCAST Advisor -- Support Check Tool
=====================================

The `acecast-advisor.sh` script is provided within the AceCAST distribution packages themselves.
Once AceCAST is installed this script (found in the `acecast/run/` sub-directory) can be used to 
check if the options specified in an existing namelist are supported by AceCAST as follows:

.. note::
   We understand that some users may want to check if their namelists are supported by AceCAST 
   **prior** to installing AceCAST. We are currently working on an online tool that would perform 
   the same tests in a browser to avoid this issue.

.. SE: this isn't DRY -- we use this same content in the "running acecast" section

.. tabs::

    .. tab:: command

        .. code-block:: shell

            # cd to the simulation run directory if you aren't already there
            ./acecast-advisor.sh --tool support-check

    .. tab:: output for supported namelist

        .. code-block:: output

    
            ***********************************************************************************
            *      ___           _____           _      ___      _       _                    *
            *     / _ \         /  __ \         | |    / _ \    | |     (_)                   *
            *    / /_\ \ ___ ___| /  \/ __ _ ___| |_  / /_\ \ __| |_   ___ ___  ___  ____     *
            *    |  _  |/ __/ _ \ |    / _` / __| __| |  _  |/ _` \ \ / / / __|/ _ \|  __|    *
            *    | | | | (_|  __/ \__/\ (_| \__ \ |_  | | | | (_| |\ V /| \__ \ (_) | |       *
            *    \_| |_/\___\___|\____/\__,_|___/\__| \_| |_/\__,_| \_/ |_|___/\___/|_|       *
            *                                                                                 *
            ***********************************************************************************
            
            
            WARNING: Namelist file not specified by user. Using default namelist file path: /home/samm.tempoquest/acecast-v3.0.1/acecast/easter500-4GPU/namelist.input 

            Support Check Configuration:
                Namelist                    : /home/samm.tempoquest/acecast-v3.0.1/acecast/easter500-4GPU/namelist.input
                AceCAST Version             : 3.0.1
                WRF Compatibility Version   : 4.4.2


            NOTE: Namelist options may be determined implicitly if not specified in the given namelist.

            Support Check Tool Success: No unsupported options found -- Ok to use namelist for AceCAST execution.

    .. tab:: output for unsupported namelist

        .. code-block:: output
            
            ***********************************************************************************
            *      ___           _____           _      ___      _       _                    *
            *     / _ \         /  __ \         | |    / _ \    | |     (_)                   *
            *    / /_\ \ ___ ___| /  \/ __ _ ___| |_  / /_\ \ __| |_   ___ ___  ___  ____     *
            *    |  _  |/ __/ _ \ |    / _` / __| __| |  _  |/ _` \ \ / / / __|/ _ \|  __|    *
            *    | | | | (_|  __/ \__/\ (_| \__ \ |_  | | | | (_| |\ V /| \__ \ (_) | |       *
            *    \_| |_/\___\___|\____/\__,_|___/\__| \_| |_/\__,_| \_/ |_|___/\___/|_|       *
            *                                                                                 *
            ***********************************************************************************
            
            
            WARNING: Namelist file not specified by user. Using default namelist file path: /home/samm.tempoquest/acecast-v3.0.1/acecast/easter500-4GPU/namelist.input 

            Support Check Configuration:
                Namelist                    : /home/samm.tempoquest/acecast-v3.0.1/acecast/easter500-4GPU/namelist.input
                AceCAST Version             : 3.0.1
                WRF Compatibility Version   : 4.4.2


            NOTE: Namelist options may be determined implicitly if not specified in the given namelist.

            SUPPORT CHECK FAILURE:
                Unsupported option selected for namelist variable mp_physics in &physics: mp_physics=10
                Supported options for namelist variable mp_physics: 0,1,6,8,28

            SUPPORT CHECK FAILURE:
                Unsupported option selected for namelist variable cu_physics in &physics: cu_physics=16
                Supported options for namelist variable cu_physics: 0,1,2,11

            Support Check Tool Failure: One or more options found that are not supported by AceCAST. Please modify your namelist selections based on the previous "SUPPORT CHECK FAILURE" messages and run this check again.


