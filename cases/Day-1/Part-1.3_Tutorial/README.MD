# Solar air heater
Day 1: Part 1.3 Tutorial - Solution

You are expected to modify a hot Room case from OpenFOAM to model a square channel in a solar air heater device.

This folder contains the solution for two cases:
 1. squareChannelConv_T_sol: Dirichlet boundary condition for temperature
 2. squareChannelConv_Robin_constantFlux_sol: Robin boundary condition to implement constant heat flux

We encourage you to try to develop the tutorial on your own and make use of this cases as supporting material if needed.

## Set up a new case folder

Start by making a copy of the hotRoom tutorial available in "tutorials/heatTransfer/buoyantBoussinesqPimpleFoam" to this folder and change the folder name to "squareChannelConv_T".

`cp -r $FOAM_TUTORIALS/heatTransfer/buoyantBoussinesqPimpleFoam/hotRoom .`

`mv hotRoom/ squareChannelConv_T`

`cd squareChannelConv_T/`

Now your in your case folders and ready to start modifying the case.

As you progress through the case, make sure you document your changes. A simple way to do this is to add comments to the code.
For comments use  characters "//" within the OpenFOAM dictionaries and "#" within the Allrun and Allclean files, at the start of each line that you would like to comment.

## Case description
Air is heated as it flows through an square channel within a solar heater device. In a portion of the top surface of the channels, an absorber plate absorbes solar radiation and transfers the heat to the air flowing throught the square channels below. You can find [examples](https://doi.org/10.1016/j.energy.2019.116437) of similar devices in the literature. Here, we will considered a smaller channel for the purpose of this tutorial, and to decrease the computation time.

We will considered a single square channel section with a height of 2 mm, width of 2 mm an total length of 30 mm. Cool air enters the channel at a velocity of 0.5 m/s and heat up as it flows through the channel. The air is heated from the top channel wall with a constant solar power of 1300 W m $^{-2}$. It is of interest to develop a CFD model of this system to:
- Visualise the velocity and temperature profile within the channels.
- Calculate the mean temperature of the air leaving the channel as a function of time
- Estimate engineering quantities of interest for the system (e.g., Nusselt number)

## Physical model

### How is the air being heated? 
Part of the top wall of the square channel is made of an absorbent material that upon receiving solar power will tranfer the heat to the air flowing at a given velocity below this plate. This will provide a fixed heat flux of approximately 1300 W/m2 of solar power, but as a first case, we will considered setting up a case with a constant temperature $T^\star$ (Dirichlet boundary condition) at the heater surface (absorbent material) and after this we will modify the boundary condition to a constant heat flux. 

We will consider the following assumptions:
- The energy conversion efficiency of tha absorber plate is assumed to be equal to 100%. 
- The absorber plate zone is assumed to be rectangular, covering the a centered portion of the length of the channel top wall.
- All other channel walls are insulated, nd not heat flux is present.

Identify the relevant heater boundary and consider it as separately from the rest of the domain before building your geometry and mesh. 

### What would be the initial temperature?

We will assume an initial temperature of the air inside the channel will be equal to the its inlet temperature, which is of 20ºC. This will allow us to observe the transient period during which the air is heated inside the channel until i reaches a steady state temperature profile.

### What will be the domain?

To produce an accurate simulation and faster solving case, we will consider a small channel of dimension 2 x 2 x 30 mm $^{3}$. While the absorber plate has dimensions of 2 x 18 mm $^{2}$ and it is centered in the top channel wall.

For simplifation we will not considered any curvature in the channel, so we will build the geometry using rectangular cuboids and blockMesh.

### What is the temperature of the surroundings and temperature of the air at the inlet?

We will assume that the temperature of the surroundings is 27ºC. which will represent a hot summer day.

We will assume the air enters the channel at 20ºC. This is the temperature of the air when returnes to the solar heater device at a lower temperature, after being used for an agricultural process, for example.

### What is the inlet velocity of the cold air entering the square channel, and what regime do we expect?

We will considered slow velocity of 0.5 m/s. For this condition, estimate the Reynolds number, considering a density of air of 1.204 kg m $^{-3}$, a dynamic viscosity of 18.25 $\times$ 10 $^{-6}$ Pa s, and as characteristic length the hydraulic diameter of the square channel:

$$ D_h = \frac{4\times A}{P} = 0.002$$

$$ Re = \frac{\rho u_{in} D_h}{\mu} = \frac{1.204\times 0.5\times 0.002}{18.25e-6} = 65.97$$

Therefore, we are in a laminar regime.

## Numerical model

Lets consider the conservation equation to solve for momentum, mass and energy

[Add queation]

We require two boundary conditions cases:

- Dirichlet condition for temperature of 400 K at the heater wall, try with [fixedValue](https://www.openfoam.com/documentation/guides/latest/doc/guide-bcs-fixed-value.html)

- Robin condition: constant solar heat flux ($q$), try with [Mixed](https://www.openfoam.com/documentation/guides/latest/doc/guide-bcs-mixed.html)

$$ k \frac{\partial T}{\partial y}|_{y = H} = h (T|_{y = H} - T_\infin) + q$$


Check this [resource](https://www.cfdyna.com/Home/OpenFOAM/of_Tut_Web/of_convection-BC.pdf) for the general derivation of a convective heat transfer boundary condition with addition of a constant heat flux

### Meshing


### 0 Directory


### constant


### system

