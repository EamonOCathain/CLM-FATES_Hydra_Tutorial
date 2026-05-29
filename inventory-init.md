
# Part 10: Working with Inventory Initialization

FATES supports several initialization modes.

---

## Restart Initialization

Continue from a previous simulation.

Requires:

```text
Restart files
```

---

## Bare Ground Initialization

Start from an empty landscape.

Useful when:

```text
No inventory data are available
Long spinup simulations are required
```

---

## Inventory Initialization

Initialize the simulation using field inventory data.

This starts the simulation with a realistic forest structure already present.

Inventory initialization requires:

```text
Patch file (.pss)
Cohort file (.css)
Control file
```

---

## Inventory Files

The inventory files describe:

### Patch Structure (.pss)

Defines:

```text
Patch ages
Patch areas
Disturbance history
```

### Cohort Structure (.css)

Defines:

```text
Tree sizes
Tree densities
Species composition
```

### Control File

Defines:

```text
Location of inventory files
Site information
```

---

## Inventory Initialization Control File

The tutorial inventory setup uses:

```bash
vi $VSC_SCRATCH/cesm/inventory/fates_bci_jra_inventory_ctrl
```

Check that the paths point to your inventory files.

Replace any hardcoded VSC numbers with your own.

---

## Switching to Bare Ground

If inventory initialization is unavailable:

```text
use_fates_inventory_init = .false.
```

This starts the simulation from bare ground.

Inventory initialization is preferred whenever high-quality inventory data are available.

---

## Converting Field Data

To use your own inventory data, field observations must be converted into FATES inventory files.

Guidance is available here:

https://fates-users-guide.readthedocs.io/projects/tutorial/en/latest/inventory_data.html

This conversion step is site-specific and usually requires:

```text
Species information
DBH measurements
Tree densities
Plot dimensions
```
