# First photonics project

A learning workspace for a first ring resonator layout with [gdsfactory](https://github.com/gdsfactory/gdsfactory). The environment is set up; the device design is intentionally left for me to implement and understand.

**Current status:** environment and project plan only. No device layout, optical simulation, or fabrication validation has been completed. This is a personal learning project, with no affiliation with NC State or HIP Lab.

## Environment

Use Python 3.12 and the pinned gdsfactory version in `pyproject.toml`. `uv.lock` records the resolved dependencies. The `.venv` folder stays local and is excluded from Git.

From this folder:

```sh
uv sync --locked
uv run python -c "import gdsfactory as gf; gf.gpdk.PDK.activate(); print('gdsfactory', gf.__version__)"
```

Run scripts with `uv run python your_script.py`. To use an editor, open this folder and select `.venv/bin/python` as its Python interpreter. `uv` is already installed on the computer where this workspace was created. On another computer, follow the [official uv installation guide](https://docs.astral.sh/uv/getting-started/installation/) first.

For an interactive Python session:

```sh
uv run python
```

Then inspect the available component without making a device:

```python
import gdsfactory as gf
gf.gpdk.PDK.activate()
help(gf.components.ring_single)
```

## My first build: one single-bus ring resonator

Aim for one explainable device before a larger circuit. A ring is a useful starting point because radius, coupling gap, and waveguide width have distinct roles. A Mach–Zehnder interferometer can be the next project.

1. **Understand the component.** Read the [gdsfactory Python introduction](https://gdsfactory.github.io/gdsfactory/notebooks/_0_python.html) and the [`ring_single` source and documentation](https://github.com/gdsfactory/gdsfactory/blob/v9.51.0/gdsfactory/components/rings/ring_single.py). Identify the bus, closed resonator, coupling region, and two optical ports. Draw a sketch and explain where light enters and exits.
2. **Write my own layout script.** Create `design.py`. Activate the generic PDK and construct a `ring_single` with explicit parameters. Suggested starting values: radius 10 µm, gap 0.2 µm, width 0.5 µm, horizontal straight length 4 µm, and vertical straight length 2 µm. Pass width through a strip cross-section. These are learning values, not foundry specifications. If using a circular-ring length formula, explicitly select `bend_circular`; the component's default bends are Euler bends.
3. **Inspect and export.** Generate a `.gds` and a layout image in a new `artifacts/` folder. View the GDS in [KLayout](https://www.klayout.de/), inspect the gap at high magnification, and verify the ring closes and the bus has two exposed ports. Python's KLayout dependency does not necessarily install the desktop viewer. Record units and layers. Explain that the generic PDK is not a foundry-qualified process and this layout has no fiber interfaces yet.
4. **Make one controlled comparison.** Change only radius, for example 8, 10, and 12 µm. Describe the expected trend in free spectral range. If adding a spectrum later, use a clearly labeled analytic model and list every assumed parameter; do not claim the drawn gap predicts the coupling coefficient without a calibrated model or electromagnetic calculation.
5. **Document what I learned.** Replace this starter README with a short project report: question, device diagram, parameter table, layout preview, method, one result, checks, limitations, and next experiment. Commit the Python source and selected outputs so someone else can reproduce them.

For circular bends and this racetrack geometry, a useful initial centerline length estimate is `L = 2πR + 2 length_x + 2 length_y`. The local wavelength free spectral range estimate is `FSR ≈ λ² / (n_g L)`, using consistent units. The group index `n_g` must come from a process model or be explicitly marked as an assumption. For an analytic spectrum, start with the [single-bus ring model in Ansys documentation](https://optics.ansys.com/hc/en-us/articles/360036618853-Single-Bus-Ring-Resonator-RING-INTERCONNECT-Element).

## What makes the finished artifact useful

- I can explain every parameter and why I chose it.
- A reader can recreate the GDS with a documented command and pinned dependencies.
- The preview makes the bus, resonator, and coupling region easy to identify.
- The report distinguishes layout generation, an assumed analytic model, electromagnetic simulation, and measured results.
- I can explain one trend and name the next experiment needed to test it.

The [HIP Lab research page](https://sites.google.com/ncsu.edu/hip-lab/research/non-volatile-photonics) describes work involving rings and Mach–Zehnder filters. This starter project is a foundation for learning those passive building blocks; it does not reproduce their heterogeneous devices or non-volatile memory results.

## Save progress

After editing or adding my own work:

```sh
git status
git add README.md design.py artifacts/
git commit -m "Add first ring layout and explain design choices"
git push
```

Only add `design.py` and `artifacts/` once they exist. Keep the first milestone small: one script, one GDS, one clear image, and an explanation I can defend.
