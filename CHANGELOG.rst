Changelog
=========

Version 0.2.0
-------------
Support for multiple timesteps
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Support for reading in model outputs across multiple timesteps has been added,
where each timestep is stored in an individual file.
:func:`psipy.io.mas.read_mas_file` will automatically read in all the MAS
output files it finds in the given directory. The time coordinate values can be
queried with the new `Variable.time_coords` property of variables.

The following methods have been updated to support this:

- `Variable.sample_at_coords` now accepts a ``t`` argument
  to interpolate across timesteps.
- `Variable.plot_radial_cut`,
  `Variable.contour_radial_cut`,
  `Variable.plot_phi_cut`,
  `Variable.contour_phi_cut`,
  `Variable.plot_equatorial_cut`, and
  `Variable.contour_equatorial_cut` all now accept a
  ``t_idx`` argument, which is the time index at which to plot the cuts. This
  defaults to ``t_idx=0``.

When loading a set of netCDF files they will be lazily loaded along the time
dimension (ie. only one file will be read into memory at any one time).

When loading a set of HDF4 or HDF5 files all of them will be read into
memory, so beware loading lots of them! Support for lazy loading may be
implemented for HDF4 or HDF5 files in the future.

Animations
~~~~~~~~~~
`Variable.plot_radial_cut`, `Variable.plot_phi_cut`, and
`Variable.plot_equatorial_cut` now support animation creation. If multiple
timesteps are loaded in the `Variable` and the timestep isn't specified, a
:class:`~matplotlib.animation.Animation` object will be returend instead of
a single plot being created. See the example gallery for more information on
how to save the animation to disk.

Other new features
~~~~~~~~~~~~~~~~~~
- Added :func:`~psipy.io.mas.convert_hdf_to_netcdf` to convert a set of HDF
  files to netCDF files. This is useful for creating animations from large
  datasets, as psipy can keep track of a number of netCDF files without reading
  them all into memory at once.

Fixes
~~~~~
- Accessing a variable from a model output multiple times will now return the
  same object, instead of making two copies of the variable in memory.

Version 0.1.1
-------------
Added the ability to change the units and radial coordinates of a `Variable`.
There are two new examples showing how to do this in the example gallery.

Version 0.1
-----------
First PsiPy release.
