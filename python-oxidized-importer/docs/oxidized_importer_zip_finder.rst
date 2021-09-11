.. py:currentmodule:: oxidized_importer

.. _oxidized_importer_zip_finder:

======================================
``OxidizedZipFinder`` Meta Path Finder
======================================

``oxidized_importer`` contains a pure Rust implementation of a *meta path
finder* that can load Python resources from zip files. Its goal is to be
a compatible reimplementation of ``zipimport.zipimporter`` from the Python
standard library.

Usage
=====

Instances of :py:class:`OxidizedZipFinder` are bound to zip archive data.

Instances can be constructed by calling
:py:meth:`OxidizedZipFinder.from_zip_data` or
:py:meth:`OxidizedZipFinder.from_path`.

:py:class:`OxidizedZipFinder` is a *meta path finder* and instances should
be registered on ``sys.meta_path``. e.g.

.. code-block:: python

   import os
   import sys
   import oxidized_importer

   HERE = os.dirname(os.path.abspath(__file__))
   zip_path = os.path.join(HERE, "archive.zip")

   zip_importer = OxidizedZipFinder.from_path(zip_path)
   sys.meta_path.insert(0, zip_importer)

Once an instance is registered on ``sys.meta_path``, it will be consulted
when an ``import`` is serviced by Python's importing mechanism.

Behavior
========

:py:class:`OxidizedZipFinder` is similar to - but critically different from -
the standard library ``zipimport.zipimporter``.

:py:class:`OxidizedZipFinder` is a *meta path finder*, not a
*path entry finder*. This means instances are bound to ``sys.meta_path`` and not
``sys.path_hooks``. Support for enabling use as a *path hook* is planned.

All I/O and zip reading in :py:class:`OxidizedZipFinder` is implemented in
Rust. Subtle differences in behavior as a result of zip parsing implementations
could occur.

:py:class:`OxidizedZipFinder` doesn't yet implement support for resource
reading (e.g. the ``importlib.abc.ResourceReader`` interface). Only loading
of ``.py`` and ``.pyc`` files is supported.

:py:class:`OxidizedZipFinder` doesn't validate the header of ``.pyc``
files. If it sees a ``.pyc`` version of a module, its bytecode will be
used as-is. (``zipimport.zipimporter`` validates that the content in
the ``.pyc`` matches expectations.)

Support for opening just sub-directories within zip files is not
yet implemented.

Python API
==========

See :py:class:`OxidizedFinder` for the Python API documentation.
