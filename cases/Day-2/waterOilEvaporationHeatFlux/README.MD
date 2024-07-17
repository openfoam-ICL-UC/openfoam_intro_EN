# Multicomponent evaporation of mulled wine
Day 2 tutorial - Solution

You are expected to adapt the multicomponent evaporation case to the evaporation of a stratified water-oil mixture un a saucepan. This folder has exactly the same files as the tutorial available in `tutorials/multiphase/icoReactingMultiphaseInterFoam`. As you progress through the case, make sure you document your changes. This can be easier creating your own fork of the repository in GitHub. If you are not sure how to do this, do not worry, just add comments to the code.

## Case description
Consider a cylindrical saucepan with internal diameter $d_i = 0.118$ m and height $H=0.12$ m. The saucepan is heated from the bottom at full power at a flux equals to $q = 101859 W m^{-2}$

Mulled wine can be modelled as an ethanol-water mixture. Assume that the wine has a 14% V/V concentration of ethanol. This corresponds to an ethanol mass fraction of approximately 0.109. 

## Physical model

### How is the wine being heated? 
The wine will be heated by the base through a modern electric hub. This will provide a fixed heat flux prescribed from realistic values and geometry. For the purpose of scaling, we will consider the [Kogan Induction Cooktop](https://www.kogan.com/au/buy/kogan-induction-cooktop/?utm_source=google&utm_medium=product_listing_ads&gad_source=1&gclid=EAIaIQobChMIi7vvlPWihwMV9aJmAh0YZgdREAQYASABEgLgd_D_BwE). The power of the four cooking zone is 7200 W. Each cooking zone has a diameter of 15 cm. We will consider the following assumptions:
- The energy conversion efficiency from electric power to heat is assumed to be equal to 100%. 
- The cooking zone is assumed to be a circle, and the totality of the area is active for heating.

$$ \dot{q} = \frac{\dot{Q}}{A} = \frac{(7200/4) \text{W}}{\pi (0.15m)^2/4} = 101.859 \hspace{0.1cm} \text{kW}/\text{m}^2$$

Identify the relevant boundary and consider it as separately from the rest of the domain before building your geometry and mesh. 

### What would be the initial temperature?

We will assume an initial temperature of the liquid mixture of 80ºC. This choice allows to observe a short transient period of liquid heating, and then to observe the ongoing of the evaporation.

### What will be the domain?

To produce an accurate simulation, we will consider a small saucepan. We will consider the standard of [stellar.co.uk](https://stellar.co.uk/knowledge-base/what-are-the-full-dimensions-of-the-pans):

$$ d_i = 0.118 \hspace{0.1cm} \text{m}, H = 0.12 \hspace{0.1cm} \text{m} $$

Note that for these dimensions, we just used the base diameter. In reality, a saucepan diameter is larger than the base diameter and it has a small curvature near the base. This would require a complex geometry that will go beyond the scope of this exercise.

### What is the temperature of the surroundings?

We will assume that the temperature of the surroundings is 18ºC, which is the UK government recommendation of internal heating to achieve a balance between thermal comfort and cost savings. This will correspond to the initial temperature of the air and the boundary condition at the end of the vapour domain.

### Will you consider the humidity?

We will consider a internal humidity of the house of 40%. This corresponds to the average value of a well-heated, well-insulated house. 

## Numerical model

Now that you have developed

- Boundary condition: try with [externalWallHeatFluxTemperature](https://www.openfoam.com/documentation/guides/latest/api/classFoam_1_1externalWallHeatFluxTemperatureFvPatchScalarField.html#details)

### Meshing
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