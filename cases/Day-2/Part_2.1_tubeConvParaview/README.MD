## TubeConv

Example of fluid flow and heating by forced convection.

- In this example, the use of OpenFOAM is illustrated for the laminar flow of liquid nitrogen in a microchannel.
- The temperature of the surroundings is set to $T_{\infty} = 298.15 K$
- The liquid nitrogen is heated through the walls. The wall temperature is calculated using a mixed boundary condition in 0/T to implement the Robin boundary condition:
$$ k_{eff} \frac{\partial T}{\partial r}|_{r=R} = h_o(T-T_{\infty}) $$

### ParaView

- In this case, you will postProcess a simulation for the heating of liquid nitrogen in a circular channel.
- You will apply a number of ParaView filters to improve the visualisation of the results.
- The step by step solution and application of the filters is available in the GitHub Wiki associated with this session.

For in-person workshops, we give a printed handout to facilitate the progress of our students. As most students only have access to one screen, having a printed handout is useful. If you are learning this content individually, we suggest you to do it in two screens or print the handout.

### Solution files

In ParaView, it is possible to save a combination of filters and procedures in a ParaView state. These files have a .pvsm extension. In this folder, we provide two of such files:

- T_r0_rR.pvsm: Plot over line of the temperature at the tube center and at the tank wall.
- Uz.pvsm: Axial velocity proviles using a Glyph paraview filter on a cross section $-R <= r <= R at the tube entrance. The plot displayes three profiles simultaneously: at the entrance of the tube, $z=0$, at the mid-point, $z = l_z/2$. and at the exit of the pipe, $z = l_z$.
