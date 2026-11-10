# AED Placement Optimization

**University of Arkansas | Optimization Methods in Data Science**

A facility-location optimization project that identifies where to place **three automated external defibrillators (AEDs)** in the University of Arkansas SCEN building to reduce estimated emergency response time.

## Problem

When someone experiences sudden cardiac arrest, access to an AED is time-sensitive. AEDs placed without considering where people are located may leave some rooms with unnecessarily long retrieval times. This project treats AED placement as a **population-weighted p-median facility-location problem**: select three room locations for AEDs and assign every modeled room to one selected AED.

The model minimizes total estimated response time weighted by each room's capacity, while requiring each room's assigned AED to be within **180 seconds** of estimated travel time.

## Model and assumptions

The notebook includes 34 candidate rooms across six floors. Each room has a floor number and a capacity used as a proxy for potential demand.

Estimated travel times are calculated as follows:

- **Same floor:** Rooms are ordered by room number, and the assumed 70-second hallway traversal is divided evenly across the ordered rooms on that floor.
- **Different floors:** Travel time is estimated as 70 seconds plus 20 seconds per floor of separation.
- **Coverage limit:** Assignments taking more than 180 seconds are prohibited.

These are simplified travel-time assumptions, **not measured walking routes or clinically validated response times**. Room capacity is a demand proxy rather than observed occupancy or cardiac-arrest incidence.

### Mathematical formulation

Let \(I\) be the set of rooms and \(J\) the candidate AED locations (the same rooms). Let \(w_i\) denote room capacity, \(t_{ij}\) estimated travel time from room \(i\) to AED location \(j\), \(x_j\) indicate whether an AED is installed at \(j\), and \(y_{ij}\) indicate whether room \(i\) is assigned to \(j\).

Minimize:

\[
\min \sum_{i\in I}\sum_{j\in J} w_i t_{ij}y_{ij}
\]

Subject to:

\[
\sum_{j\in J}x_j=3
\]

\[
\sum_{j\in J}y_{ij}=1 \quad \forall i\in I
\]

\[
y_{ij}\le x_j \quad \forall i,j
\]

\[
y_{ij}=0 \quad \text{if }t_{ij}>180
\]

with binary \(x_j\) and \(y_{ij}\).

The notebook also reports the **capacity-weighted average estimated response time**, computed as the objective value divided by total room capacity.

## Technologies

- Python
- [Pyomo](https://www.pyomo.org/) for optimization modeling
- [HiGHS](https://highs.dev/) for mixed-integer optimization
- Jupyter Notebook / Google Colab

## Run the project

1. Open `Optimization_Final_Code.ipynb` in Jupyter or Google Colab.
2. Install the dependencies in your environment:

   ```bash
   pip install pyomo highspy
   ```

3. Run the model cell. It uses `pyo.SolverFactory("highs")` and prints selected AED locations, room-to-AED assignments, the weighted objective, and average response time.

**Note:** The notebook's optional Colab setup cell installs IDAES extensions, whereas the optimization cell specifically requests the **HiGHS** solver. Ensure HiGHS is available before running the model.

## Outputs

The notebook prints:

- Three selected AED room locations
- Each modeled room's assigned AED and estimated travel time
- Total capacity-weighted response time
- Capacity-weighted average response time

The exact selected rooms and objective should be taken from a successful solver run; this README does not claim results that have not been independently reproduced.

## Limitations and future improvements

- Replace estimated travel times with measurements based on building floor plans and actual walking routes.
- Incorporate elevator access, stairwell positions, doors, and realistic retrieval times.
- Use observed occupancy patterns instead of room capacity alone.
- Compare different AED budgets (e.g., two, three, or four devices) and evaluate worst-case response time.
- Validate placement recommendations with building safety personnel before real-world deployment.

## Repository contents

- `Optimization_Final_Code.ipynb` — original Pyomo optimization model
- `README.md` — project description, formulation, instructions, and limitations

*Academic optimization project; not a substitute for an AED deployment safety assessment.*