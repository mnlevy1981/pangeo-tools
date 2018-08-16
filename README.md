## `launch-pangeo-on-cheyenne`

This script should be run on your local machine.
It will ssh to cheyenne and submit a job to the share queue to launch jupyter lab.
Once the job starts running, it will determine the name of the compute node running the job.
It will then ssh back to cheyenne to create a tunnel forwarding the jupyter port (probably 8888).
At that point, you should be able to go to [http://localhost:8888](http://localhost:8888) and enter your jupyter token.
(On MacOS, the website will open automatically).

### Assumptions

1. You must have installed conda on cheyenne, and have a conda environment that contains `jupyterlab`.
See [the tutorial](https://github.com/NCAR/pangeo-tutorial-august-2018/blob/master/setup.md#setup-python-for-working-on-a-personal-computer-or-on-cheyenne) for instructions on setting up conda on cheyenne.
By default, `launch-pangeo-on-cheyenne` thinks this environment is named `pangeo-cheyenne`.
You can install the recommended environment (with this name) by running
```
        $ conda env create -f pangeo-cheyenne.yaml
```
Alternatively, if you already have a conda environment you wish to use, specify it with the `-e` option.

1. Required packages:

   * `dask`
   * `xarray`
   * `jupyterlab`
   * `dask-jobqueue=0.3.0`

1. Recommended packages for plotting:

   * `matplotlib`
   * `Cartopy`

1. The current implementation requires you to open up a new tunnel to the `dask` port once you have started the dashboard.
```
        $ ssh -N -l [USERNAME] -L 8787:[COMPUTE NOTE]:8787 cheyenne.ucar.edu
```
8787 is the default port, but if it is in use by another user then a different one will be presented.
There is a package named `nbserverproxy` that can be used to forward this port through 8888 (the jupyter port),
but it doesn't work with the current `dask-jobqueue` release.
This repository will be updated once `dask-jobqueue` has been updated.

1. `jupyterlab` will launch from `/glade/work/$USER`

### Options

1. If you get disconnected and need to re-establish the ssh tunnel, use the `-H REMHOST` option
(where `REMHOST` is the name of the compute node you are running).
The host name should be printed as part of the output of the script the first time you run it:
```
Job host:  r8i7n9
```
Or can be found with
```
$ qstat -xf JOBID | grep exec_host
    exec_host = r8i7n9/1
```