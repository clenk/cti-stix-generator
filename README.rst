|Build_Status| |Coverage| |Version| |Binder|

cti-stix-generator
==================

This is an `OASIS TC Open Repository
<https://www.oasis-open.org/resources/open-repositories/>`__. See the
`Governance <#governance>`__ section for more information.

The STIX generator is a tool for generating random STIX content for prototyping
and testing. It uses a simple, sentence-like syntax for expressing what STIX
content to generate. This tool is provided in three forms: as a Jupyter notebook,
as a commandline tool, and a Python library.

For more information, see `the documentation <https://stix2-generator.readthedocs.io/>`__ on ReadTheDocs.

Jupyter Notebook
----------------

The `Jupyter notebook <https://jupyter.org/>`__ provides an interactive
environment to input generator syntax and view the generated content. Go here to
`launch the environment <https://mybinder.org/v2/gh/oasis-open/cti-stix-generator/HEAD>`__.

Open ``stix.ipynb`` in Jupyter to use the tool. Look at ``examples.ipynb`` for
documentation and examples of the syntax.

To use the notebook locally, install the generator's dependencies including the ``jupyter`` extras, and run Jupyter:

.. code-block:: bash

    pip install -e .[jupyter]
    jupyter nbextension install stix2viz --py
    jupyter nbextension enable stix2viz --py
    jupyter notebook

.. note::

   If you are using the JupyterLab interface, the STIX generator notebook extension can only be used in classic mode.

Usage
----------------
Commandline Tool
~~~~~~~~~~~~~~~~

The build_stix commandline tool reads prototyping language from a file, and
prints the generated objects to stdout.  If a bundle is selected, the bundle is
printed instead.

::

    usage: build_stix.py [-h] [-b] [-e ENCODING] [-v]
                     [--stix-version {2.0,2.1}]
                     [--extra-specs EXTRA_SPECS] [-n] [-c CONFIG]
                     language-file

    Create STIX content from the STIX prototyping language

    positional arguments:
      language-file         The file containing STIX prototyping language

    optional arguments:
      -h, --help            show this help message and exit
      -b, --bundle          Create a bundle
      -e ENCODING, --encoding ENCODING
                            Encoding to use when reading text files, e.g. STIX
                            prototyping language, custom generator
                            specifications, etc. Default=utf-8
      -v, --verbose         Enable verbose diagnostic output. Repeat for
                            increased verbosity.
      --stix-version {2.0,2.1}
                            STIX version to use. Default=2.1
      --extra-specs EXTRA_SPECS
                            A JSON file with extra object generator
                            specifications. These will be merged with the
                            built-in specifications, and made available for
                            use in prototyping language content.
      -n, --embed-variable-names
                            Embed variable names in generated objects using a
                            custom property.
      -c CONFIG, --config CONFIG
                            Config file with options to customize how content
                            is generated.

The generate_stix tool is used for more general object generation, which does
not require a language file to be specified:

::

    usage: generate_stix.py [-h] [--min-rels MIN_RELS] [--max-rels MAX_RELS]
                        [--p-reuse P_REUSE] [--p-sighting P_SIGHTING]
                        [--dangling-refs] [--ref-max-depth REF_MAX_DEPTH] [-v]
                        [--stix-version {2.0,2.1}] [-b]

    Generation random STIX content

    optional arguments:
      -h, --help            show this help message and exit
      --min-rels MIN_RELS   Minimum number of SROs to create. Default=1
      --max-rels MAX_RELS   Maximum number or SROs to create. Default=5
      --p-reuse P_REUSE     Probability of object reuse, when creating new
                        connections among objects. Must be a real number in
                        [0, 1]. Lower values result in a graph with more nodes
                        and less interconnection. Higher values result in a
                        graph with fewer nodes and more interconnection.
                        Default=0.5
  --p-sighting P_SIGHTING
                        Probability that when an SRO is added, it is a
                        sighting. Must be a real number in [0, 1]. Default=0.1
  --dangling-refs       Leave reference properties "dangling". Don't force
                        them to refer to existing objects. Applies to all
                        reference properties *except* the endpoints of SROs.
  --ref-max-depth REF_MAX_DEPTH
                        If creating a new object to avoid a dangling reference,
                        the new object could itself have reference properties;
                        new objects created to satisfy those could themselves
                        have reference properties, etc. This setting limits how
                        far we grow this "reference graph". Enforcement of this
                        limit is best-effort; reference properties required by
                        the specification may cause further growth. Only
                        applicable if --dangling-refs is not given. Must be a
                        non-negative integer. Default=0
  -v, --verbose         Enable verbose diagnostic output. Repeat for increased
                        verbosity.
  --stix-version {2.0,2.1}
                        STIX version to use. Default=2.1
  -b, --bundle          Create a bundle

Python Library
~~~~~~~~~~~~~~

You can also generate STIX objects programmatically in a Python script. This can
be useful when the generated objects are used in Python code.

The fastest and easiest way to create random STIX objects is with the
create_stix_generator object:

.. code-block:: python

    import stix2generator

    generator = stix2generator.create_stix_generator()
    generated = generator.generate()

This creates a dictionary of objects related to each other, easy to submit to a
taxii server or sent through the stix validator.

You can create single objects of a specified type using create_object_generator:

.. code-block:: python

    object_generator = stix2generator.create_object_generator()
    object = object_generator.generate("indicator")

A given configuration object can produce more specific results, if necessary:

.. code-block:: python

    config = stix2generator.generation.object_generator.Config(optional_property_probability=.25, minimize_ref_properties=False)
    object_generator = stix2generator.create_object_generator(object_generator_config=config)
    object = object_generator.generate("indicator")

You can also use the language_processor object in a similar fashion as the
build_stix command-line tool. This will produce a list objects based around the
text you give to the build_graph function. In the case below, a malware and an
identity object are created with a relationship object linking them together:

.. code-block:: python

    language_processor = stix2generator.create_default_language_processor()
    objects = language_processor.build_graph("Malware targets Identity.")

Caveats
-------

The tool generates random data for all properties, so it may be nonsensical but
will have the correct datatype or structure according to the STIX
specification.

The object generator currently only generates STIX 2.1 objects. The commandline
tool and some APIs will error out if any STIX version other than "2.1" is used.

Governance
----------

This GitHub public repository `cti-stix-generator <https://github.com/oasis-open/cti-stix-generator/>`__ was created at the request of the `OASIS Cyber Threat Intelligence (CTI) TC <https://www.oasis-open.org/committees/cti/>`__ as an `OASIS TC Open Repository <https://www.oasis-open.org/resources/open-repositories/>`__ to support development of open source resources related to Technical Committee work.

While this TC Open Repository remains associated with the sponsor TC, its development priorities, leadership, intellectual property terms, participation rules, and other matters of governance are `separate and distinct <https://github.com/oasis-open/cti-stix-generator/blob/master/CONTRIBUTING.md#governance-distinct-from-oasis-tc-process>`__ from the OASIS TC Process and related policies.

All contributions made to this TC Open Repository are subject to open source license terms expressed in the `BSD-3-Clause License <https://www-legacy.oasis-open.org/sites/www.oasis-open.org/files/BSD-3-Clause.txt>`__. That license was selected as the declared `applicable license <https://www.oasis-open.org/resources/open-repositories/licenses>`__ when the TC Open Repository was created.

As documented in `Public Participation Invited <https://github.com/oasis-open/cti-stix-generator/blob/master/CONTRIBUTING.md#public-participation-invited>`__, contributions to this OASIS TC Open Repository are invited from all parties, whether affiliated with OASIS or not. Participants must have a GitHub account, but no fees or OASIS membership obligations are required. Participation is expected to be consistent with the `OASIS TC Open Repository Guidelines and Procedures <https://www.oasis-open.org/policies-guidelines/open-repositories>`__, the open source `LICENSE <https://github.com/oasis-open/cti-stix-generator/blob/master/LICENSE.md>`__ designated for this particular repository, and the requirement for an `Individual Contributor License Agreement <https://www.oasis-open.org/resources/open-repositories/cla/individual-cla>`__ that governs intellectual property.

Maintainers
~~~~~~~~~~~

TC Open Repository `Maintainers <https://www.oasis-open.org/resources/open-repositories/maintainers-guide>`__ are responsible for oversight of this project's community development activities, including evaluation of GitHub `pull requests <https://github.com/oasis-open/cti-stix-generator/blob/master/CONTRIBUTING.md#fork-and-pull-collaboration-model>`__ and `preserving <https://www.oasis-open.org/policies-guidelines/open-repositories#repositoryManagement>`__ open source principles of openness and fairness. Maintainers are recognized and trusted experts who serve to implement community goals and consensus design preferences.

Initially, the TC members have designated one or more persons to serve as Maintainer(s); subsequently, participating community members may select additional or substitute Maintainers, by `consensus agreements <https://www.oasis-open.org/resources/open-repositories/maintainers-guide#additionalMaintainers>`__.

.. _currentmaintainers:

Current Maintainers of this TC Open Repository
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

-  `Emily Ratliff <mailto:Emily.Ratliff@ibm.com>`__; GitHub ID: https://github.com/ejratl; WWW: `IBM <http://www.ibm.com/>`__

-  `Duncan Sparrell <mailto:duncan@sfractal.com>`__; GitHub ID: https://github.com/sparrell; WWW: `sFractal <http://sfractal.com/>`__

About OASIS TC Open Repositories
--------------------------------

-  `TC Open Repositories: Overview and Resources <https://www.oasis-open.org/resources/open-repositories/>`__
-  `Frequently Asked Questions <https://www.oasis-open.org/resources/open-repositories/faq>`__
-  `Open Source Licenses <https://www.oasis-open.org/resources/open-repositories/licenses>`__
-  `Contributor License Agreements (CLAs) <https://www.oasis-open.org/resources/open-repositories/cla>`__
-  `Maintainers' Guidelines and Agreement <https://www.oasis-open.org/resources/open-repositories/maintainers-guide>`__

Feedback
--------

Questions or comments about this TC Open Repository's activities should be composed as GitHub issues or comments. If use of an issue/comment is not possible or appropriate, questions may be directed by email to the Maintainer(s) `listed above <#currentmaintainers>`__.

Please send general questions about TC Open Repository participation to OASIS Staff at repository-admin@oasis-open.org and any specific CLA-related questions to repository-cla@oasis-open.org.

.. |Build_Status| image:: https://github.com/oasis-open/cti-stix-generator/workflows/cti-stix-generator%20test%20harness/badge.svg
   :target: https://github.com/oasis-open/cti-stix-generator/actions?query=workflow%3A%22cti-stix-generator+test+harness%22
.. |Coverage| image:: https://codecov.io/gh/oasis-open/cti-stix-generator/branch/master/graph/badge.svg
   :target: https://codecov.io/gh/oasis-open/cti-stix-generator
.. |Version| image:: https://img.shields.io/pypi/v/stix2-generator.svg?maxAge=3600
   :target: https://pypi.org/project/stix2-generator/
.. |Binder| image:: https://mybinder.org/badge_logo.svg
   :target: https://mybinder.org/v2/gh/oasis-open/cti-stix-generator/HEAD
