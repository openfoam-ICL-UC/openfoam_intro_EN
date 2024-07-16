#### TubeConv

Example of fluid flow and heating by forced convection.

- In this example, the use of OpenFOAM is illustrated for the laminar flow of liquid nitrogen in a microchannel.
- The temperature of the surroundings is set to $T_{\infty} = 298.15 K$
- The liquid nitrogen is heated through the walls. The wall temperature is calculated using a mixed boundary condition in 0/T to implement the Robin boundary condition:
$$ k_{eff} \frac{\partial T}{\partial r}|_{r=R} = h_o(T-T_{\infty}) $$