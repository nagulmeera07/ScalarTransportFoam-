# Parameter study on the Transport Equation

- Clone the repository ' https://github.com/nagulmeera07/ScalarTransportFoam-.git ' in the run directory of openfoam.
- open each case file then run scalarTransportFoam and make .foam.
- open .foam file in paraview.
- run remaining cases then open in same paraview layout by changing position of the each solution.
- compare the solutions.

## Main Govering Equation ∂T/∂t + ∇·(uT) = D<sub>T</sub> ∆T

| Case No | **u** [m/s] | **D<sub>T</sub>** [m²/s] | **Transport equation** |
|----|-------------|-------------------------|------------------------|
| scalarTransportFoam_1 | (0 0 0)     | 1e-6                    |  ∂T/∂t = D<sub>T</sub> ∆T  |
| scalarTransportFoam_2 | (0 0 0)     | 1e-4                    | ∂T/∂t = D<sub>T</sub> ∆T |
| scalarTransportFoam_3  | (0 0 0)     | 1e-2                    | ∂T/∂t = D<sub>T</sub> ∆T  |
| scalarTransportFoam_4 | (0 0 0)     | 1e0                     |∂T/∂t = D<sub>T</sub> ∆T |
| scalarTransportFoam_5| (1 0 0)     | 0                       |  ∂T/∂t + ∇·(uT) = 0     |
| scalarTransportFoam_6  | (0.5 0 0)   | 0                       | ∂T/∂t + ∇·(uT) = 0      |
| scalarTransportFoam_7| (2 0 0)     | 0                       |  ∂T/∂t + ∇·(uT) = 0   |
| scalarTransportFoam_8 | (1 0 0)     | 1e-6                    |  ∂T/∂t + ∇·(uT) = D<sub>T</sub> ∆  |
| scalarTransportFoam_9 | (1 0 0)     | 1e-4                    | ∂T/∂t + ∇·(uT) = D<sub>T</sub> ∆T |
| scalarTransportFoam_10 | (1 0 0)     | 1e-2                    |  ∂T/∂t + ∇·(uT) = D<sub>T</sub> ∆T  |
| scalarTransportFoam_11 | (1 0 0)     | 1e0                     |  ∂T/∂t + ∇·(uT) = D<sub>T</sub> ∆T|