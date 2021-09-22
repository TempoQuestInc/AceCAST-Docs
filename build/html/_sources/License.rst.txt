.. _Licenselink:

License Information
===================

Under Construction
------------------

* After `registering to use AceCAST <https://tempoquest.com/acecast-registration/>`_ , you should have received an email with information on downloading AceCAST as well as a license file (ex. acecast-trial.lic). This file will be checked by the acecast.exe executable at runtime to ensure the user has a valid license.
* To ensure that AceCAST can evaluate your license at runtime there are *two* options:
	#. Copy the license file to the run directory you are executing acecast.exe in
	#. Point the RLM_LICENSE environment variable to the location of the license file::

			$ export RLM_LICENSE=$HOME/AceCAST/run/acecast-trial.lic

* When running acecast, a successful license checkout should generate output similar to this in the rsl.out.0000 and rsl.error.0000 files::

		Checking out AceCAST Licenses.
		RLM license checkout:
		------------------------
		Product: acecast
		Version: 1
		Count:   4
		------------------------
		Successfully checked out licenses.
