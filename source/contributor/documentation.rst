==========================
Contributing Documentation
==========================

.. WARNING:: This site documents Glimpse |release|. It is a work in progress, and still has a long way to go.

   For Glimpse v1 documentation, please visit `getglimpse.com/Docs <http://getglimpse.com/Docs/>`_.

Glimpse documentation is `stored in GitHub <https://github.com/glimpse/docs>`_ and is hosted on the `ReadTheDocs.org <https://github.com/glimpse/docs>`_ platform at `docs.getglimpse.com <http://docs.getglimpse.com/>`_.

Any individual page in the Glimpse documentation can be edited by clicking "Edit on GitHub" link at the top of the page.

For larger contributions, or to more quickly work with multiple pages, ReadTheDocs can be run locally.

Installing ReadTheDocs
======================

ReadTheDocs is based on the popular open source project `Sphinx <http://www.sphinx-doc.org/>`_. Sphinx is built in Python and leverages a powerful Markdown-like language called reStructuredText (reST). 

The steps to install are:

1. Download and Install Python 3.5 (or later) from `the Python Downloads page <https://www.python.org/downloads/>`_.
2. Using ``git``, clone the Glimpse Docs repository locally: ``git clone git@github.com:Glimpse/Docs.git docs``
3. Change into the ``docs`` directory: ``cd docs``
4. Using ``pip``, Python's package manager, install Sphinx: ``pip install sphinx``
5. Install the ReadTheDocs's Sphinx theme: ``pip install -U sphinx_rtd_theme``
6. Install ``sphinx-autobuild``: ``pip install sphinx-autobuild``
7. Run ``sphinx-autobuild`` with these two options: ``sphinx-autobuild source build/html``
8. Browse to http://localhost:8000/

Editing Documentation
=====================

Edit any ``.rst`` file in the `source` directory. When changes are saved, ``sphinx-autobuild`` will automatically rebuild the site and refresh your browser.

For help with reStructuredText itself, use `the reStructuredText Primer in Sphinx's documentation <http://www.sphinx-doc.org/en/stable/>`_. 