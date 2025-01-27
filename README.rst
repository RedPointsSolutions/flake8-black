flake8-black
============

.. image:: https://img.shields.io/pypi/v/flake8-black.svg
   :alt: Released on the Python Package Index (PyPI)
   :target: https://pypi.python.org/pypi/flake8-black
.. image:: https://img.shields.io/conda/vn/conda-forge/flake8-black.svg
   :alt: Released on Conda
   :target: https://anaconda.org/conda-forge/flake8-black
.. image:: https://img.shields.io/travis/peterjc/flake8-black/master.svg
   :alt: Testing with TravisCI
   :target: https://travis-ci.org/peterjc/flake8-black/branches
.. image:: https://img.shields.io/pypi/dm/flake8-black.svg
   :alt: PyPI downloads
   :target: https://pypistats.org/packages/flake8-black
.. image:: https://img.shields.io/badge/code%20style-black-000000.svg
   :alt: Code style: black
   :target: https://github.com/python/black

Introduction
------------

This is an MIT licensed `flake8 <https://gitlab.com/pycqa/flake8>`_ plugin
for validating Python code style with the command line code formatting tool
`black <https://github.com/python/black>`_. It is available to install from
the Python Package Index (PyPI):

- https://pypi.python.org/pypi/flake8-black

Black, *"The Uncompromising Code Formatter"*, is normally run to edit your
Python code in place to match their coding style, a strict subset of the
`PEP 8 style guide <https://www.python.org/dev/peps/pep-0008/>`_.

The point of this plugin is to be able to run ``black --check ...`` from
within the ``flake8`` plugin ecosystem. You might use this via a ``git``
pre-commit hook, or as part of your continuous integration testing.

Flake8 Validation codes
-----------------------

Early versions of flake8 assumed a single character prefix for the validation
codes, which became problematic with collisions in the plugin ecosystem. Since
v3.0, flake8 has supported longer prefixes, therefore this plugin uses ``BLK``
as its prefix.

====== =======================================================================
Code   Description (*and notes*)
------ -----------------------------------------------------------------------
BLK100 Black would make changes.
BLK9## Internal error (*various, listed below*):
BLK900 Failed to load file: ...
BLK901 Invalid input.
BLK998 Could not access flake8 line length setting (*no longer used*).
BLK999 Unexpected exception.
====== =======================================================================

Note that if your Python code has a syntax error, ``black --check ...`` would
report this as an error. Likewise ``flake8 ...`` will by default report the
syntax error, but importantly it does not seem to then call the plugins, so
you will *not* get an additional ``BLK`` error.


Installation
------------

Python 3.6 or later is required to run ``black``, so that is recommended, but
``black`` can be used on Python code written for older versions of Python.

You can install ``flake8-black`` using ``pip``, which should install ``flake8``
and ``black`` as well if not already present::

    $ pip install flake8-black

Alternatively, if you are using the Anaconda packaging system, the following
command will install the plugin with its dependencies::

    $ conda install -c conda-forge flake8-black

The new validator should be automatically included when using ``flake8`` which
may now report additional validation codes starting with ``BLK`` (as defined
above). For example::

    $ flake8 example.py

You can request only the ``BLK`` codes be shown using::

    $ flake8 --select BLK example.py


Configuration
-------------

We assume you are familiar with `flake8 configuration
<http://flake8.pycqa.org/en/latest/user/configuration.html>`_ and
`black configuration
<https://black.readthedocs.io/en/stable/pyproject_toml.html>`_.

We recommend using the following settings in your ``flake8`` configuration,
for example in your ``.flake8``, ``setup.cfg``, or ``tox.ini`` file::

    [flake8]
    # Recommend matching the black line length (default 88),
    # rather than using the flake8 default of 79:
    max-line-length = 88
    extend-ignore =
        # See https://github.com/PyCQA/pycodestyle/issues/373
        E203,

Note currently ``pycodestyle`` gives false positives on the spaces ``black``
uses for slices, which ``flake8`` reports as ``E203: whitespace before ':'``.
Until `pyflakes issue 373 <https://github.com/PyCQA/pycodestyle/issues/373>`_
is fixed, and ``flake8`` is updated, we suggest disabling this style check.

Separately ``pyproject.toml`` is used for ``black`` configuration - if this
file is found, the plugin will look at the following ``black`` settings:

* ``target_version``
* ``skip_string_normalization``
* ``line_length``


Ignoring validation codes
-------------------------

Using the flake8 no-quality-assurance pragma comment is not recommended
(e.g. adding ``# noqa: BLK100`` to the first line black would change).
Instead use the black pragma comments ``# fmt: off`` at the start, and
``# fmt: on`` at the end, of any region of your code which should not be
changed. Or, exlude the entire file by name (see below).


Ignoring files
--------------

The plugin does *NOT* currently consider the ``black`` settings ``include``
and ``exclude``, so if you have certain Python files which you do not use
with ``black`` and have told it to ignore, you will *also* need to tell
``flake8`` to ignore them (e.g. using ``exclude`` or ``per-file-ignores``).


Version History
---------------

======= ============ ===========================================================
Version Release date   Changes
------- ------------ -----------------------------------------------------------
v0.1.0  2019-06-03   - Uses main ``black`` settings from ``pyproject.toml``,
                       contribution from `Alex <https://github.com/ADKosm>`_.
                     - WARNING: Now ignores ``flake8`` max-line-length setting.
v0.0.4  2019-03-15   - Supports black 19.3b0 which changed a function call.
v0.0.3  2019-02-21   - Bug fix when ``W292 no newline at end of file`` applies,
                       contribution from
                       `Sapphire Becker <https://github.com/sapphire-janrain>`_.
v0.0.2  2019-02-15   - Document syntax error behaviour (no BLK error reported).
v0.0.1  2019-01-10   - Initial public release.
                     - Passes ``flake8`` max-line-length setting to ``black``.
======= ============ ===========================================================


Developers
----------

This plugin is on GitHub at https://github.com/peterjc/flake8-black

To make a new release once tested locally and on TravisCI::

    $ git tag vX.Y.Z
    $ python setup.py sdist --formats=gztar
    $ twine upload dist/flake8-black-X.Y.Z.tar.gz
    $ git push origin master --tags

The PyPI upload should trigger an automated pull request updating the
`flake8-black conda-forge recipe
<https://github.com/conda-forge/flake8-black-feedstock/blob/master/recipe/meta.yaml>`_.
