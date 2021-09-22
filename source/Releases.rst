.. _releaseslink:

What's New
==========

Version 1.2
-----------
* Nesting
	* This release includes a large number of new and improved features, the primary of which is nesting. Both 1-way and 2-way nesting
          is now fully supported with the only notable exceptions being the inability to use vertical nesting and restricting the user to
          using interp_method_type=2 (sint). If either of these are required for your simulations please contact support@tempoquest.com
          to ensure that we prioritize their development for future versions.


* Physics Additions
	* cu_physics = 1, 11 (Kain-Fritsch and Multi-scale Kain-Fritsch cumulus schemes)
	* cu_rad_feedback = .true. (Sub-grid cloud effect to the optical depth in radiation)
	* kf_edrates = 1 (Add entrainment/detrainment rates and convective timescale output variables)
	* kfeta_trigger = 1, 2, 3 (KF trigger option; cu_physics=1 only)
	* lightning_option = 3 (Lightning parameterization; predicting the potential for lightning activity)
	* iccg_method = 2 (Coarsely prescribed intra-cloud and cloud-to-ground partitioning method)
	* use_mp_re = 1 (see bugfix)
	* scalar_pblmix = 1 (Mix scalar fields consistent with PBL option)
	* grav_settling = 1, 2 (Gravitational settling of fog/cloud droplets)
	* topo_shading = 1 (Neighboring-point shadow effects for solar radiation)
	* slope_rad = 1 (Slope effects for solar radiation)
	* swint_opt = 1 (Interpolation of short-wave radiation based on the updated solar zenith angle between SW call)
	* o3input = 2 (ozone input option for radiation, using CAM ozone data; ozone.formatted)
	* sf_sfclay_physics = 5 (MYNN surface layer scheme)
	* icloud = 2, 3 (cloud effect to the optical depth in radiation)
    

* Dynamics Additions
	* scalar_adv_opt = 2, 3, 4 (advection options for scalar variables)
	* h_sca_adv_order = 6 (6th order horizontal scalar advection) 
	* h_mom_adv_order = 6 (6th order horizontal momentum advection) 


* Performance improvements
	* The initialization overhead time has been an issue for users with short simulations. We have significantly improved the
          allocation and physics initialization routines (over 10X faster in many cases) to ensure this overhead is nearly negligible 
          when compared to the total runtime for any simulation, regardless of the simulation length.

	* We have addressed an issue where the Kessler scheme (mp_physics = 1) was significantly slower than it should have been. We
          are now seeing up to 30X speedup for this component and users should be able to confidently use this option.

	* We have significantly optimized the performance of the Noah land-surface scheme (sf_surface_physics = 2), which should 
          give users approximately a 5-10% overall speedup for simulations using this scheme.

	* We have made some universal memory optimizations that have shown up to 10% overall runtime speedups in some cases.


* Improvements over the base WRF-CPU implementation
	* There is a bug in the base WRF code (https://github.com/wrf-model/WRF) in all previous releases (currently version 4.2.2) 
          that caused issues when using multi-scale KF (cu_physics=11) on outer nests but not the inner nests (example for 2-domain 
          simulation: cu_physics = 11, 0). This is a common configuration for nested runs since the inner nests may run at 
          convection-resolving resolutions but the coarse domains require a cumulus scheme. This caused the model to produce 
          incorrect results. A bug report has been submitted to the WRF developers but this issue has been resolved in the AceCAST 
          v1.2 release and is currently safe for users running such configurations.

	* The base WRF v3.8.1 code had issues with lightning_option = 3 causing crashes at runtime. This issue has been resolved
          in AceCAST.


* Bug Fixes
	* Fixed issue with adaptive time stepping where the CFL condition was not calculated correctly causing longer time steps
          that would cause stability issues.
	* Fixed issue where effective radii computed in mp schemes were incorrectly modified by RRTMG.


* AceCAST Advisor Tool
	* We have modified both the support-check and scaling-advisor tools to ensure they account for nested runs and 
          implicitly-defined options.


* Feature Development Targets for Version 1.3
	* Release v1.3 will incorporate a variety of new features. Our development targets are prioritized by user requests. Please 
          contact support@tempoquest.com if you have any requests for new features. Currently we intend on implementing the following 
          options.

		* Observational Nudging (&fdda namelist options)
		* Fractional Seaice (fractional_seaice = 1 and associated suboptions)
		* Stochastic Parameterization Schemes (&stoch namelist options)

	* Although I/O Quilting is supported in AceCAST to the extent that it is also supported in WRF, there are significant memory
          limitations that cause the I/O server processes to fail at runtime quite frequently in both AceCAST and WRF. I/O Quilting 
          could have significant benefits for GPU execution with AceCAST if we could make the implementation more reliable. We are
          currently exploring this opportunity for version 1.3 or later.


Version 1.1.2
-------------
* Release 1.1.2 adds beta support for IBM Power9 systems on Linux.
  This Power9 version is intended for research use only.
  TQI acknowledges computational resources of the Oak Ridge Leadership Computing Facility at the Oak Ridge National Laboratory, 
  which is supported by the Office of Science of the U.S. Department of Energy under Contract No. DE-AC05-00OR22725. 


Version 1.1.1
-------------
* This release does not incorporate any new features. This release incorporates changes necessary to enable counting, floating 
  licenses. We have also cleaned up much of the output from the license checkout/checkin tasks.

Version 1.1
-----------
* AceCAST has been compiled and tested with NVIDIA HPC SDK (20.7) and CUDA 11. This version has support for A100 architecture GPUs. 


* Physics Additions
	* Thompson (mp_physics = 8) & Thompson aerosol-aware microphysics (mp_physics = 28)
	* MYNN surface layer (sf_sfclay_physics = 5)
	* MYNN 3rd level TKE scheme (bl_pbl_physics = 6)
	* RUC land-surface model (sf_surface_physics = 3)

* Dynamics Additions
	* do_avgflx_em = 1 (Output time-averaged mass-coupled advective velocities)
	* momentum_adv_opt = 3 (5th-order WENO) advection option
	* moist_adv_opt = 2,3,4 advection options

* Miscellaneous
	* Support for adaptive time stepping 

		* diag_print = 1 (printing out time series of basic model diagnostics)
		* Performance optimizations for WSM6 (mp_physics = 6), YSU PBL (bl_pbl_physics = 1), and BMJ (cu_physics = 2) schemes

Version 1.0.1
-------------
* Diagnostics
	* We have ported a significant selection of diagnostics options. The following options are now available to AceCAST.
        
	* &time_control:
		* nwp_diagnostics = 1
		* output_diagnostics = 1
	* &afwa
		* afwa_diag_opt = 1
		* afwa_ptype_opt = 1
		* afwa_vil_opt = 1
		* afwa_radar_opt = 1
		* afwa_severe_opt = 1
		* afwa_icing_opt = 1
		* afwa_vis_opt = 1
		* afwa_cloud_opt = 1
		* afwa_therm_opt = 1
		* afwa_buoy_opt = 1
		* afwa_bad_data_check = 1

	* &diags
		* p_lev_diags = 1
		* z_lev_diags = 1
		* ! all associated suboptions

.. admonition:: Note

    The following afwa options are still not available in this version:
      #. afwa_turb_opt = 1
      #. afwa_hailcast_opt = 1

    Please contact support@tempoquest.com if you would like us to consider supporting any other specific diagnostics options in future versions.

* Physics
	* We have added support for the following physics options:
		* Mellor-Yamada-Janjic TKE scheme (bl_pbl_physics = 2)
		* Monin-Obukhov (Janjic) scheme (sf_sfclay_physics = 2)



Version 1.0
-----------
* Testing
	* AceCAST v1.0 has been thoroughly tested at all stages of model development and ready for user evaluation. We 
  	  rigorously evaluated 12 main physics and majority of dynamics options for numerical and performance aspects using 
  	  numerous coarse and mesoscale simulations.  Additionally, scaling, domain size, boundary, resolution, integration 
  	  order, and IO sensitivity experiments have been performed to provide a robust high-performance NWP model.


* Updates
	* Licensing Changes
		* We have moved from providing a generic trial license within the distribution package itself to providing 
     	  	  individual trial licenses for each user. The trial licenses will be sent to the user via email after registering 
          	  at https://tempoquest.com/acecast-registration/. The trial license will be valid for 60 days beginning the 
          	  day of registration.

    
	* Dependency installation script improvements
		* Added secondary dependency installation functionality for RPM-based and Debian-based Linux distributions using
                  the yum and apt-get package managers. Although this isn't necessary for most users where these secondary deps
          	  are already installed, this may be useful on systems that do not have these packages installed. Note that using
          	  this option requires sudo access.

        	* Usage for RPM-based Linux Distributions:      ./install_deps.sh --install-secondary-packages-rpm
        	* Usage for Debian-based Linux Distributions:   ./install_deps.sh --install-secondary-packages-deb

        	* Added checks to ensure each installation step succeeded before moving on to the next. Issues with the dependency
         	  installation script will now be much clearer and easier to identify.

		* Improved namelist configuration checks
    
		* Extended configuration support checks to ensure a valid set of options is chosen at runtime.


	* AceCAST advisor script
		* Added a namelist checking utility (run/acecast-advisor.sh), which advises the user how to change a namelist based
          	  on what options are supported by AceCAST as well as the number of GPUs one should use when running the given 
          	  namelist.


	* Performance Optimizations
		* Gravity Wave Drag (gwd_opt = 1) - Our initial implementation of the gwd option was rather slow due to a lack of 
          	  parallelism. This scheme has been reimplemented to exploit the available parallelism and is no longer a 
          	  significant performance bottleneck.

		* RRTMG Longwave Radiation (ra_lw_physics = 4) - The memory overhead of the RRTMG-LW scheme has been significantly
          	  reduced, which has reduced allocation times and improved computational performance as well.

		* MYNN PBL (bl_pbl_physics = 5) - The MYNN PBL scheme has been reworked to exploit more parallelism.

Version 1.0-beta
----------------

* Initial public release of AceCAST
    
* Supported Platforms
	* This release of AceCAST has a single generic distribution targeting x86-64 Linux systems. Support is not guaranteed 
  	  for any particular Linux distribution but this release has been tested successfully on a variety of distributions 
  	  when using the recommended installation methods (see README.ACECAST). This distribution has been built for NVIDIA 
  	  GPU compute capabilities 3.5, 6.0 and 7.0. We extensively tested the model on Intel (Haswell and Skylake) CPUs with 
  	  NVIDIA V100 GPUs on a CentOS Linux version 7 platform.
