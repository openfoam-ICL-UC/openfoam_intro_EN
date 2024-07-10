# openfoam_intro_EN

Introduction to OpenFOAM for Chemical Engineering Workshop. The examples are relevant to process engineering, food engineering, and biotechnology.

#### Suggested Prior Knowledge

The following prior knowledge will greatly facilitate your learning path. However, these are not absolute requirements and can be learned on the go.

* Basic command of the `Unix` console: If you want to learn the basics, we recommend this three-hour Software Carpentry course: [The Unix Shell](https://swcarpentry.github.io/shell-novice/)
* Transport Phenomena or Fluid Mechanics + Heat/Mass Transfer at undergraduate level
* Basic fundamentals of numerical methods: you can access to an excellent reference material on numerical methods at [Imperial College ESE - Primer Computational Mathematics](https://primer-computational-mathematics.github.io/book/c_mathematics/numerical_methods/intro.html). Additionally, if you are a spanish speaker and want to learn basic algorithms to solve initial value problems, boundary value problems, and initial and boundary value problems in the context of Transport Phenomena, we recommend the free MOOC [Transferencia de calor, masa y momentum computacional.](https://www.coursera.org/learn/transferencia-de-momentum-calor-y-masa-computacional).

#### Computational Requirements
The following computational requirements are **absolutely necessary** to start the workshop.
At the beginning of the workshop, it is necessary to install the following software:

* OpenFOAM v2406
* If your OS is Windows, Windows Subsystem for Linux 2 (WSL 2): [Installation instructions](https://github.com/openfoam-ICL-UC/openfoam_intro_EN/wiki/OpenFOAM-Installation-%E2%80%90-Windows)
* If your OS is macOS, pre-compiled version of OpenFOAM in Docker container: [Installation instructions](https://github.com/openfoam-ICL-UC/openfoam_intro_EN/wiki/OpenFOAM-Installation-%E2%80%90-macOS)
* If your OS is Linux, there is no need to install virtual machines or Docker.
* ParaView > 5.0: It should be installed on Windows Subsystem for Linux or macOS or Linux: [Installation instructions](https://github.com/openfoam-ICL-UC/openfoam_intro_EN/wiki/OpenFOAM-Installation-%E2%80%90-Windows)

This workshop is structured in two days, with two taught parts in the morning and one workshop at the afternoon. The learning objectives of each part of this workshop are:

#### Day 1, Part 1: Introduction to the Finite Volume Method
1. Convert a continuous mathematical model to a discrete numerical model for a one-dimensional unsteady heat transfer problem.
2. Discretise partial differential equations using the finite volume method.
3. Implement solution algorithm in Python and compare discretisation and algorithms for linear equation systems.

#### Day 1, Part 2: Implementation of the Numerical Solution to a Transport Phenomena Problem in OpenFOAM
1. Set up an OpenFOAM case representative of three-dimensional unsteady heat transfer in a fluid flow system.
2. Run an OpenFOAM `solver` from the command line.
3. Visualise the simulation results using paraview.

#### Day 1, Part 3: Hands-on setup of a 3-D unsteady forced convection problem.
1. Apply the OpenFOAM workflow to produce a case consisting of forced convection in a rectangular channel from a problem statement.
2. Use the command line to build the mesh, run the simulation, refine the mesh and apply basic postProcessing functions.
3. Visualise the results with ParaView

#### Day 2, Part 1: Post-Processing of Results in OpenFOAM
1. Write a `functionObject` code to calculate quantities of engineering interest.
2. Generate explanatory figures of relevant transport phenomena from Paraview filters.
3. Compare the OpenFOAM model results with the results obtained in Python.

#### Day 2, Part 2: Advanced Examples and Solvers of OpenFOAM for Coupled Transport Phenomena
1. Analyse the implementation of the conservation equations of heat, mass, and momentum for the `icoReactingMultiphaseInterFoam` solver.
2. Reproduce an OpenFOAM tutorial on water evaporation in air, where heat and mass transfer are coupled.
3. Post-process a multiphase OpenFOAM case.

#### Day 2, Part 3: Recycle a case to simulate a complex process
1. Anticipate the thermophysical and thermodynamic propertis necessary to simulate the evaporation of ethanol from a water-ethanol mixture.
2. Adapt the tutorial for icoReactingMultiphaseInterFoam to simulate the evaporation of ethanol during the preparation of a simplified Mulled Wine.
3. Postprocess the results using ParaView and the command line to suggest simplifying assumptions for reduced order models.

The documentation associated with each session can be found in [Wikis](https://github.com/openfoam-ICL-UC/openfoam_intro_EN/wiki)

## Authors:
Catalina Pino Muñoz, Research Associate, Earth Science and Engineering Department, Imperial College London

Felipe Huerta, Professor, Department of Chemical and Bioprocess Engineering, Pontificia Universidad Católica de Chile

July, 2024.
