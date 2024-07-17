# Evaporation of stratified water-oil mixture
Day 2 tutorial - Solution

You are expected to adapt the multicomponent evaporation case to the evaporation of a stratified water-oil mixture un a saucepan. This folder has exactly the same files as the tutorial available in `tutorials/multiphase/icoReactingMultiphaseInterFoam`. As you progress through the case, make sure you document your changes. This can be easier creating your own fork of the repository in GitHub. If you are not sure how to do this, do not worry, just add comments to the code.

## Case description
Consider a cylindrical saucepan with internal diameter $d_i = 0.118$ m and height $H=0.12$ m. The system is filled to 50% of its capacity with water. On top of the liquid layer, a canola oil layer is located that uses 25% of the volume of the saucepan. The saucepan is heated from the bottom at a low power, with a flux equal to $q = 16459.6$ W m $^{-2}$. It is of interest to develop a CFD model for this system with the following objectives:

- Visualise the boiling dynamics in terms of velocity,  volume fraction and temperature fields.
- Calculate the flow rate of water that leaves the saucepan as a function of time.

## Physical model

### How is the saucepan being heated? 
Look at the literature or comercial data for cook-tops and estimate a temperature superheating or heat flux. List your assumptions. Identify the boundaries of the system.

### What would be the initial temperature?

Multiphase simulations tend to be slow. We suggest you to start with liquid water at its boiling point at atmospheric pressure, $ T|_{t=0} = 373.15 K$. This choice allows to observe a short transient period of superheating for the onset of the evaporation.

### What will be the domain?

Choose the smallest possible saucepan and register its internal diameter and height. Model the saucepan as a vertical cylinder to simplify mesh generation. 

### What is the temperature of the surroundings?

We will assume that the temperature of the surroundings is 18ºC, which is the UK government recommendation of internal heating to achieve a balance between thermal comfort and cost savings. This will correspond to the initial temperature of the air and the boundary condition at the end of the vapour domain.

### Will you consider the humidity?

As we expect the onset of nucleate boiling, the main driver of the evaporation will be the heat influx. This will be much higher than the water concentration gradient. Therefore, it is safe to assume that the air is dry as this won't affect significantly the evaporation rate. 

Just for reference, the internal humidity of the house of 40%. This corresponds to the average value of a well-heated, well-insulated house. 

## Numerical model

Now that you have developed

- Boundary condition: try with [externalWallHeatFluxTemperature](https://www.openfoam.com/documentation/guides/latest/api/classFoam_1_1externalWallHeatFluxTemperatureFvPatchScalarField.html#details)

### Meshing
An efficient approach is to take the m4 file from day 1 that is a template to create cylindrical meshes. This file is hard coded, so it requires tuning and modification. A rule of thumb is to keep the proportions of the square side half, curvature and radius. The original values are 0.5:0.6:1. Rescaling to a radius of 0.059, we obtain 0.0295:0.0354:0.059. 

```
convertToMeters 1;

// Inner square side half
define(s, 0.0295)

// Inner square side curvature
define(sc, 0.0354)

// cylinder radius
define(r, 0.059)

// Height of cylinder
define(z, 0.12)

// Base z
define(Zb, 0)

// Outlet z
define(Zt, calc(Zb + z))

// Number of cells at inner square
define(Ns, 30)

// Number of cells between inner square and circle
define(Ni, 15)

// Number of cells in the cylinder height
define(Nz, 30)
```

- Modify the "inlet" boundary name to "bottom". This has the advantage to be located at a vertical coordinate $z=0$, consistent with the bottom of the saucepan.

```
patches
(
    wall walls
    (
        btQuad(r0, r3)
        btQuad(r1, r0)
        btQuad(r2, r1)
        btQuad(r3, r2)
    )
    
    wall bottom
    (
        bottomQuad(s3, s0, s1, s2)
        bottomQuad(s3, r3, r0, s0)
        bottomQuad(s2, r2, r3, s3)
        bottomQuad(s1, r1, r2, s2)
        bottomQuad(s0, r0, r1, s1)
    )
    
    patch top
    (
        topQuad(s3, s0, s1, s2)
        topQuad(s3, r3, r0, s0)
        topQuad(s2, r2, r3, s3)
        topQuad(s1, r1, r2, s2)
        topQuad(s0, r0, r1, s1)
    )
);
```


Perform `m4 blockMeshDict.m4 > blockMeshDict`, then change the working directory to the root case and execute `blockMesh`. This will create the new mesh.


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
- p_rgh: set the pressure minus the hydrostatic head to internalField equal to the atmospheric pressure, p = 101325 Pa. Set the boundary conditions at the walls and bottom as fixedFluxPressure. Set the boundary condition at the saucepan top as totalPressure. as this tends to give more stability to the simulation.
- T: Set the internal field (initial condition) to 373.15, which is equivalent to 80ºC. The boundary conditions are more complex:
- Bottom: set a superheat ot 12ºC with respect to the boiling temperature of water. This corresponds to 385.15 K.
- Walls: in reality the surrounding air will be cooling the saucepan. For an initial approximation, this effect will be neglected and a zeroGradient boundary condition will be set.
- U: set bottom and walls as noSlip, and otp as pressureInletOutletVelocity.
- vapour.gas: Set bottom and walls boundaries as zeroGradient, and top as inletOutlet.

### constant

#### g
It is important to change the constant/g file to have the gravity pointing downwards in the $z$ direction and not in the $y$ direction.

```
FoamFile
{
    version     2.0;
    format      ascii;
    class       uniformDimensionedVectorField;
    object      g;
}
// * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //

dimensions      [0 1 -2 0 0 0 0];
value           (0 0 -9.81);
// *****

```


#### thermophysicalProperties.gas
Directly reuse the file from session 2.2, as in the simulation it will be assumed that no oil evaporates. If oil would evaporate, the thermophysical properties of the oil vapour must be added.
 
#### thermophysicalProperties.liquid
It is important to update hte Prandtl number, viscosity, and Prandtl number. A reference kappa (thermal conductivity) is included but OpenFOAM calculates it as $k = \mu c_p/Pr $.


```
thermoType
{
    type            heRhoThermo;
    mixture         pureMixture;
    transport       const;
    thermo          hConst;
    equationOfState rhoConst;
    specie          specie;
    energy          sensibleEnthalpy;
}

mixture
{
    specie
    {
        nMoles          1;
        molWeight       18.9;
    }
    equationOfState
    {
        rho             1000;
    }
    thermodynamics
    {
        Cp              4181;
        Hf              -2.45e6; //[J/Kg]
    }
    transport
    {
        mu              0.282e-3;
        Pr              1.75;
        kappa           0.679;
    }
}
```

#### thermophysicalProperties.oil

Non-clarified canola oil has a boiling temperature of 107 deg C. Its thermophysical properties are the following:

```
thermoType
{
    type            heRhoThermo;
    mixture         pureMixture;
    transport       const;
    thermo          hConst;
    equationOfState rhoConst;
    specie          specie;
    energy          sensibleEnthalpy;
}

mixture
{
    specie
    {
        nMoles          1;
        molWeight       876;
    }
    equationOfState
    {
        rho             920;
    }
    thermodynamics
    {
        Cp              1900;
        Hf              -3.676e5; //[J/Kg]
    }
    transport
    {
        mu              65e-3;
        Pr              130;
    }
}
```

### phaseProperties
This dictionary establishes properties of interface and phase change. There are three phases and three possible combinations of two phases. In the massTransferModel sub-dictionary, the Lee empirical model is used to calculate the evaporation rate. The exact formula that computes this quantity can be found at the OpenFOAM documentation of the class header, [Lee.H](https://www.openfoam.com/documentation/guides/latest/api/Lee_8H_source.html)

$$
\dot{m} = C \rho \alpha \frac{(T - T_{activate})}{T_{activate}}
$$

For water evaporation at moderate heat fluxes, an evaporation coefficient $C = 0.1$ is appropriate. Higher values will overestimate the evaporation rate. It will be assumed that liquid nad oil are pure phases, and that the gas is a water-air mixture. The interfacial properties were obtained from the literature. Note that for the Lee model of evapration, `Tactivate` corresponds to the saturation temperature of the fluid of interest. This must be specified for each liquid phase.

```

type    massTransferMultiphaseSystem;

phases  (liquid gas oil);

liquid
{
    type            pureMovingPhaseModel;
}

oil
{
    type            pureMovingPhaseModel;
}

gas
{
    type            multiComponentMovingPhaseModel;
}

surfaceTension
(
    (gas and liquid)
    {
        type            constant;
        sigma           0.07;
    }
    
    (oil and liquid)
    {
        type            constant;
        sigma           0.005;
    }

    (gas and oil)
    {
        type            constant;
        sigma           0.07;
    }
);

massTransferModel
(
    (liquid to gas)
    {
        type            Lee;
        species         vapour.gas;
        C               0.1;
        Tactivate       373;
    }
    (oil to gas)
    {
        type            Lee;
        species         vapour.gas;
        C               0.1;
        // Unrefined canola oil
        Tactivate       380;
    }
);
```


### system

#### setFieldsDict
Modify the default dictionary to set a liquid filling equal to 75% of the height of the cylinder:


```
defaultFieldValues
(
    volScalarFieldValue alpha.gas   1
    volScalarFieldValue alpha.liquid 0
    volScalarFieldValue alpha.oil 0
);

regions
(
    boxToCell
    {
        box (-1 -1 -1) (1 1 0.06);
        fieldValues
        (
            volScalarFieldValue alpha.liquid 1
            volScalarFieldValue alpha.gas    0
            volScalarFieldValue alpha.oil    0
        );
    }

    boxToCell
    {
        box (-1 -1 0.06) (1 1 0.089);
        fieldValues
        (
            volScalarFieldValue alpha.oil 1
            volScalarFieldValue alpha.gas    0
            volScalarFieldValue alpha.liquid    0
        );
    }
);

```

#### turbulenceProperties
Reutilise directly the file from session 2.2. We won't analyse in depth the turbulence models for multiphase mixtures, and will use $k-\epsilon$ as a default model.


## PostProcessing

### ParaView volume visualisation
In ParaView, the fields can be visualised in several ways. The most frequent visualisation is `surface`, where the values at the surfaces of the domain, cells and subdomains are displayed. For 3-D simulations, it is useful to use the volume representation. In particular, if the field has the value 0 in a particular location, it is shown as transparent. This is useful for multiphase simulations as it allows to observe the interplay between the phases.

### Slice
Observe how the phases evolve in a representative slice at $\theta = \theta_0$. In this scenario, the azimuthal symmetry does not occur for every angle, so depending on the location of the plane you will obtain slightly different results.


### Integrated quantities

An order-of-magnitude verification test can be performed by comparing the mass flow that leaves the saucepan with the theoretical prediction of pure water evaporation:

$$ \dot{m}_{e} \approx \frac{\dot{Q}}{\Delta H_{LV}} = \frac{180 W}{2.45 \times 10^6 J/kg} = 7.34 \times 10^{-5} kg/s$$

You can compare this value with the water that leaves the saucepan using the following steps in ParaView:

1. Create a foam.foam dummy file and open ParaView through `paraview foam.foam`
2. Use a calculator to creata a new variables that represents the mass flux, $ \dot{m}_e = \rho v_z \omega_w \alpha_V $, based on Cell Data. Name this quantity `massflux`

3. In the pipeline browser, select the boundary `top`.
4. Execute the filter `IntegrateVariables` over the `top` boundary, based on Cell Data. Do not check the cell "divide data by cell volume". You will obtain a table with one row with the integrated properties over the boundary surface.
5. Clic on the row produced by `IntegrateVariables` and execute the filter `Plot Data Over Time`,
6. In the menu at the bottom left, select only `massflux` as the quantity to be ploted.
7. Compare the values obtained at different times with the theoretical predictions. What could be the causes of the differences?

After running the case setup in this folder for  30 s, the flow of water vapour that leaves the sauce pan is $ \dot{m}_{out} = 7.46 \times 10^{-6}$ kg/s. The lower mass flow is expected as there are a number of heat losses in the way of the water bubbles to the interface that produce their recondensation. When the bubbles reach the oil layer, they transfer sensible and latent heat to the oil, cooling and condensing a fraction of the bubbles.

Well done, you have completed the new `icoReactingMultiphaseInterFoam` tailored to Chemical Engineering and related disciplines!

If you want a challenge, you can try to run the case `waterOilEvaporationHeatFluxSmall`.








