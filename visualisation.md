# Part 14: Visualising Results

FATES typically writes one NetCDF output file per month.

Before analysis it is often useful to concatenate them into a single file.

---

## Move to the Run Directory

Example:

```bash
cd output/fates-tutorial-bci_jra-inventory_init.2024-08-30/run
```

---

## Concatenate Monthly Files

Use NCO:

```bash
ncrcat -h \
fates-tutorial-bci_jra-inventory_init.2024-08-30.elm.h0.190*.nc \
fates-tutorial-bci_jra-inventory_init.2024-08-30.sofar.nc
```

This command combines all monthly files into a single NetCDF file.

---

## Inspect the Output

View metadata:

```bash
ncdump -h fates-tutorial-bci_jra-inventory_init.2024-08-30.sofar.nc
```

View dimensions and variables:

```bash
ncks -m fates-tutorial-bci_jra-inventory_init.2024-08-30.sofar.nc
```

Visualise interactively:

```bash
ncview fates-tutorial-bci_jra-inventory_init.2024-08-30.sofar.nc
```

---

## Plotting Tutorials

Example analysis notebooks are available from the FATES tutorial:

https://github.com/NGEET/fates-tutorial/tree/main/fates-tutorial-jupyter-book

Both:

```text
Python
R
```

versions are available.

These provide examples for:

- Biomass trajectories
- Basal area
- PFT composition
- Mortality
- Recruitment
- Carbon fluxes

---

## Example Plotting Scripts

Additional plotting scripts are available at:

https://github.com/sdeherto/CLM-FATES4UGent/tree/main/plotting

These can be used as a starting point for analysing your own simulations.

# Part 12: Modifying the FATES Parameter File

One of the strengths of FATES is the ability to experiment with different plant functional types (PFTs) and trait combinations.

---

## Create a Two-PFT Parameter File

Move to the parameter directory:

```bash
cd $VSC_SCRATCH/cesm/params
```

Create a new parameter file containing two identical copies of the same PFT:

```bash
./FatesPFTIndexSwapper.py \
    --fin=fates_params_default.nc \
    --fout=fates_params-2pfts.nc \
    --pft-indices=1,1
```

---

## Convert to CDL

Convert to a human-readable format:

```bash
ncdump fates_params-2pfts.nc > fates_params-2pfts.cdl
```

Open the file:

```bash
vi fates_params-2pfts.cdl
```

You can now modify parameters for each PFT independently.

---

## Rebuild the NetCDF File

After editing:

```bash
ncgen -o fates_params-2pfts.nc fates_params-2pfts.cdl
```

This step is mandatory.

FATES can only read the NetCDF version.

---

## Using modify_fates_paramfile.py

Copy the helper script:

```bash
cp $VSC_SCRATCH/cesm/sources/ctsm-5.2.0/src/fates/tools/modify_fates_paramfile.py .
```

The script supports:

```text
--var   parameter name
--pft   PFT index
--fin   input file
--fout  output file
--val   new value
--O     overwrite
```

Example:

```bash
./modify_fates_paramfile.py \
    --var <parameter> \
    --pft 2 \
    --fin input.nc \
    --fout output.nc \
    --val <value> \
    --O
```

---

## Comparing Different Trait Strategies

A useful exercise is to create:

```text
Early successional PFT
Late successional PFT
```

by modifying parameters related to:

- Growth
- Mortality
- Wood density
- Recruitment
- Leaf economics

and comparing their coexistence through time.

---