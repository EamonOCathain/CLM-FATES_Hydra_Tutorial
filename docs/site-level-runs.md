
# Part 8: Setting Up a Site-Level FATES Simulation

So far we have run a standard CLM-FATES simulation using global datasets.

Next we will run FATES at a single site using inventory and meteorological observations.

Site-level simulations allow direct comparison with field measurements such as:

- Forest inventories
- Flux tower measurements
- Local meteorological observations
- Trait measurements

---

## Example Site: Barro Colorado Island (BCI)

For this tutorial we will use:

```text
Barro Colorado Island (BCI), Panama
```

The simulation uses:

```text
Inventory data
Local meteorological forcing
FATES vegetation dynamics
```

---

## Creating a Parameter File Directory

Create a directory for custom parameter files:

```bash
mkdir $VSC_SCRATCH/cesm/params
```

Move into it:

```bash
cd $VSC_SCRATCH/cesm/params
```

---

## Copy the Default FATES Parameter File

Copy the default parameter file:

```bash
cp $VSC_SCRATCH/cesm/inputdata/lnd/clm2/paramdata/fates_params_api.32.0.0_12pft_c231215.nc \
   fates_params_default.nc
```

---

## Converting Between NetCDF and CDL

FATES parameter files are stored as NetCDF files.

To inspect them more easily they can be converted into CDL format.

Convert NetCDF to CDL:

```bash
ncdump fates_params_default.nc > fates_params_default.cdl
```

Convert CDL back to NetCDF:

```bash
ncgen -o fates_params_default.nc fates_params_default.cdl
```

Verify the files exist:

```bash
ls
```

---

## Create a Single-PFT Parameter File

Copy the PFT manipulation script:

```bash
cp $VSC_SCRATCH/cesm/sources/ctsm-5.2.0/src/fates/tools/FatesPFTIndexSwapper.py .
```

Load SciPy:

```bash
ml SciPy-bundle/2023.07-gfbf-2023a
```

Create a parameter file containing only a single tropical PFT:

```bash
./FatesPFTIndexSwapper.py \
    --fin=fates_params_default.nc \
    --fout=fates_params_default-1pft.nc \
    --pft-indices=1
```

This creates a simplified parameter file containing only one PFT.

The resulting file will be used in the site-level simulations described in the following sections.

# Part 9: Running a Site-Level FATES Simulation

The easiest way to run FATES is through a create script that automates all four CESM workflow steps:

1. create_newcase
2. case.setup
3. case.build
4. case.submit

Example create scripts are available in:

https://github.com/sdeherto/CLM-FATES4UGent/tree/main/runscripts

For this tutorial we will use:

```text
create_clm-fates-bci_jra-init.sh
```

This script creates a FATES simulation for:

```text
Barro Colorado Island (BCI)
Inventory initialization
JRA climate forcing
```

---

## Prepare Directories

Create directories for run scripts and site data:

```bash
cd $VSC_SCRATCH/cesm

mkdir runscripts
mkdir sitedata
```

---

## Download Tutorial Files

Clone the tutorial repository:

```bash
git clone https://github.com/sdeherto/CLM-FATES4UGent.git
```

Move into the repository:

```bash
cd CLM-FATES4UGent
```

Copy the required directories:

```bash
cp -r runscripts $VSC_SCRATCH/cesm/runscripts
cp -r sitedata $VSC_SCRATCH/cesm/sitedata
```

---

## Modify the Create Script

Move to the runscripts directory:

```bash
cd $VSC_SCRATCH/cesm/runscripts
```

Open the script:

```bash
vi create_clm-fates-bci_jra-init.sh
```

Some paths contain a hardcoded VSC number.

Replace:

```text
vsc46573
```

with your own VSC number wherever necessary.

---

## Run the Create Script

Make the script executable:

```bash
chmod u+x create_clm-fates-bci_jra-init.sh
```

Launch the simulation:

```bash
./create_clm-fates-bci_jra-init.sh
```

The script will automatically:

- Create the case
- Configure the case
- Build the executable
- Submit the run

You will see a large amount of CESM output as the model compiles.

---

## Monitor Progress

Check the queue:

```bash
squeue
```

When the job status becomes:

```text
R
```

the simulation is actively running.

---

## Locate Output

Navigate to the run directory.

Example:

```bash
cd output/fates-tutorial-bci_jra-inventory_init.2024-08-30/run
```

List the files:

```bash
ls
```

You should begin to see monthly output files such as:

```text
*.elm.h0.1900-01.nc
```

One file is produced for each output period.

---


# Part 12: Setting Up FATES at a New Site

A new site simulation requires three main inputs:

```text
Surface file
Domain file
Climate forcing
```

---

## Surface Files

Contain information such as:

```text
Land cover
Soil properties
Topography
Vegetation characteristics
```

---

## Domain Files

Define:

```text
Spatial extent
Grid information
```

for the simulation.

---

## Climate Forcing

Typically contains:

```text
Solar radiation
Precipitation
Temperature
Humidity
Wind
```

at six-hourly or finer resolution.

---

## Generate Site Files

Navigate to:

```bash
cd $VSC_SCRATCH/cesm/sources/ctsm-5.2.0/tools/site_and_regional
```

Run:

```bash
./subset_data point \
    --lat 8.95 \
    --lon 280.2 \
    --site bci_subset \
    --create-surface \
    --create-datm \
    --create-domain \
    --dompft 1 \
    --overwrite \
    --datm-syr 2003 \
    --datm-eyr 2015 \
    --create-user-mods \
    --outdir $VSC_SCRATCH/cesm/sitedata/bci_test \
    --inputdata-dir $VSC_SCRATCH/cesm/inputdata
```

This generates site-level files that can be used in a new FATES simulation.

---

## Create a New Site Script

Start from an existing example.

For example:

```text
create_clm-fates-yangambi.sh
```

Rename it for your site.

---

## Update the Script

Modify:

```text
SITE
PTS_LAT
PTS_LON
surface file
domain file
STOP_N
REST_N
```

Ensure the script points to the files generated by `subset_data`.

---

## Additional Output Variables

Extra output variables can be requested through:

```text
user_nl_elm
```

within the create script.

---

# Part 13: Setting Up Regional Simulations

Regional simulations work similarly to site-level simulations but cover many grid cells.

---

## Generate Regional Surface Data

Navigate to:

```bash
cd $VSC_SCRATCH/cesm/sources/ctsm-5.2.0/tools/site_and_regional
```

Run:

```bash
./subset_data region \
    --create-surface \
    --lat1 -25 \
    --lat2 25 \
    --lon1 1 \
    --lon2 45 \
    --overwrite \
    --verbose \
    --outdir $VSC_SCRATCH/cesm/sitedata/africa \
    --inputdata-dir $VSC_SCRATCH/cesm/inputdata
```

---

## Create the Mesh File

Load NCO:

```bash
module load NCO
```

Copy the SCRIP file:

```bash
cp $VSC_SCRATCH/cesm/sitedata/scrip.nc ./
```

Generate the mesh:

```bash
ncks --rgr infer --rgr scrip=scrip.nc \
<fsurdat_generated_in_previous_step> \
lnd_mesh.nc
```

Convert:

```bash
ESMF_Scrip2Unstruct scrip.nc lnd_mesh.nc 0
```

---

## Create a Regional Run Script

Start from:

```text
create_clm-fates-africa.sh
```

Rename it for your region.

---

## Update the Script

Modify:

```text
SITE
surface file
mesh file
STOP_N
REST_N
```

and any desired output variables.

Regional simulations are useful when:

```text
A single site is too small
Continental-scale patterns are required
Spatially explicit vegetation dynamics are needed
```