
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