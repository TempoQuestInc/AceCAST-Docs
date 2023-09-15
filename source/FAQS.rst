.. meta::
   :description: FAQ's for AceCast, click for more
   :keywords: Questions, FAQ, Technical, AceCast, Documentation, TempoQuest


Frequenty Asked Questions
=========================

General
-------
#. Q: How much does AceCast Cost? 

   * A: Please get in touch with :ref:`sales <supportlink>`.

#. Q: What is AceCAST?
    
   * A: AceCAST is like `WRF <https://www.mmm.ucar.edu/weather-research-and-forecasting-model>`_ but with GPU's instead of CPU's. This means that you can have higher quality results in a faster time!

#. Q: What kind of runtime improvements can I expect to see?

   * A: Typically we have seen cost savings of 2-3x cheaper and runtime savings averaging x10 faster than CPU-equivalent simulations, however the exact value varies, please get in touch with :ref:`sales <supportlink>` for further details.


Runtime
-------

#. Q: I downloaded AceCast, now what?

   * A: Check out the :ref:`installationguide` to get started!

#. Q: What are the system requirements?

   * A: Refer to :ref:`requirementslink`

#. Q: Do I need any other software to run AceCast?

   * A: There are some dependecies required. Please refer to :ref:`installationguide`

#. Q: Why is AceCAST running slower than WRF?

   * A: One reason could be, if you are using an older version of AceCAST you may see both a wrf.exe and an acecast.exe. Although both work, only acecast.exe is optimized for speed, wrf.exe was previously included only for user convenience.

#. Q: What kind of input is needed to run AceCAST?

   * A: AceCAST is WRF but on GPU's, so any `compatible input <https://forum.mmm.ucar.edu/threads/what-fields-are-mandatory-for-running-wrf.5381/>`_ to WRF should also work for AceCAST.

Technical
---------

#. Q: My output is all NaN's, help!

   * A: Check your rsl.error log, if you see CFL errors then decrease your time step in your namelist.input file (~5-6x dx)

#. Q: Why do I get an error when I run install_deps.sh?

   * A: Your system may not be compatible/properly setup, please :ref:`get in touch with us <supportlink>` for help.

#. Q: My license has expired what do I do?

   * A: Send us an :ref:`email, we would be happy to renew it for you! <supportlink>`

#. Q: WRF has many "submodels" which one's are supported by AceCAST?

   * A: Currently, TempoQuest only supports WRF-ARW, however we have plans to port WRF-DA and WRF-Chem to GPU's in the future. If you are interested in this please reach out to us and we can prioritize the porting.

#. Q: What hardware do I need to run AceCAST?

   * A: Since AceCAST is a GPU application, GPU's are needed. Theoretically any modern GPU would be compatible but we have found the best results and compatibility with "modern" HPC GPU's such as NVIDIA P100, V100, and A100 types.

#. Q: Can I run AceCAST in the cloud?

   * A: Yes! `See our WRFOnDemand page <https://wrfondemand.com/>`_ to get started

#. Q: What namelist options are supported by AceCAST?

   * A: WRF (AceCAST) has a large array of options many of which are only used by a small percentage of users, thus we only support the major/most commonly used options. See the :ref:`nmlsupporttbl` to get a sense of which one's those are. If there is an option you are looking for that we do not yet support, we can port it for you. 

Other
-----
#. Q: What are the recommended settings to use?

   * A: This varies but generally you can check out these links for WRF `namelist.wps <https://www2.mmm.ucar.edu/wrf/users/namelist_best_prac_wps.html>`_
     and `namelist.input <https://www2.mmm.ucar.edu/wrf/users/namelist_best_prac_wrf.html>`_ best practices.

#. Q: Where can I learn more?

   * A: Read through through the different tabs on the side of this page and then :ref:`get in touch with us <supportlink>`


