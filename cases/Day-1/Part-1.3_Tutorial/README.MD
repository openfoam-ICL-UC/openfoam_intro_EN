# Solar air heater
Day 1 tutorial - Solution

You are expected to adapt the hotRoom case to model a square channel in a solar air heater. 
Start by making a copy of the hotRoom tutorial available in `tutorials/heatTransfer/buoyancyBousineqPimpleFoam` to this folder and change the folder name to `squareChannelConv_T`.

As you progress through the case, make sure you document your changes. A simple way to do this is to add comments to the code.
For comments use // within the dictionaries and # whitin the Allrun and Allclean files, at the start of each comment line.

## Case description
Air is heated as it flows through an square channel within a solar heater device. The top surface of the channels is made of an absorber plate that absorbes solar radiation and transfers the heat to the air flowing throught the channels.

## Physical model

### How is the air being heated? 
Part of the top wall of this square channel is made of an absorbent material that...

The air will be heated by a section of the top channel wall. This will provide a fixed heat flux of approximately 1300 W/m2 of solar power, but as a first case we will start setting up the absorber plate at a constant temperature (Dirichlet boundary condition)

We will consider the following assumptions:
- The energy conversion efficiency of tha absorber plate is assumed to be equal to 100%. 
- The absorber plate zone is assumed to be rectangular, covering a portion of the length of the channel.

Identify the relevant boundary and consider it as separately from the rest of the domain before building your geometry and mesh. 

### What would be the initial temperature?

We will assume an initial temperature of the air inside the channel will be equal to the its inlet temperature of 20ºC to observed tha transient period during which is heated inside the channel.

### What will be the domain?

To produce an accurate simulation and faster solving case, we will consider a small channel of dimension 2 x 2 x 30 [mm]. While the absorber plate has dimensions of 2 x 18 [mm] and it is centered in the top channel wall.

For simplifation we will not considered any curvature in the channel, so we will build the geometry using rectangular cuboids.

### What is the temperature of the surroundings and temperature of the air at the inlet?

We will assume that the temperature of the surroundings is 27ºC. which will represent a hot summer day.

We will assume the air enters the channel at 20ºC. This is the temperature of the air when returnes at a lower temperature to the solar heater, after being used for an agricultural process for example.

### What is the inlet velocity of the cold air entering the square channel, and what regime do we expect?

We will use a velocity of 0.5 m/s.

Estimate the Reynolds number, considering a density of air of 1.204 kg/m3, a dynamic viscosity of  , and as characteirstic length the hydraulic diameter of the square channel

$$ D_h = \frac{4\times Area}{Perimeter} = \frac{4\times (0.002\times 0.002)}{0.008} = $$

$$ Re = \frac{\rho u_{in} D_h}{\mu} = \frac{1.204\times 0.5\times 0.002}{} = $$

The Re << 2300, therefore we are in a laminar regime.

## Numerical model

Lets consider the conservation equation to solve for momentum, mass and energy

[Add queation]

We require to consider boundary conditions

- Boundary condition: try with [externalWallHeatFluxTemperature](https://www.openfoam.com/documentation/guides/latest/api/classFoam_1_1externalWallHeatFluxTemperatureFvPatchScalarField.html#details)

### Meshing
We will build the mesh isung the blockMeshDict

An efficient approach is to take the m4 file from day 1 that is a template to create cylindrical meshes. This file is hard coded, so it requires tuning and modification. A rule of thumb is to keep the proportions of the square side half, curvature and radius. The original values are 0.5:0.6:1. Rescaling to a radius of 0.059, we obtain 0.0295:0.0354:0.059. 

- Modify the "inlet" boundary name to "bottom". This has the advantage to be located at a vertical coordinate is $z=0$, consistent with the bottom of the roof. It is important to change the constant/g file to have the gravity pointing downwards in the $z$ direction and not in the $y$ direction.

Perform `m4 blockMeshDict.m4 > blockMeshDict", then execute blockMesh from the root case directory. This will create the new mesh.

### 0 Directory
- Modify each field dictionary to include only the bottom, top and walls dictionary.

- air.gas: assume that the vapour phase is air.
- alpha.gas: the boundary condition at the top is pure air. The phase fraction in the rest of the tank will be set to zero and modified later with setFields.
- alpha.liquid assume internalField uniform 1 everywhere, and a boundary condition of 0 at the top.
- alphat: set walls with the original values of alphatWallFunction, and top with calculated.
- epsilon: set walls with the default wall function and top with inletOutlet.
- k: set the turbulent kinetic energy at the walls with the default wall function and top with inletOutlet.
- nut: set the rate of change of turbulent viscosity as the default wall function at the walls and bottom, and calculated at the top.
- p: set the pressure internalField equal to the atmospheric pressure, p = 101325 Pa, and leave all other boundary conditions as calculated.
- p_rgh: set the pressure minus the hydrostatic head to internalField equal to the atmospheric pressure, p = 101325 Pa. Set the boundary conditions at the walls and bottom as fixedFluxPressure. Set the boundary condition at the tnak top as totalPressure. as this tends to give more stability to the simulation.
- T: Set the internal field (initial condition) to 353.15, which is equivalent to 80ºC. The boundary conditions are more complex:
- Bottom: set a superheat ot 10ºC with respect to the boiling temperature of water. This corresponds to 383.15 K.
- Walls: in reality the surrounding air will be cooling the saucepan. For an initial approximation, this effect will be neglected and a zeroGradient boundary condition will be set.
- U: set bottom and walls as noSlip, and otp as pressureInletOutletVelocity.
- vapour.gas: Set bottom and walls boundaries as zeroGradient, and top as inletOutlet.

### constant
- Set ethanol-water mixture

### system

#### setFieldsDict
Modify the default dictionary to set a liquid filling equal to 75% of the height of the cylinder:


```
regions
(
    boxToCell
    {
        box (0 0 0) (1 1 0.09);
        fieldValues
        (
            volScalarFieldValue alpha.liquid 1
            volScalarFieldValue alpha.gas    0
        );
    }
);
```