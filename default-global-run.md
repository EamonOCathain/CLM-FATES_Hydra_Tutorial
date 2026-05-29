
# Part 2: Creating and Running a Default CLM-FATES Simulation

CESM simulations follow four main steps:

1. Create a case
2. Set up the case
3. Build the model executable
4. Submit the model run

These four commands form the core CESM workflow:

```bash
./create_newcase
./case.setup
./case.build
./case.submit
```

---

## Understanding create_newcase

The `create_newcase` command defines:

- The case name
- The model resolution
- The component set (compset)
- The compiler
- The target machine

General syntax:

```bash
./create_newcase \
    --case CASE_NAME \
    --res RESOLUTION \
    --compset COMPSET \
    --compiler COMPILER \
    --machine MACHINE
```

---

## Case Name

The `--case` argument defines where the case will be created.

For this tutorial:

```bash
--case $VSC_SCRATCH/cesm/cases/control
```

The case will be called:

```text
control
```

and all case configuration files will be stored under:

```text
$VSC_SCRATCH/cesm/cases/control
```

---

## Resolution

The `--res` argument defines the model grid.

For this tutorial:

```bash
--res f19_g17
```

This corresponds to a global grid with approximately:

```text
1.9° latitude × 2.5° longitude
```

Many additional grids are available:

http://www.cesm.ucar.edu/models/cesm2/config/grids.html

---

## Compset

The `--compset` argument defines:

- Which model components are active
- Physical parameterisations
- Forcing configuration
- Carbon and vegetation options

For this tutorial we use:

```bash
I2000Clm50Fates
```

This is a CLM5-based FATES simulation representing year-2000 conditions.

A full list of CESM compsets is available at:

http://www.cesm.ucar.edu/models/cesm2/config/compsets.html

---

## Create the Case

Move to the CESM scripts directory:

```bash
cd $VSC_SCRATCH/cesm/sources/ctsm-5.2.0/cime/scripts
```

Create the case:

```bash
./create_newcase \
    --case $VSC_SCRATCH/cesm/cases/control \
    --res f19_g17 \
    --compset I2000Clm50Fates \
    --compiler gnu \
    --machine hydra \
    --run-unsupported
```

After completion, move to the case directory:

```bash
cd $VSC_SCRATCH/cesm/cases/control
```

Inspect the generated files:

```bash
ls
```

The newly created case now contains all configuration files required to set up, compile, and run the model.

# Part 3: Configuring the Case with case.setup

Once the case has been created, the next step is to configure how the simulation will run.

The case configuration is stored in a collection of XML files located in the case directory:

```bash
cd $VSC_SCRATCH/cesm/cases/control
```

List the contents:

```bash
ls
```

You should see files such as:

```text
env_run.xml
env_batch.xml
env_build.xml
env_mach_pes.xml
```

These files control:

- Run length
- Start date
- Restart frequency
- Walltime
- Number of processors
- Compilation settings
- Batch submission options

---

## Inspect the Default Run Settings

Open the run configuration file:

```bash
vi env_run.xml
```

Try to find:

- The simulation start date
- How long the model will run
- Restart settings

Although these files can be edited directly, CESM recommends using the `xmlchange` utility.

---

## Disable Short-Term Archiving

For tutorial runs we disable the short-term archiver.

Run:

```bash
./xmlchange DOUT_S=FALSE
```

Verify the change:

```bash
./xmlquery DOUT_S
```

Expected output:

```text
DOUT_S: FALSE
```

---

## Set Simulation Length

For this tutorial we will run a 12-month simulation.

Configure the run length:

```bash
./xmlchange STOP_N=12,STOP_OPTION=nmonths
```

This tells CESM:

```text
STOP_OPTION = nmonths
STOP_N      = 12
```

meaning:

```text
Run for 12 months.
```

Verify:

```bash
./xmlquery STOP_N
./xmlquery STOP_OPTION
```

---

## Configure Computational Resources

Set the number of MPI tasks and walltime:

```bash
./xmlchange NTASKS=4,JOB_WALLCLOCK_TIME=24:00:00
```

This requests:

```text
4 MPI tasks
24 hours walltime
```

Verify:

```bash
./xmlquery NTASKS
./xmlquery JOB_WALLCLOCK_TIME
```

---

## Run case.setup

Once the configuration is complete, initialise the case:

```bash
./case.setup
```

This command:

- Generates component namelists
- Creates batch scripts
- Creates user namelist files
- Prepares the case for compilation

New files will appear in the case directory.

Inspect the directory again:

```bash
ls
```

Pay particular attention to:

```text
user_nl_clm
user_nl_datm
CaseDocs/
Buildconf/
```

These files are generated during setup and contain the final configuration that will be passed to the model.

---

## Modifying CLM Namelists

Many CLM options can be changed through:

```bash
vi user_nl_clm
```

This file allows users to override default namelist values.

Examples include:

- Output variables
- Surface datasets
- Initial conditions
- Carbon cycle options
- FATES settings

Reference:

https://www.cesm.ucar.edu/models/cesm2/settings/current/clm5_0_nml.html

---

# Part 4: Building the Model with case.build

After setup, the model must be compiled.

Compilation converts the source code into an executable that can run on Hydra.

---

## Start Compilation

From the case directory:

```bash
./case.build
```

Compilation may take several minutes.

During the build process CESM:

1. Generates final namelists
2. Compiles supporting libraries
3. Compiles individual model components
4. Links everything into a single executable

---

## What Gets Compiled?

For a CLM-FATES run you will typically see components such as:

```text
datm
clm
mosart
cpl
```

along with supporting libraries:

```text
gptl
mct
pio
csm_share
```

Briefly:

**gptl**
Timing and profiling library.

**mct**
Model Coupling Toolkit used for communication between components.

**pio**
Parallel I/O library for reading and writing NetCDF files.

**csm_share**
Shared CESM infrastructure used by multiple components.

**datm**
Data atmosphere model providing meteorological forcing.

**clm**
Community Land Model containing FATES.

**mosart**
River routing model.

**cpl**
The coupler that exchanges information between components.

---

## Build Success

The build is successful when you see:

```text
MODEL BUILD HAS FINISHED SUCCESSFULLY
```

Example:

```text
Total build time: 334 seconds
MODEL BUILD HAS FINISHED SUCCESSFULLY
```

At this point the executable has been created and the model is ready to run.

---

# Part 5: Exploring the Source Code and Input Data

While the model is compiling it is useful to explore the source code and datasets used by CLM-FATES.

---

## Exploring the FATES Source Code

Move to the FATES source directory:

```bash
cd $VSC_SCRATCH/cesm/sources/ctsm-5.2.0/src/fates
```

One useful file to inspect is:

```bash
cd biogeophys

vi FatesPlantHydraulicsMod.F90
```

This module contains parts of the plant hydraulics implementation.

The FATES source tree is organised into modules covering topics such as:

```text
allometry
carbon cycling
recruitment
mortality
competition
radiation
hydraulics
disturbance
```

Do not modify any source files during the tutorial.

---

## Exploring Input Data

The CESM input datasets are stored under:

```bash
cd $VSC_SCRATCH/cesm/inputdata
```

For CLM-FATES, important datasets include:

```text
surface datasets
domain files
meteorological forcing
parameter files
initial conditions
```

---

## Surface Datasets

Navigate to:

```bash
cd $VSC_SCRATCH/cesm/inputdata/lnd/clm2/surfdata_esmf
```

Surface datasets contain information such as:

- Land cover
- Soil properties
- Topography
- PFT distributions
- Urban fractions
- Crop fractions

---

## Parameter Files

Navigate to:

```bash
cd $VSC_SCRATCH/cesm/inputdata/lnd/clm2/paramdata
```

These files contain:

- Plant traits
- Photosynthesis parameters
- Hydraulic parameters
- Carbon cycle coefficients
- FATES PFT definitions

---

## Inspect NetCDF Files

Useful commands:

Display metadata:

```bash
ncdump -h filename.nc
```

Display dimensions and variables:

```bash
ncks -m filename.nc
```

Visualise interactively:

```bash
ncview filename.nc
```

(Note that ncview generally requires a graphical session and may not work through the web interface.)

---

## Understanding Input Data Requirements

CLM-FATES requires several categories of input data:

```text
Meteorological forcing
Surface datasets
Domain files
Parameter files
Initial condition files
```

These files determine:

- Where the model runs
- Which vegetation is present
- How the vegetation behaves
- What climate it experiences
- How the simulation is initialised

In later sections we will replace many of these default datasets with site-specific FATES inputs.

# Part 6: Running the Model and Inspecting Output

Once the model has been successfully built, it can be submitted to the scheduler.

---

## Submit the Model Run

From the case directory:

```bash
cd $VSC_SCRATCH/cesm/cases/control
```

Submit the run:

```bash
./case.submit
```

CESM will:

- Verify required input files exist
- Generate final namelists
- Create batch submission scripts
- Submit the job to SLURM

---

## Monitor Job Status

Check the queue:

```bash
mysqueue
```

or

```bash
squeue -u $USER
```

Typical job states include:

```text
PD = Pending
R  = Running
CG = Completing
```

---

## Cancel a Job

If necessary:

```bash
scancel <jobid>
```

Example:

```bash
scancel 6031806
```

---

## Locate Model Output

Once the run has completed, move to the run directory:

```bash
cd $VSC_SCRATCH/cesm/output/control/run
```

List the contents:

```bash
ls
```

You should see various NetCDF output files.

Typical files include:

```text
control.clm2.h0.0001-01.nc
control.clm2.r.0002-01-01-00000.nc
control.cpl.hi.0001-01.nc
```

---

## Inspect CLM Output

Open the main CLM history file:

```bash
ncview control.clm2.h0.0001-01.nc
```

Alternatively inspect metadata:

```bash
ncdump -h control.clm2.h0.0001-01.nc
```

Questions to explore:

- Which time period does the file cover?
- Which variables are available?
- Which dimensions are present?
- What units are used?

---

## Common Output Variables

Examples include:

```text
GPP
NPP
TLAI
TOTVEGC
TOTSOMC
QVEGE
QVEGT
QSOIL
```

You can inspect individual variables using:

```bash
ncks -m control.clm2.h0.0001-01.nc
```

---

## Useful Output Directories

Case setup:

```text
$VSC_SCRATCH/cesm/cases/control
```

Run output:

```text
$VSC_SCRATCH/cesm/output/control/run
```

Build logs:

```text
$VSC_SCRATCH/cesm/output/control/bld
```

Archive files:

```text
$VSC_SCRATCH/cesm/output/control/archive
```

---

# Part 7: Understanding the CESM Directory Structure

A typical CESM installation contains four main directories.

---

## CESM Scripts Directory

Location:

```bash
cd $VSC_SCRATCH/cesm/sources/ctsm-5.2.0/cime/scripts
```

Purpose:

```text
Create new cases
Manage configurations
Generate case directories
```

Important command:

```bash
./create_newcase
```

---

## Input Data Directory

Location:

```bash
cd $VSC_SCRATCH/cesm/inputdata
```

Purpose:

```text
Meteorological forcing
Surface datasets
Domain files
Parameter files
Initial conditions
```

This directory is typically shared between users.

---

## Cases Directory

Location:

```bash
cd $VSC_SCRATCH/cesm/cases
```

Purpose:

```text
Case setup
Configuration
Compilation
Submission
```

Each simulation has its own case directory.

Example:

```text
cases/control
```

---

## Output Directory

Location:

```bash
cd $VSC_SCRATCH/cesm/output
```

Purpose:

```text
Executable build files
Runtime files
Model output
Restart files
Logs
```

This is where the model actually runs.

---

## Useful Links

CLM5 namelists:

https://www.cesm.ucar.edu/models/cesm2/settings/2.2.0/clm5_0_nml.html

CESM2 compsets:

https://www.cesm.ucar.edu/models/cesm2/config/compsets.html

CLM5 documentation:

https://escomp.github.io/ctsm-docs/versions/release-clm5.0/html/tech_note/index.html

CESM website:

http://www.cesm.ucar.edu/models/cesm2/

Example plotting scripts:

https://github.com/sdeherto/CLM-FATES4UGent/tree/main/plotting

---