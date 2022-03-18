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

   * A: This varies, please get in touch with :ref:`sales <supportlink>` for specific details.


Runtime
-------

#. Q: I downloaded AceCast, now what?

   * A: Check out the :ref:`installation page <Installation>` to get started!

#. Q: What are the system requirements?

   * A: Refer to :ref:`System Requirements <requirementslink>`

#. Q: Do I need any other software to run AceCast?

   * A: There are some dependecies required. Please refer to :ref:`Installation`

#. Q: Why is AceCAST running slower than WRF?

   * A: One reason could be, if you are using an older version of AceCAST you may see both a wrf.exe and an acecast.exe. Although both work, only acecast.exe is optimized for speed, wrf.exe was previously included only for user convenience.

Technical
---------

#. Q: My output is all NaN's, help!

   * A: Check your rsl.error log, if you see CFL errors then decrease your time step in your namelist.input file (~5-6x dx)

#. Q: Why do I get an error when I run install_deps.sh?

   * A: Your system may not be compatible/properly setup, please :ref:`get in touch with us <supportlink>` for help.

#. Q: My license has expired what do I do?

   * A: Send us an :ref:`email, we would be happy to renew it for you! <supportlink>`.


Other
-----
#. Q: What are the recommended settings to use?

   * A: This varies but generally you can check out these links for WRF `namelist.wps <https://www2.mmm.ucar.edu/wrf/users/namelist_best_prac_wps.html>`_
     and `namelist.input <https://www2.mmm.ucar.edu/wrf/users/namelist_best_prac_wrf.html>`_ best practices.




#. Q: Where can I learn more?

   * A: Read through through the different tabs on the side of this page and then :ref:`get in touch with us <supportlink>`


