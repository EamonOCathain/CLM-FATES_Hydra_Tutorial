
# Part 1: Preparing the CESM Environment

## Prerequisites

You will need:

- A VSC account
- Access to the Hydra cluster at VUB
- CESM dependencies available through the module system

For UGent users:

- Input data will be stored in shared UGent storage
- Simulations will be compiled and executed on Hydra
- Model output should be written to `$VSC_SCRATCH`, which is local to Hydra

---

## Load Required Modules

Load the CESM dependencies:

```bash
module load CESM-deps/2-foss-2024a
```

---

## Create the CESM Directory Structure

Create a working directory:

```bash
mkdir $VSC_SCRATCH/cesm
```

Create the standard CESM folder structure:

```bash
mkdir $VSC_SCRATCH/cesm/cases
mkdir $VSC_SCRATCH/cesm/output
mkdir $VSC_SCRATCH/cesm/sources
```

Create a symbolic link to the shared CESM input data repository:

```bash
ln -sf /data/gent/vo/000/gvo00074/inputdata_cesm \
       $VSC_SCRATCH/cesm/inputdata
```

The resulting directory structure should look like:

```text
cesm/
├── cases/
├── output/
├── sources/
└── inputdata
```

---

## Download CTSM

Move to the sources directory:

```bash
cd $VSC_SCRATCH/cesm/sources
```

Clone CTSM:

```bash
git clone -b ctsm5.2.0 https://github.com/ESCOMP/CTSM ctsm-5.2.0
```

---

## Download External Components

Move into the CTSM source directory:

```bash
cd ctsm-5.2.0
```

Download the required external components:

```bash
./manage_externals/checkout_externals
```

This step downloads all CESM components required by CTSM.

---

## Configure CESM for Hydra

Add the Hydra machine configuration to CESM:

```bash
update-cesm-machines -m ctsm $EBROOTCESMMINDEPS/machines/
```

This allows CESM to recognise Hydra as a supported machine for compilation and job submission.

---