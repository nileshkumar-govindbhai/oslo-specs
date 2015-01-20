..

=============================
 Unicode support in Oslo Configuration
=============================

https://blueprints.launchpad.net/oslo.config/+spec/values-unicode-support

Non ASCII values in oslo configuration file, returns string insted of UNICODE.

Problem description
===================

Non ASCII string value (like vCenter Cluster name in Japanese or Chinese) for configuration parameter can be stored in file using String type. When non-ascii values are retrieved from Oslo.config, a string(s) is returned.

If Configuration parameter value is non-ascii, then the value will be of String type, instead value should be of Unicode type or return Unicode.

Hence value of the parameter needs to be converted to Unicode or it will fail in all the String operations (like comparison, concatenation etc.).

Proposed change
===============

oslo.config
------------

String type will have another attribute ``encoding`` which will be default ``None`` means no encoding will be perform on return value. If the ``encoding`` type given as ``utf-8`` or ``latin-1`` then return value must be encoded using this format.

::

    # oslo/config/types.py

    class String(ConfigType):
        ...
        ...
        def __init__(self, choices=None, quotes=False, encoding=None):
            ...
            ...
            if encoding:
                return value.encode(encoding)
            ...
            ...


When ``String`` object created, need to pass ``encoding``.

::

    # oslo/config/cfg.py
    class StrOpt(Opt):
        ...
        ...
        def __init__(self, name, choices=None, **kwargs):
            encoding = kwargs.get('encoding', None)
            super(StrOpt, self).__init__(name,
                                         type=types.String(choices=choices,
                                                           encoding=encoding),
                                         **kwargs)
            ...
            ...


Alternatives
------------


1. Create new type Unicode and use when config value is non ASCII. This needs to be changed every project where seems use of Unicode.

2. Change value after retrieve from oslo.config. Everytime read from oslo.config needs to be encode or decode as per requirements.

Impact on Existing APIs
-----------------------


Need to pass ``encoding`` when create ``String`` object in ``cfg.py``.

Security impact
---------------

None

Performance Impact
------------------

None

Configuration Impact
--------------------

None

Developer Impact
----------------

Developer has to pass ``encoding`` when non ASCII value can be provided to config.

Testing Impact
--------------

None

Implementation
==============

Assignee(s)
-----------

Primary assignee:
  Nileshkumar Patel (nileshkumar-govindbhai-patel)

Other contributors:
  Abhishek Srivastava (srivastava-abhishek)
  Aiswarya Sundaran (aiswarya-sundaran)

Milestones
----------

Target Milestone for completion: kilo

Work Items
----------

1. Add new attribute ``encoding`` to ``String`` type.
2. Pass ``encoding`` to ``String`` type when it create object.
3. Create testcash for the encoding.

Incubation
==========

N/A

Adoption
--------

N/A

Library
-------

N/A

Anticipated API Stabilization
-----------------------------

This new API will give only one extra attribute. Default it is ``None``, and will not effect existing APIs.

Documentation Impact
====================

Change docs to support different type of encoding in oslo.config.

Dependencies
============

None

References
==========

None


.. note::

  This work is licensed under a Creative Commons Attribution 3.0
  Unported License.
  http://creativecommons.org/licenses/by/3.0/legalcode



