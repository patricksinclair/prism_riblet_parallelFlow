This version is the one which will be used for the big runs now.  It's pretty similar to the previous version, but things have been tidied up a bit.
Mesh and geometry are now sorted.
This is the parallel flow, the riblets are aligned in the z-direction, and the flow is in the z-direction.

As a test, we'll set the side wall velocity boundary conditions to zeroGradient instead of slip, and see if this works.
The reason being, for the 45 degree flow case, there might be some biofilm that gets pushed to the side, so having them as zeroGradient
might stop there being any weird edge effects.
Or it might make things much worse, lets find out.




/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
This is the base system for creating 3D prism riblet systems.

Rudimentary way to visualise in 3D
use the threshold filter on alpha.biofilm and set the limits to 0.5 and 1.5, this will only plot biofilm
then add another threshold filter to the main case file, but don't change the limit and instead reduce the opacity.
this will show the biofilm in the internal system with a translucent box around it.
we can figure out a more aesthetic way for this later on.

For the dynamic meshing, we used the dynamicmeshdict from the dambreak with obstacle case. the dynamicMeshDict for the 2D case is still saved in the constant folder
for now.

///////////////////////////////////////////////////////////////

Currently varying the biofilm thickness and the riblet separation. Current riblet separations are 23, 46 and 92 microns.

Biofilm thicknesses are:

thick = 60 microns
thin = 21 microns
level = 42 microns

//////////////////////////////////////////////

For script submission, submit the mpi_script_interFoam one, and then for any further required runs, run the job_continiuity_script.

To continue a stopped openfoam job, the steps are as follows:

1. Change the startFrom keyword in system/controlDict as startTime -> latestTime.  This can be done via: sed -i '/startFrom/c\startFrom       latestTime;' system/controlDict
(Fixed the error where both occurrences of startTime were being replaced, resulting in the simulation restarting from 0.)
NOW INSTEAD OF THIS JUST USE THE job_continiuity_script SCRIPT
2. Just in case the job was stopped in the middle of filewriting, run this command to delete the latest time directories: foamListTimes -processor -latestTime -rm
3. Then run the solver as usual.  No need to reconstructPar anything.

////////////////////////////////////////////////////////////////////////

To use Cogan's method of calculating the drag, use the interpolater script to convert the dynamic refined mesh into a uniform one by utilisng the mapFields utility.
Then use the postProcessing -func "grad(U)" OpenFOAM utility.
Then export the volume and surface data to .csv files via paraview.
Then we can use the python scripts to calculate the time derivatives and volume/surface integrals.
