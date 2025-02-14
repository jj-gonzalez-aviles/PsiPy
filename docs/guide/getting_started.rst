Getting started
===============

psipy is a package for loading and visualising the output of PSI's MAS model
runs. This page provides some narrative documentation that should get you up
and running with obtaining, loading, and visualising some model results.

Getting data
------------
The PSI `MHDWeb pages`_ give access to MAS model runs. The runs are indexed by
Carrington rotation, and for each Carrington rotation there are generally a
number of different runs, varying in the type model run and/or
the boundary conditions.

To load data with psipy you need to manually download the files you are
interested in to a directory on your computer.

.. _MHDWeb pages: http://www.predsci.com/mhdweb/data_access.php

Loading data
------------
psipy stores the output variables from a single MAS run in the `MASOutput`
object. To create one of these, specify the directory which has all of the
output ``.hdf`` files you want to load:

.. code-block:: python

    from psipy.model import MASOutput

    directory = '/path/to/files'
    mas_output = MASOutput(directory)

It is assmumed that the files have the filename structure
``'{var}{timestep}.hdf'``, where ``var`` is a variable name, and ``timestep``
is a three-digit zero-padded integer timestep.

To see which variables have been loaded, we can look at the ``.variables``
attribute:

.. code-block:: python

    print(mas_output.variables)

This will print a list of the variables that have been loaded. Each individual
variable can then be accessed with square brackets, for example to get the
radial magnetic field component:

.. code-block:: python

    br = mas_output['br']

This will return a `Variable` object, which stores the underlying data as a
`xarray.DataArray` under the `Variable.data` property.

Data coordinates
----------------
The data stored in `Variable.data` contains the values of the data as a normal
array, and in addition stores the coordinates of each data point.

MAS model outputs are defined on a 3D grid of points on a spherical grid. The
coordinate names are ``'r', 'theta', 'phi'``. The coordinate values along each
dimension can be accessed using the ``r_coords, theta_coords, phi_coords``
properties, e.g.:

.. code-block:: python

  rvals = br.r_coords

Sampling data
-------------
Variable objects have a `Variable.sample_at_coords` method, to take a sample of
the 3D data cube along a 1D trajectory. This is helpful for flying a 'virtual
spacecraft' through the model, in order to compare model results with in-situ
measurements.

`sample_at_coords` requires arrays of longitude, latitude, and radial distance.
Given these coordinates, it uses linear interpolation to extract the values
of the variable at each of the coordinate points.

For an example of how all this works, see :ref:`sphx_glr_auto_examples_sampling_plot_in_situ_comparison.py`.

Field line tracing
------------------
The `streamtracer` library is used to trace magnetic field lines through
models. Note that this must be installed prior to use (pip install streamtracer).
In spherical coordinates the streamline equations are:

.. math:: \frac{dr}{ds} = \hat{B}_{r}
.. math:: \frac{d\theta}{ds} = \frac{\hat{B}_{\theta}}{r}
.. math:: \frac{d\phi}{ds} = \frac{\hat{B}_{\phi}}{r\cos(\theta)}

To trace magnetic field lines the :class:`~psipy.tracing.FortranTracer` can be used.
From a set of seed points with defined radius, longitude, latitude, the tracer
is called to trace field lines from these points:

.. code-block:: python

  import astropy.units as u
  from psipy.tracing import FortranTracer
  tracer = FortranTracer()

  # Radius
  r = [40, 45]
  lat = [0, 10] * u.deg
  lon = [0, np.pi / 4] * u.rad
  xs = tracer.trace(model, r=r, lat=lat, lon=lon)

The tracer has two configurable options:

- ``max_steps`` is the maximum number of steps that an individual field line
  can have. This is set to ``'auto'`` by default, which will allocate four
  times the steps needed to travel radially from the inner to the outer
  boundary of the model.
- ``step_size`` is the size of individual steps along the field line, as a
  multiple of the radial cell size. This is set to ``1`` by default.

For a full example see :ref:`sphx_glr_auto_examples_tracing_tracing_pyvista.py`.
