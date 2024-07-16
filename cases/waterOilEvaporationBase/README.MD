# Multicomponent evaporation of mulled wine
Day 2 tutorial

You are expected to adapt the multicomponent evaporation case to alcohol evaporation during the preparation of Mulled Wine. This folder has exactly the same files as the tutorial available in `tutorials/multiphase/icoReactingMultiphaseInterFoam`.

As you progress through the case, make sure you document your changes. This can be easier creating your own fork of the repository in GitHub. If you are not sure how to do this, do not worry, just add comments to the code.

## Case description
Mulled wine can be modelled as an ethanol-water mixture. Assume that the wine has a 14% V/V concentration of ethanol. This corresponds to an ethanol mass fraction of approximately 0.109. 

### Physical model
Before starting to touch a single file of this directory, you must write a pyhsical model description. This description often has a list of assumptions, a diagram of the physical system, and the equations to be solved. It is not necessary for you to write all the equations, but you must identify the non-standard constituve relationships that are relevant for multicomponent evaporation. In setting up your scenario, consider the following questions to aid you on the development of the physical model:

- How is the wine being heated? 

Identify the relevant boundary and consider it as separately from the rest of the domain before building your geometry and mesh. 

- What would be the initial temperature?

 It can be very interesting to explore how the ethanol evaporate during the pre-heating of the mixture. However, it is highly likely that the simulation would take a long time. Decide whether you want to explore the pre-heating or the ethanol evolution once mixture has achieved its saturation temperature.

- What will be your domain?

Decide whether you will consider only a saucepan and its vapour, or whether you will also include the room. We recommend that if you are a beginner or even an intermediate user, stick to the saucepan. If you want a challenge, consider a broader domain to extend your results to non-controlled environments. In any case, you must identify the dimensions of the domain. If you model a saucepan as a vertically orientated cylinder saucepan, what would be its diameter and height?

- What is the temperature of the surroundings?
This will be relevant for the initial condition of the air above the liquid mixture, and potentially a boundary condition.

- Will you consider the humidity?

As the evaporation progresses, both ethanol and water will continuosuly evaporate. The driving force of evaporation will depend on the concentration gradient of both components. If you don't have a background on mass transfer or psycrhometry, assume that the air is completely dry.

Share your physical model with the person next to you and discuss over the assumptions.


### Numerical model

Now that you have developed