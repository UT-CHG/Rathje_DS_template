# ADCIRC Use Case - Using Tapis and Pylauncher for Ensemble Modeling in DesignSafe 

**Clint Dawson, University of Texas at Austin**  
**Carlos del-Castillo-Negrete, University of Texas at Austin**  
**Benjamin Pachev, University of Texas at Austin**  

The following use case presents an example of how to leverage the Tapis API to run an ensemble of HPC simulations. The specific workflow to be presented consists of running ADCIRC, a storm-surge modeling application available on DesignSafe, using the parametric job launcher pylauncher. All code and examples presented are meant to be be executed from a Jupyter Notebook on the DesignSafe platform and using a DesignSafe account to make Tapis API calls. Accompanying notebooks for this use case can be found in the ADCIRC folder in [Community Data](https://www.designsafe-ci.org/data/browser/public/designsafe.storage.community/Use%20Case%20Products).

Learn more: [Jupyter notebooks on DS Juypterhub](https://www.designsafe-ci.org/rw/workspace/#!/Jupyter::Analysis).

## Background 

### Citation and Licensing

* Please cite [Dawson et al. (2021)](https://doi.org/10.17603/ds2-68a9-0s64) if using any of the Texas FEMA Hurricane data used in the examples below. 

* Please cite [Rathje et al. (2017)](https://doi.org/10.1061/(ASCE)NH.1527-6996.0000246) to acknowledge the use of DesignSafe resources.  

* This software is distributed under the GNU General Public License (https://www.gnu.org/licenses/gpl-3.0.html).  

### ADCIRC

For more information on running ADCIRC and documentation, see the following links:

* [ADCIRC Wiki](https://wiki.adcirc.org/wiki/Main_Page)
* [ADCIRC Web Page](https://adcirc.org/)

ADCIRC is available as a standalone app accesible via the DesignSafe front-end at ()

ADCIRC files used in this demo are pre-staged on TACC resources that DesignSafe execution systems have access to, at the path `/work/06307/clos21/pub/adcirc`. See the section on using data from Projects directory for using other data sources.

### Tapis

Tapis is the main API to control and access HPC resources with. For more resources and tutorials on how to use Tapis, see the following:

- [Tapis CLI](https://tapis-cli.readthedocs.io/en/latest/contents.html)
* [AgavePy](https://tacc-cloud.readthedocs.io/projects/agave/en/latest/)
* [DesignSafe Webinar](https://www.youtube.com/watch?v=-_1lNWW8CAg&t=1854s&ab_channel=NHERIDesignSafe-CIMedia)

To initialize tapis in our jupyter notebook we use AgavePy. Relies on `tapis auth init --interactive` being run from CLI first.

```python
from agavepy.agave import Agave

ag = Agave.restore()
```

### Pylauncher

Pylauncher is a parametric job launcher used for launching a collection of HPC jobs within one HPC job. By specifying a list of jobs to execute in either a CSV or json file, pylauncher manages resources on a given HPC job to execute all the jobs using the given nodes. Inputs for pylauncher look something like (for csv files, per line):

```
num_processes,<pre process command>;<main parallel command>;<post process command>
```

The pre-process and post-process commands are executed in serial, while the main command is executed in parallel using the appropriate number of processes. Note pre and post process commands should do light file management and movement and no heavy lifting/operations.

## Tapis Pylauncher App

The pylauncher app is the main application we will be using to run ensemble simulations. It serves as a wrapper around the [TACC pylauncher](https://github.com/TACC/pylauncher) utility. 

Email cdelcastillo21@gmail.com for access to the application, or check out the application from the github page - https://github.com/UT-CHG/tapis-pylauncher and deploy it using either the Tapis CLI or AgavePy (See documentation links above under Tapis section).

Overview of this section:

* Tapis Configuration
* Submitting and monitoring
* Getting and visualizing output

### Basic Inputs Overview - Generator Script

Inputs for a pylauncher ensemble run will consist of a zipped file full of the necessary scripts to run configure the pylauncher ensemble run. 

* generator.sh - 


### Staging Files 

Note that for large scale ensemble s


### Simple ADCIRC run using Tapis

This section will contain info about how to run a simple ADCIRC run using the pylauncher app. This example has an accompanying notebook in the [ADCIRC Use Case folder](https://www.designsafe-ci.org/data/browser/public/designsafe.storage.community/Use%20Case%20Products/ADCIRC) in the Community Data directory, called ADCIRC-Simple

We will run ADCIRC on the [Shinnecock Inlet Test Grid](https://adcirc.org/home/documentation/example-problems/shinnecock-inlet-ny-with-tidal-forcing-example/).

![caption](img/si_mesh.png)
> Shinnecock Inlet Test Grid. ADCIRC solves the Shallow Water Equations over a Triangular Mesh, depicted above. On the right we see one of the stations, station 2, we will be analyzing output for.

#### Staging Inputs

Input directory contains the following files for running

* setup.sh - Setup script to run before running any ensemble jobs. Sets up runs and logs directory, which is redundant in this case since we are only running a singular run.
* generator.sh - Generator entry point script. Calls the python function.
* generator.py - Python generator function with a basic generator for configuring an ensemble of ADCIRC runs.
* post_process.sh - Script to run per-job to set-up each ADCIRC run using adcprep.
* pre_process.sh - Script to run per-job after each ADCIRC run to move outputs and logs to appropriate directories and cleanup.

The generator script for our case doe

Note we have to first zip up the containing directory since the application expects a zipped input:

```python
adcirc_job_dir = job_configs / 'adcirc' 
input_zip = job_configs / 'adcirc.zip'
input_zip.unlink(missing_ok=True)
os.system(f"zip -r {str(input_zip)} {str(adcirc_job_dir)}")
input_zip
```


#### Configuring and Submitting Job

The python job configuration looks like:

```python
base_dir = '/work2/06307/clos21/pub/adcirc/inputs/ShinnecockInlet/nodal/GD-WindMult_WindJan2018_CFSv2_12'
runs_dir = base_dir
execs_dir = '/work2/06307/clos21/pub/adcirc/execs/stampede2/v55_nodal_beta'
cores_per_job = 8
write_proc_per_job = 0
generator_args = f"{base_dir}  {execs_dir}"
generator_args += f" --cores_per_job {cores_per_job} --write_proc_per_job {write_proc_per_job}"


adcirc_config = {}
adcirc_config['name'] = 'adcirc_simple'
adcirc_config['appId'] =  'pylauncher-test-0.0.1'
adcirc_config['nodeCount'] = 1
adcirc_config['processorsPerNode'] =  10
adcirc_config['memoryPerNode'] = '1'
adcirc_config['maxRunTime'] = '00:30:00'
adcirc_config['archive'] = True
adcirc_config['archiveOnAppError'] = True
adcirc_config['inputs'] = {'job_inputs': configs_uri + '/adcirc.zip'}
adcirc_config['parameters'] = {'pylauncher_input': 'jobs_list.json',
                               'generator_args': generator_args}


job = ag.jobs.submit(body=adcirc_config)
```

#### Monitoring Job 

We can get our jobs status by using the `getStatus` command. Note we must wait for it to reach a `FINISHED` state, after archiving, to analyze outputs.

```python
ag.jobs.getStatus(jobId=job['id'])
```

And look at job directory files as they execute to monitory them:

```
output_files = [f['name'] for f in ag.jobs.listOutputs(filePath='outputs', jobId=job['id'])]
```

#### Getting Job Output

Once the job reaches the archived state, we can see the archive path of the job, which should be accessible from our notebook.

```pyhton
job = ag.jobs.get(jobId=job['id'])
job_archive_path = Path(prefix) / Path(job['archivePath']).relative_to('clos21')
```

For interacting with ADCIRC output data we will primarily use the xarray library:

```python
import xarray as xa

f61 = xa.load_dataset(job_archive_path / 'outputs' / 'job_1.61.nc')
f61
```

We should see an xarray structure looking something like:

![caption](img/f61_xarray.png)
> Example ADCIRC time-series xarray data-structure

We can quickly plot using xarray's native plotting capabilities:

```python
f61.isel(station=2)['zeta'].plot()
```

![caption](img/si_tides_ts.png)
> Example ADCIRC time-series output, this example contains tide only forcing, so we see a pretty basic periodic signal.

#### Analyzing Logs

Log files for each job are stored in the `logs/` directory of any job, and are saved per job. They can also be loaded conveniently using pandas:


## Ensemble of ADCIRC Runs

In this section we will review how to execute an ensemble run of a larger set of files. We will also go over how to use files that are staged in a Project directory. 

We will use as a model data set the [Texas FEMA Synthetic Storm Data Set](https://www.designsafe-ci.org/data/browser/projects/5832364376574324245-242ac116-0001-012/)


### Configuring Job

TODO: Configuring Ensemble run

### Running and Monitoring

TODO: Running and Monitoring Ensemble runs. Cover doing smaller batches.

### Post-Processing: Summarizing Output

TODO: Post-process ensemble run into figure of time-series.

## General Purpose Ensemble Simulations 

TODO: Shell demo of how to build a general-purpose ensemble simulator

TODO: Finish Section
