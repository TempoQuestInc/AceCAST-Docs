.. meta::
   :description: License Information for AceCast, click for more
   :keywords: License, AceCast, Documentation, TempoQuest, ACECAST_LICENSE, RLM_LICENSE, acecast-license-check, acecast-hostid

.. _Licenselink:

License Information
===================

AceCAST is licensed software. The *acecast.exe* executable checks for a valid license every time it
starts. The other executables in the package (*real.exe*, *ndown.exe*, the WPS programs, etc.) do
not need a license.

AceCAST uses TempoQuest AceCAST licenses. Older releases used Reprise RLM licenses (see
:ref:`legacyrlm`).

.. _licenserequests:

AceCAST License Requests
************************

To request an AceCAST license, use the `Request an AceCAST license <https://tempoquest.com/contact?topic=sales>`__ form, or email support@tempoquest.com.

.. _tqlicense:

Using Your AceCAST License
**************************

Offline and Online Licenses
---------------------------

A TempoQuest AceCAST license is a small text file (typically named `acecast.lic`). There are two
kinds:

* **Offline license.** The license is checked entirely by `acecast.exe` on the machine that runs
  AceCAST. No network access is needed. An offline license can be tied to the machine it will be
  used on (see :ref:`tqhostids`), and each offline license file is accepted only for a limited time
  (see :ref:`tqlicenseterm`).
* **Online license.** The license file identifies your account, and AceCAST obtains a license from
  the TempoQuest license service each time it starts (see :ref:`tqonline`). Online licenses do not
  run without network access.

You use both kinds in the same way, by pointing AceCAST at the license file.

.. _licenseusage:

Using the License
-----------------

The simplest way to use a license is to point the *ACECAST_LICENSE* environment variable at the
file in the job script that launches AceCAST, before the *mpirun* command:

.. code-block:: shell

    export ACECAST_LICENSE=$HOME/licenses/acecast.lic

.. _tqlicensecheck:

Checking the License Before a Job
---------------------------------

The program `acecast-license-check` in `acecast/run`, next to `acecast.exe`, checks the license that
`acecast.exe` would use on this machine, without starting a model run. It looks for the license in
the same places as `acecast.exe` (see below), except that the last place, `acecast.lic` beside the
executable, is beside `acecast-license-check` itself. So keep it in the same directory as
`acecast.exe`, and run it as the same user and in the same environment as the job: with the same
*ACECAST_LICENSE* and *ACECAST_LICENSE_CACHE* settings, from the run directory, and on the machine
(or in the container) that will run MPI rank 0.

.. code-block:: shell

    export ACECAST_LICENSE=$HOME/licenses/acecast.lic
    ~/acecast-v4.7.1/acecast/run/acecast-license-check --ranks 4

Usage:

.. code-block:: output

    usage: acecast-license-check [--ranks N] [--online]
           acecast-license-check --version

* `--ranks N`: the number of MPI ranks your run will use (with one MPI task per GPU, the number of
  GPUs). The license's seats are compared with it (see :ref:`tqseats`). The default is 1.
* `--online`: also check an online license. Without this option the TempoQuest license service is
  not contacted and nothing is written: offline license files are checked as `acecast.exe` checks
  them, but an online license is only reported as present and is **not** checked. With `--online`
  the check is a real run as far as the license service is concerned: a license is checked out and
  checked in again, recorded like a run that ended at once, and billed as such where usage is
  metered.
* `--version`: print the version of the license check. `--help` prints the usage.

In short: for an offline license run `acecast-license-check --ranks N`; for an online license run
`acecast-license-check --ranks N --online`, keeping in mind that this counts as a run.

A license that passes produces output similar to the following:

.. code-block:: output

    Checking the AceCAST license without contacting the license service.
    AceCAST license source: /home/acecast-user/licenses/acecast.lic (from ACECAST_LICENSE); license lic_2026_0001; 4 of 4 seats
    AceCAST license: Example Weather Company, expires 2027-10-31
    License check passed.

If no license is found, the output names every place that was checked:

.. code-block:: output

    Checking the AceCAST license without contacting the license service.
    No AceCAST license found (checked: ACECAST_LICENSE unset; TEMPOQUEST_LICENSE unset; ./acecast.lic not found; /home/acecast-user/acecast-v4.7.1/acecast/run/acecast.lic not found). Set ACECAST_LICENSE to the path of your TempoQuest AceCAST license file (ACECAST-LIC-V1), or contact support@tempoquest.com.
    License check failed.

The exit status can be used in job scripts:

.. list-table::
   :widths: 15 85
   :header-rows: 1

   * - Exit status
     - Meaning
   * - 0
     - licensed (with or without a `WARNING:` line)
   * - 1
     - refused
   * - 2
     - usage error
   * - 3
     - not checked: only an online license could have decided; run again with `--online` to check it

Where AceCAST Looks for a License
---------------------------------

AceCAST looks in the following places, in this order of preference, and uses the first license that
passes every check:

#. The *ACECAST_LICENSE* environment variable
#. The *TEMPOQUEST_LICENSE* environment variable
#. A file named `acecast.lic` in the current working directory (i.e. the simulation run directory)
#. A file named `acecast.lic` in the same directory as the `acecast.exe` executable. If the run
   directory contains a symbolic link to `acecast.exe`, this is the directory of the file the link
   points to.

Emergency license files, which TempoQuest support may issue, are considered only after all standard
offline and online licenses from these places.

Each of the environment variables may be set to any of the following:

* the path of a license file
* the path of a directory (AceCAST reads `acecast.lic` in it first, then at most 32 other files
  whose names end in `.lic`, in name order)
* a colon-separated list of files and/or directories
* the text of the license itself

.. note::
   The value is used as it is given. A leading `~`, surrounding quotes and `$VARIABLE` references
   inside the value are **not** expanded by AceCAST, so let your shell expand them (for example
   use `$HOME/licenses/acecast.lic` unquoted or in double quotes rather than
   ``'~/licenses/acecast.lic'``).

.. _tqonline:

Online Licenses and the License Agent
-------------------------------------

The AceCAST package includes the program `acecast-license-agent` in `acecast/run`, next
to `acecast.exe`. AceCAST uses it to contact the TempoQuest license service for an online license.
Keep it in the same directory as `acecast.exe` (if you copy `acecast.exe` somewhere else, copy
`acecast-license-agent` with it), or set *ACECAST_LICENSE_AGENT* to its path. If no usable agent is
found, an online license cannot be checked, and a job that has only an online license stops with a
message saying so. An offline license does not use the agent.

The license is checked by the first MPI task only, so for an online license the machine that runs
MPI rank 0 needs outbound HTTPS access to the license service. Unless your license names a
different server, these are:

* `license.wrfondemand.com`
* `license-dr.wrfondemand.com` (used if the first cannot be reached)

AceCAST contacts the license service when the run starts. When the run ends it records the end of
the run and, when the license requires it, tries to report it to the license service; a report that
could not be sent is retried later.

AceCAST keeps license state in `$HOME/.acecast/license` (see the table below to use a different
directory). Keep this directory persistent and writable by the user that runs the jobs.

If your site reaches the internet through a proxy, AceCAST passes the standard proxy variables
(*HTTPS_PROXY*, *NO_PROXY* and the other usual proxy variables, upper or lower case) to the license
agent, so set them in the job environment. The agent trusts the system's CA certificates; if your
site's network needs another CA certificate, set *ACECAST_CA_BUNDLE* to the path of a PEM file that
contains it, and the agent trusts the certificates in that file as well. If that file cannot be read
or holds no PEM certificate, the agent cannot contact the license service.

An online license that is tied to a machine needs the machine ID to be readable and the GPU UUIDs to
be visible through the NVIDIA driver (NVML) on the machine that runs MPI rank 0. This applies on any
machine, not only in containers; other online licenses do not always need it. If AceCAST runs in a
container, see :ref:`tqhostids` for how to make both visible there.

.. _tqseats:

Seats
-----

A standard offline license file may state a number of seats: the most MPI compute ranks that one
run may use. With one MPI task per GPU, as recommended, this is the number of GPUs in one run. When
such a license has seats, the `AceCAST license source:` line shows how many are used, for example
`4 of 4 seats`. A run with more ranks than the license allows stops with a message such as:

.. code-block:: output

    /home/acecast-user/licenses/acecast.lic (from ACECAST_LICENSE): this run uses 8 MPI ranks but license lic_2026_0001 allows 4 (seats). Run with at most 4 ranks, or contact support@tempoquest.com for more seats.

The seats of an online floating license are shared by the runs that use the license at the same
time. For an online license the `AceCAST license source:` line reports the number of GPUs the run
uses instead (see :ref:`tqlicensesuccess`).

Environment Variables
---------------------

.. list-table::
   :widths: 35 65
   :header-rows: 1

   * - Variable
     - Purpose
   * - *ACECAST_LICENSE*
     - The license to use: a file, a directory, a colon-separated list of these, or the license text.
   * - *TEMPOQUEST_LICENSE*
     - The second license source, used when no license from *ACECAST_LICENSE* is accepted; same
       forms.
   * - *ACECAST_LICENSE_AGENT*
     - Path of `acecast-license-agent`, if it is not in the same directory as `acecast.exe`.
   * - *ACECAST_LICENSE_CACHE*
     - Directory for license state (default `$HOME/.acecast/license`). Keep it persistent and
       writable.
   * - *ACECAST_LICENSE_TIMEOUT*
     - Time in seconds allowed for each request to the license service, shared between the two
       servers (default 45). Values below 5 or above 300 are treated as 5 or 300; a value that is
       not a whole number is ignored and the default is used.
   * - *ACECAST_LICENSE_WAIT*
     - For an online license whose seats are all in use: how many seconds AceCAST waits for seats,
       for each license server (default 120). The whole license check can take longer than this.
       Values below 0 or above 3600 are treated as 0 or 3600; a value that is not a whole number is
       ignored and the default is used.
   * - *ACECAST_CA_BUNDLE*
     - Optional: path of a PEM file of CA certificates that the license agent trusts in addition to
       the system's CA certificates.

.. _tqlicensesuccess:

Successful License Check
------------------------

A successful license check produces output similar to the following near the top of the
`rsl.out.0000` and `rsl.error.0000` files:

.. code-block:: output

    Checking out AceCAST Licenses.
    AceCAST license source: /home/acecast-user/licenses/acecast.lic (from ACECAST_LICENSE); license lic_2026_0001
    AceCAST license: Example Weather Company, expires 2027-10-31
    Successfully checked out licenses.

The expiry date of a standard offline license file is the last day it is valid: the license runs to
the end of that day, UTC.

For an online license the `AceCAST license source:` line also says `online license service`, the kind
of online license and the number of GPUs the run uses, for example:

.. code-block:: output

    AceCAST license source: /home/acecast-user/licenses/acecast.lic (from ACECAST_LICENSE); online license service; license lic_2026_0002 (floating); 4 GPU(s)

A `WARNING:` line is added when the license expires within 30 days, or when it has already expired
and is running within its grace period. If you see this warning, contact support@tempoquest.com to
renew before the license stops working.

Failed License Check
--------------------

If no valid license is found, AceCAST prints a single line that explains the reason and then stops:

.. code-block:: output

    Checking out AceCAST Licenses.
    /home/acecast-user/licenses/acecast.lic (from ACECAST_LICENSE): AceCAST license lic_2026_0001 expired on 2026-09-30 and its 14-day grace period ended on 2026-10-14. Contact support@tempoquest.com to renew.
    -------------- FATAL CALLED ---------------
    ...
    License checkout failed. Exiting.

Common reasons include:

* **No license found**: none of the locations listed above holds a license. The message lists every
  place that was checked. Make sure *ACECAST_LICENSE* is exported in the same shell or job script
  that launches *mpirun*.
* **File not found or not readable**: check the path for typos, a leading `~`, or quotes that were
  not expanded by the shell.
* **Reprise RLM license**: the file is a Reprise RLM license. AceCAST needs a TempoQuest AceCAST
  license (see :ref:`legacyrlm`).
* **Expired license**: the license and its grace period have ended.
* **Different machine**: the license is tied to host identifiers that do not match this machine.
  Run `acecast-hostid` on this machine and send its output to support@tempoquest.com (see
  :ref:`tqhostids`). If the identifiers could not be read at all (for example the GPUs are not
  visible inside a container), the message says so.
* **Too many MPI ranks**: the run uses more MPI ranks than the license has seats (see
  :ref:`tqseats`).
* **License term too long**: the license file is valid for longer than AceCAST accepts (see
  :ref:`tqlicenseterm`).
* **System clock**: the machine's date is more than a day earlier than the date the license was
  issued.
* **License service not reached** (online licenses): the machine that runs MPI rank 0 could not
  reach the license service, or `acecast-license-agent` is missing (see :ref:`tqonline`).

For the checks it performs, `acecast-license-check` shows the same message without starting a run
(see :ref:`tqlicensecheck`; an online license is checked only with `--online`). If the message does
not make the problem clear, send the complete line to support@tempoquest.com.

.. _tqhostids:

Licenses Tied to a Machine
--------------------------

An offline license can be tied to the machines it will be used on. To request one, run
`acecast-hostid` (in `acecast/run`, next to `acecast.exe`) on each machine that will run AceCAST,
and send the whole output to support@tempoquest.com. The license is checked on the machine that runs
MPI rank 0, so in a cluster include every node that may run MPI rank 0 of a job.

.. code-block:: shell

    ~/acecast-v4.7.1/acecast/run/acecast-hostid

Run it without options. The output lists the GPU UUIDs (`gpu:` lines) and the machine ID (`mid:`
line) of the machine, for example:

.. code-block:: output

    == AceCAST host ids ==
    gpu:GPU-12345678-90ab-cdef-1234-567890abcdef
    mid:0123456789abcdef0123456789abcdef
    # hostname: gpu-node01 (for reference only; not a license host id)

Lines that start with `#` are notes, for example when no GPU or no machine ID could be found. Read
the output before you send it: `acecast-hostid` exits with status 0 even when it found no
identifiers, so check that it printed at least one `gpu:` or `mid:` line. A license that lists
several identifiers is valid on a machine that has any one of them.

If AceCAST will run inside a container, run `acecast-hostid` inside the container, in the same way
AceCAST will run:

* Licenses tied to GPUs need the GPUs to be visible inside the container, so always pass the
  *--gpus* option when running `acecast-hostid` and `acecast.exe` with Docker.
* Inside a container the machine ID may be missing, or may belong to the container image rather
  than the host. Mount the host's */etc/machine-id* read-only into the container, for example with
  Docker:

  .. code-block:: shell

      docker run --gpus all -v /etc/machine-id:/etc/machine-id:ro ...

  The same applies to an online license that is tied to a machine.
* The license file must be readable inside the container, and *ACECAST_LICENSE* must give its path
  as seen inside the container.

.. _tqlicenseterm:

How Long an Offline License File Is Accepted
--------------------------------------------

AceCAST accepts a standard offline license file only for a limited time. The time is counted in days
from the date the license was issued to its expiry date, plus its grace period, and AceCAST refuses a
license file whose total is larger than:

.. list-table::
   :widths: 60 40
   :header-rows: 1

   * - The license file is
     - Accepted for at most
   * - tied to at least one machine (see :ref:`tqhostids`)
     - 460 days from its issue date, grace period included
   * - not tied to a machine (valid on any machine, also when it names machines as well)
     - 14 days from its issue date, grace period included

What this means for you:

* A license file tied to your machines can be valid for at most 460 days, grace period included.
  If your contract runs longer, you will need a new license file before the current one reaches its
  limit; contact support@tempoquest.com. Install each new file before the old one expires (see
  :ref:`tqrenewal`).
* A license file that is not tied to a machine, for example for cloud machines that change from run
  to run, must be replaced every 14 days. For such a setup ask TempoQuest for a license tied to your
  machines or for an online license. Because its whole term is shorter than 30 days, such a file
  always produces the expiry `WARNING:` line.
* The limit depends only on the license file, not on today's date. A file issued for a longer term
  is refused from its first day, with a message such as one of these:

.. code-block:: output

    /home/acecast-user/licenses/acecast.lic (from ACECAST_LICENSE): license lic_2026_0001 is valid for 800 days from its issue date, grace included; this AceCAST build accepts a license file for at most 460 days. Contact support@tempoquest.com for a license file with a shorter term.

.. code-block:: output

    /home/acecast-user/licenses/acecast.lic (from ACECAST_LICENSE): license lic_2026_0001 is bound to no machine and is valid for 379 days from its issue date, grace included; this AceCAST build accepts a license file bound to no machine for at most 14 days. Contact support@tempoquest.com for a license bound to this machine (send the output of acecast-hostid) or an online license.

These limits apply to standard offline license files only, not to online licenses. Emergency license
files, which TempoQuest support may issue, have their own rules and are not covered by this section.

.. _tqrenewal:

Renewing a License
------------------

When you receive a renewed license, replace the old license file with the new one, and do so before
the old license expires. (A standard offline license file may hold several licenses, up to 64 in a
file of at most 1 MiB, and AceCAST uses the first valid one, but replacing the old file is simpler
than appending renewals to it.)

.. _legacyrlm:

Older Releases (Reprise RLM)
****************************

AceCAST 4.7.0 and earlier releases, the earlier public download of AceCAST 4.7.1, and the
container images on DockerHub used Reprise RLM licenses. Reprise RLM licensing is discontinued; to move to a TempoQuest license, use the
`Request an AceCAST license <https://tempoquest.com/contact?topic=sales>`__ form.

.. _licensemigration:

Moving from an Older Reprise RLM Release
----------------------------------------

#. Request a TempoQuest AceCAST license and the AceCAST package with the `Request an AceCAST license <https://tempoquest.com/contact?topic=sales>`__
   form. Your Reprise RLM license file cannot be used. For a license tied to your machines, send the
   output of `acecast-hostid` from each machine (see :ref:`tqhostids`).
#. Unpack the package. If you already have an `acecast-v4.7.1/` directory from an earlier download,
   unpack the new package in a different location, or move the old directory first.
#. Keep `acecast-license-agent` in the same directory as `acecast.exe`, or set
   *ACECAST_LICENSE_AGENT* to its path (see :ref:`tqonline`).
#. Replace `export RLM_LICENSE=...` in your job scripts, environment modules and container run
   scripts with `export ACECAST_LICENSE=/path/to/acecast.lic`. *RLM_LICENSE* is no longer read.
#. If you have an online license, make sure the machine that runs MPI rank 0 can reach the license
   service (see :ref:`tqonline`).
#. Check the license with `acecast-license-check --ranks N`, where N is the number of MPI ranks
   your runs use. For an online license add `--online`; this counts as a run with the license
   service (see :ref:`tqlicensecheck`).
#. Run AceCAST and check `rsl.out.0000` for the `AceCAST license source:` line and the
   *Successfully checked out licenses.* message described in :ref:`tqlicensesuccess`.
