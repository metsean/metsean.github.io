---
layout: post
title: Part 2. Building a model of local wind effects
tags: [CFD, wind]
categories: [regional wind model]
image: cfdplot.png
excerpt_separator: <!--more-->

---

## Summary

This post describes the construction of a Compuational Fluid Dynamic (CFD) model of the Wellington region which gives local detail of the windflow around topography. This model will be used in Part 3 of this blog series to scale the refernce site wind distribution and generate local wind environment statistics.<!--more-->


## What's CFD?

The wind flow across some region can be modelled using the technique of computational fluid dynamics (CFD). This method calculates how the wind flows through space and around obstructions. It like a virtual wind tunnel and provided the model is correctly constructed, it should reproduce the local wind effects caused by the terrain. CFD simulations are quite complicated beasts and I will only try here to give a quick overview of the process. 

## Building a CFD model


The first step in constructing a CFD model is to define the relevant physics which must be solved for. For this work is was assumed that the lower atmosphere could be modelled as an incompressible homogeneous layer. The Navier Stokes equations, which describe fluid dynamics, will be solved in their Reynolds-averaged forumulation to give a steady state solution of velocity, pressure, and the turbulent kinetic energy, rate of dissipation and viscosity for discrete volumes within the model. The OpenFoam CFD package was used for this simulation. 

The model extent must next be defined. For this work the basic model size was a rectangle 12km x 12km x 960m high. This work could be easily scaled up for larger regional models - however the simulation is computationally intense and larger models would take longer to solve.

The model was centered on Wellington city and rotated through 16 orientations for wind directions N, NNE, NE etc. The projections are shown on the map image below. The white cross is the location of the reference site in the model

![model extent and orienation]({{ site.baseurl }}/images/modelRotation.png "model extent and orientation")

The bottom boundary of the model is the terrain - the data source for this is the LINZ 8m Digital Elevation Model. To work with the CFD software this elevation model needs to be converted to a stl standard 3d model file. There is a script available for doing this provided here. This layer has an imposed boundary condition of no-slip in the model - this means the boundary velocity is zero. This same no-slip boundary condition is imposed for the top and sides of the model. For the inlet a log wind profile is applied and the outlet is set as zero pressure .

The model domain is then discretized into what is mostly a mostly hexahedral mesh using the snappyHexMesh utility. The base mesh has a 40m resolution but refines down to 10m as it approaches the terrain surface. The mesh completes with two layers aligned to the boundary surface. The mesh is approximately 20 million cells in size.

![mesh]({{ site.baseurl }}/images/mesh.png "model mesh")

The CFD models require quite a bit of computational muscle to complete. For this work the simulations were performed on an AWS c5 4xlarge machine - this allows for parallel processing across 8 cores and has ample memory for the model size. The model solutions converged after approximately 1000 iterations (2 hours).

For this work we are only interested in wind speeds near the ground surface and so we can take a slice from the full 3D solution. It is important not to take the solution from the cell nearest the boundary, and so, using paraview (a visualising and analyzing tool) we take slices at a height of 20m above the surface. Once such slice is shown below - the color scale gives the magnitude of the velocity and the arrows give the vector direction. Regional effects are evident. Directional changes are shown by the arrows and the wind speeds are highest on the hill top.

![cfd image]({{ site.baseurl }}/images/cfdplot.png "CFD model image")

The surface slices were exported from paraview for the 16 different wind directions. From these datasets python interpolation objects were generated which give bivariate spline approximations to the surface velocity magnitude and direction. This were generated using the scipy package interpolate.RectBivariateSpline class giving for each wind direction functions _fVi(x,y)_ and _fthetai(x,y)_ which described the velocity magnitude and direction at location coordinates _x,y_. 
