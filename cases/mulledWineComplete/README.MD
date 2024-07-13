# Multicomponent evaporation of mulled wine
Day 2 tutorial - Solution

You are expected to adapt the multicomponent evaporation case to alcohol evaporation during the preparation of Mulled Wine. This folder has exactly the same files as the tutorial available in `tutorials/multiphase/icoReactingMultiphaseInterFoam`.

As you progress through the case, make sure you document your changes. This can be easier creating your own fork of the repository in GitHub. If you are not sure how to do this, do not worry, just add comments to the code.

## Case description
Mulled wine can be modelled as an ethanol-water mixture. Assume that the wine has a 14% V/V concentration of ethanol. This corresponds to an ethanol mass fraction of approximately 0.109. 

### Physical model

#### How is the wine being heated? 
The wine will be heated by the base through a modern electric hub. This will provide a fixed heat flux prescribed from realistic values and geometry. For the purpose of scaling, we will consider the [Kogan Induction Cooktop](https://www.kogan.com/au/buy/kogan-induction-cooktop/?utm_source=google&utm_medium=product_listing_ads&gad_source=1&gclid=EAIaIQobChMIi7vvlPWihwMV9aJmAh0YZgdREAQYASABEgLgd_D_BwE). The power of the four cooking zone is 7200 W. Each cooking zone has a diameter of 15 cm. We will consider the following assumptions:
- The energy conversion efficiency from electric power to heat is assumed to be equal to 100%. 
- The cooking zone is assumed to be a circle, and the totality of the area is active for heating.

$$ \dot{q} = \frac{\dot{Q}}{A} = \frac{(7200/4) \text{W}}{\pi (0.15m)^2/4} = 101.859 \hspace{0.1cm} \text{kW}/\text{m}^2$$

Identify the relevant boundary and consider it as separately from the rest of the domain before building your geometry and mesh. 

#### What would be the initial temperature?

We will assume an initial temperature of the liquid mixture of 80ºC. This choice allows to observe a short transient period of liquid heating, and then to observe the ongoing of the evaporation.

#### What will be the domain?

To produce an accurate simulation, we will consider a small saucepan. We will consider the standard of [stellar.co.uk](https://stellar.co.uk/knowledge-base/what-are-the-full-dimensions-of-the-pans):

$$ d_i = 0.118 \hspace{0.1cm} \text{m}, H = 0.12 \hspace{0.1cm} \text{m} $$

Note that for these dimensions, we just used the base diameter. In reality, a saucepan diameter is larger than the base diameter and it has a small curvature near the base. This would require a complex geometry that will go beyond the scope of this exercise.

#### What is the temperature of the surroundings?

We will assume that the temperature of the surroundings is 18ºC, which is the UK government recommendation of internal heating to achieve a balance between thermal comfort and cost savings. This will correspond to the initial temperature of the air and the boundary condition at the end of the vapour domain.

#### Will you consider the humidity?

We will consider a internal humidity of the house of 40%. This corresponds to the average value of a well-heated, well-insulated house. 

### Numerical model

Now that you have developed

- Boundary condition: try with [externalWallHeatFluxTemperature](https://www.openfoam.com/documentation/guides/latest/api/classFoam_1_1externalWallHeatFluxTemperatureFvPatchScalarField.html#details)
