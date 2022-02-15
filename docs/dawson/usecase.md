# Using ADCIRC and HPC workflows for Ensemble Modeling in DesignSafe 

**Clint Dawson, University of Texas at Austin**  
**Carlos del-Castillo-Negrete, University of Texas at Austin**  
**Benjamin Pachev, University of Texas at Austin**  

The following use case presents an example of how to leverage the Tapis API DesignSafe is built off of to run a complex HPC workflow. The specific workflow to be presented consists of running ADCIRC, a storm-surge modeling application available on DesignSafe, using the parametric job launcher utility known as pylauncher. All code and examples presented are meant to be be executed from a Jupyter Notebook on the DesignSafe platform and using a DesignSafe account to make Tapis API calls. 

[Jupyter notebooks on DS Juypterhub](https://www.designsafe-ci.org/rw/workspace/#!/Jupyter::Analysis)<br/>
The rest of this documentation is laid out as follows:


## Background 

### Citation and Licensing

* Please cite [Dawson et al. (2021)](https://doi.org/10.17603/ds2-68a9-0s64) if using any of the Texas FEMA Hurricane data used in the examples below. 

* Please cite [Rathje et al. (2017)](https://doi.org/10.1061/(ASCE)NH.1527-6996.0000246) to acknowledge the use of DesignSafe resources.  

* This software is distributed under the GNU General Public License (https://www.gnu.org/licenses/gpl-3.0.html).  

### ADCIRC

TODO: Finish Brief ADCIRC overview and links to further resources:

ADCIRC Resources:

* [Link Example - this goes to Google](https://www.google.com)

#### Shallow Water Equations

Depth-averaged barotropic form of the Shallow Water Equations:
<center>
```math
$$
\frac{\partial \zeta}{\partial t} + \nabla \cdot (\mathbf{U} H) = 0,
$$

$$
\frac{\partial \mathbf{U}}{\partial t} + \mathbf{U} \cdot \nabla \mathbf{U} +
f \mathbf{k} \times \mathbf{U} = -\nabla\left[ \frac{p_s}{\rho_0} + g\zeta\right] +
\frac{\boldsymbol\tau_s - \boldsymbol\tau_b}{\rho_0 H},
$$
```

Where we have:

  * $\zeta=\zeta(x, y, t)$ = water surface elevation with respect to mean sea level
  * $\mathbf{U} = \mathbf{U}(x, y, t)$ the depth-averaged velocity (averaged over the height of the water column)
  * $f$ = Coriolis parameter, $\mathbf{k}$ = ___
  * $p_s$ = atmospheric pressure, $g$ = gravitational constant, $\rho_0$ = reference density of water
  * $\boldsymbol\tau_s$ = surface stress, $\boldsymbol\tau_b$ = bottom stress, $H$ = height of the water column


### Tapis

TODO: Brief Tapis overview with links to more resources and tutorials/docs:

[Link Example - this goes to Google](https://www.google.com)

## Simple ADCIRC run using Tapis

This section will contain info about how to run a simple ADCIRC run that and visualize some outputs

Overview:

* Set-up
* Tapis Configuration
* Submitting and monitoring
* Getting and visualizing output

### Configuring input and exeuctables

TODO: Quick overview on optiosn for inptus and executables. For now use default examples available on either DesignSafe (How to update these?) and/or hurrican storm-surge project.

### Configuring Tapis Job

TODO: Configuring job json input

**ADCIRC Job Configurations**

| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Stampede2| CPU      | 2017     |     
| Frontera | CPU & GPU| 2019     |     

**Tapis HPC Configurations**

| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Stampede2| CPU      | 2017     |     
| Frontera | CPU & GPU| 2019     |     

Or use markdown table generator: [https://www.tablesgenerator.com/markdown_tables](https://www.tablesgenerator.com/markdown_tables)

### Submitting and Monitoring Job

TODO: Finish section on submitting and monitoring tapis job. common errors?

### Downloading and visualizing output

TODO: Getting and visualizing output, Python and figuregen outputs

*Add images to the folder img and use relative path to specify the location of the image.*   

![caption](img/mkdocs-template.png)
> Use case template design

## Pylauncher - Running an Ensemble of ADCIRC Runs

TODO: Finish Section

Numbered list 

1. [numbered linked item](https://maps.google.com)
2. second item
3. third item


### Pylauncher Background

TODO: Pylauncher info and background

### Configuring Job

### Running and Monitoring

### Post-Processing: Summarizing Output

## Pylauncher - General Purpose Ensemble Simulations

TODO: Finish Section
