Implementation
==============

The implementation phase involves coding, unit testing, and preparing components for integration.

Overview
--------

Implementation translates detailed designs into source code following automotive coding standards 
and safety requirements.

Key Activities
--------------

* Source code development
* Unit testing
* Code reviews
* Static analysis
* Documentation

Coding Standards
----------------

MISRA C
~~~~~~~

Industry standard for automotive C programming:

* MISRA C:2012 for safety-critical code
* Mandatory rules: 100% compliance
* Required rules: 100% compliance  
* Advisory rules: Document deviations

Example Rules:

* No dynamic memory allocation
* No recursion
* Limited use of pointers
* Explicit type conversions
* Defensive programming

AUTOSAR C++
~~~~~~~~~~~

For C++ development:

* AUTOSAR C++14 Coding Guidelines
* Subset of C++ safe for automotive
* Restrictions on templates, exceptions
* Memory management guidelines

Best Practices
--------------

1. **Follow Design**: Implement per LLD specifications
2. **Code Reviews**: Peer review all safety code
3. **Static Analysis**: Zero critical warnings
4. **Unit Test**: Test as you code
5. **Version Control**: Commit frequently with clear messages
6. **Documentation**: Comment complex algorithms
7. **Defensive Programming**: Validate inputs, check returns
8. **Error Handling**: Handle all error conditions

Next Steps
----------

Implementation leads to:

* :doc:`../testing/unit-testing` - Component verification
* Code integration and system build
