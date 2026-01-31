Requirements Traceability
==========================

Requirements traceability is essential for ensuring complete test coverage and managing changes 
throughout the V-Model lifecycle.

Overview
--------

Traceability provides bidirectional links between:

* User Requirements ↔ System Requirements
* System Requirements ↔ Design Specifications
* Design Specifications ↔ Implementation
* Requirements ↔ Test Cases
* Test Cases ↔ Test Results

Benefits of Traceability
-------------------------

**Quality Assurance**
  * Ensures all requirements are implemented
  * Verifies all implementations are tested
  * Confirms all tests pass

**Change Management**
  * Assesses impact of requirement changes
  * Identifies affected design and tests
  * Reduces regression risk

**Regulatory Compliance**
  * Demonstrates complete coverage
  * Provides audit trail
  * Supports certification

**Project Management**
  * Tracks completion status
  * Identifies gaps and orphans
  * Measures progress

Traceability Matrix Types
--------------------------

Forward Traceability
~~~~~~~~~~~~~~~~~~~~

Traces requirements downward through the V-Model:

.. list-table::
   :header-rows: 1
   :widths: 20 30 30 20

   * - User Req
     - System Req
     - Design Spec
     - Status
   * - USR-SAF-001
     - SYS-BRK-001, SYS-BRK-002
     - HLD-BRK-001
     - Complete
   * - USR-PERF-001
     - SYS-PT-001, SYS-PT-004
     - HLD-PT-001
     - In Progress
   * - USR-INFO-001
     - SYS-IVI-001, SYS-IVI-002
     - HLD-IVI-001
     - Complete

Backward Traceability
~~~~~~~~~~~~~~~~~~~~~~

Traces implementation upward to requirements:

.. list-table::
   :header-rows: 1
   :widths: 25 25 25 25

   * - Code Module
     - Design Spec
     - System Req
     - User Req
   * - brake_controller.c
     - LLD-BRK-010
     - SYS-BRK-001
     - USR-SAF-001
   * - engine_control.c
     - LLD-PT-015
     - SYS-PT-001
     - USR-PERF-001

Horizontal Traceability
~~~~~~~~~~~~~~~~~~~~~~~~

Links requirements to verification:

.. list-table::
   :header-rows: 1
   :widths: 20 15 25 20 20

   * - Requirement
     - ASIL
     - Test Case
     - Test Result
     - Status
   * - SYS-BRK-001
     - D
     - TC-BRK-001, TC-BRK-002
     - PASS
     - Verified
   * - SYS-ADAS-002
     - D
     - TC-AEB-001
     - IN PROGRESS
     - Testing
   * - SYS-IVI-001
     - QM
     - TC-IVI-001
     - PASS
     - Verified

Complete Traceability Example
------------------------------

End-to-End Trace: Emergency Braking
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   USR-SAF-002: Emergency Collision Avoidance
   "The vehicle shall provide automatic emergency braking"
   │
   ├─→ SYS-ADAS-001: Forward Collision Warning
   │   ├─→ HLD-FCW-001: FCW Algorithm Architecture
   │   │   ├─→ LLD-FCW-010: TTC Calculation
   │   │   │   ├─→ CODE: fcw_ttc_calc.c
   │   │   │   └─→ TC-UT-FCW-010: Unit test TTC
   │   │   └─→ LLD-FCW-011: Warning Generation
   │   │       ├─→ CODE: fcw_warning.c
   │   │       └─→ TC-UT-FCW-011: Unit test warnings
   │   └─→ TC-IT-FCW-001: Integration test FCW
   │
   ├─→ SYS-ADAS-002: Automatic Emergency Braking
   │   ├─→ HLD-AEB-001: AEB Controller Architecture
   │   │   ├─→ LLD-AEB-020: Collision Detection
   │   │   │   ├─→ CODE: aeb_detection.c
   │   │   │   └─→ TC-UT-AEB-020: Unit test detection
   │   │   ├─→ LLD-AEB-021: Brake Request
   │   │   │   ├─→ CODE: aeb_brake.c
   │   │   │   └─→ TC-UT-AEB-021: Unit test braking
   │   │   └─→ LLD-AEB-022: Data Logging
   │   │       ├─→ CODE: aeb_logger.c
   │   │       └─→ TC-UT-AEB-022: Unit test logging
   │   ├─→ TC-IT-AEB-001: Integration test AEB
   │   └─→ TC-ST-AEB-001: System test AEB
   │
   └─→ TC-AT-AEB-001: NCAP AEB test protocol
       └─→ RESULT: PASS (95% score)

Traceability Attributes
-----------------------

Each traceability link should capture:

.. code-block:: text

   Link Attributes:
   - Source ID: Originating item
   - Target ID: Related item
   - Link Type: derives-from, implements, verifies, etc.
   - Status: Proposed, Active, Deprecated
   - Rationale: Why the link exists
   - Version: When link was created/modified
   - Owner: Responsible person

Traceability Tools
------------------

Requirements Management Tools
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**IBM DOORS**
  * Industry standard for automotive
  * Comprehensive traceability
  * Integration with testing tools
  * Supports ASPICE compliance

**Polarion ALM**
  * Complete lifecycle management
  * Built-in traceability
  * Workflow automation
  * Real-time collaboration

**Jama Connect**
  * Purpose-built for complex products
  * Live traceability views
  * Risk-based testing
  * Compliance reporting

**Codebeamer**
  * Full ALM platform
  * ISO 26262 compliance
  * Automotive templates
  * Integration with IDEs

Traceability Metrics
--------------------

Coverage Metrics
~~~~~~~~~~~~~~~~

.. code-block:: text

   Requirements Coverage:
   Coverage = (Verified Requirements / Total Requirements) × 100%
   
   Target: 100% for safety-critical (ASIL B-D)
           95% minimum for QM/ASIL A
   
   Test Coverage:
   Test Coverage = (Passed Tests / Total Tests) × 100%
   
   Target: 100% pass rate for release

Orphan Metrics
~~~~~~~~~~~~~~

.. code-block:: text

   Orphan Requirements:
   Requirements with no downstream links (not implemented)
   Target: 0 orphans
   
   Orphan Implementations:
   Code with no upstream requirements (gold-plating)
   Target: <5% of code base
   
   Orphan Tests:
   Tests not linked to requirements
   Target: 0 orphan tests

Suspect Links
~~~~~~~~~~~~~

.. code-block:: text

   Suspect Links:
   Links where source or target has changed
   Require review and re-verification
   Target: Review within 1 week of change

Traceability Report Example
----------------------------

.. code-block:: text

   TRACEABILITY STATUS REPORT
   Project: Vehicle Control System
   Date: 2026-01-31
   Baseline: Release 1.0
   
   ┌─────────────────────────────────────────┐
   │ Requirements Status                     │
   ├─────────────────────────────────────────┤
   │ Total User Requirements:          42    │
   │ Total System Requirements:       328    │
   │ Total Design Specifications:     856    │
   │                                         │
   │ Implemented:                     328    │
   │ In Progress:                      28    │
   │ Not Started:                       0    │
   └─────────────────────────────────────────┘
   
   ┌─────────────────────────────────────────┐
   │ Test Coverage                           │
   ├─────────────────────────────────────────┤
   │ Total Test Cases:               1,247   │
   │ Passed:                         1,189   │
   │ Failed:                            12   │
   │ In Progress:                       46   │
   │                                         │
   │ Coverage:                        95.3%  │
   └─────────────────────────────────────────┘
   
   ┌─────────────────────────────────────────┐
   │ Traceability Health                     │
   ├─────────────────────────────────────────┤
   │ Orphan Requirements:                 3  │
   │ Orphan Tests:                        0  │
   │ Suspect Links:                      15  │
   │                                         │
   │ Overall Health:                  97.2%  │
   └─────────────────────────────────────────┘
   
   ISSUES REQUIRING ATTENTION:
   1. USR-COMF-015: No system requirements allocated
   2. SYS-NET-042: Implementation incomplete
   3. TC-BRK-087 through TC-BRK-098: Tests failing

Traceability Matrix Templates
------------------------------

Requirements to Test Cases
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. csv-table::
   :header: "Req ID", "Requirement", "ASIL", "Test Cases", "Status", "Coverage"
   :widths: 15, 30, 10, 25, 10, 10

   "SYS-BRK-001", "Braking distance < 130ft", "D", "TC-BRK-001, TC-BRK-002", "PASS", "100%"
   "SYS-ADAS-002", "AEB activation", "D", "TC-AEB-001, TC-AEB-002, TC-AEB-003", "PASS", "100%"
   "SYS-IVI-001", "Boot time < 3s", "QM", "TC-IVI-001", "PASS", "100%"

Design to Code
~~~~~~~~~~~~~~

.. csv-table::
   :header: "Design ID", "Component", "Source Files", "Unit Tests", "Coverage"
   :widths: 15, 25, 30, 20, 10

   "LLD-BRK-010", "ABS Controller", "abs_control.c, abs_types.h", "test_abs.c", "98%"
   "LLD-ADAS-020", "Object Detection", "obj_detect.c, kalman.c", "test_obj.c", "100%"

Traceability Process
--------------------

1. Requirements Phase
   - Create requirements in tool
   - Assign unique IDs
   - Define parent-child relationships
   - Set verification methods

2. Design Phase
   - Link design specs to requirements
   - Decompose to lower levels
   - Maintain bidirectional traces
   - Review completeness

3. Implementation Phase
   - Link code modules to design specs
   - Track implementation status
   - Identify orphan code
   - Conduct code reviews

4. Testing Phase
   - Create test cases for each requirement
   - Link test results to test cases
   - Track pass/fail status
   - Identify coverage gaps

5. Validation Phase
   - Generate traceability reports
   - Verify 100% coverage for critical items
   - Resolve any gaps or orphans
   - Obtain stakeholder approval

6. Maintenance Phase
   - Maintain traces during changes
   - Mark suspect links
   - Re-verify after modifications
   - Update documentation

Traceability Challenges
-----------------------

**Common Issues:**

* **Manual Maintenance**: Time-consuming without tools
* **Tool Integration**: Different tools for requirements/testing/code
* **Granularity**: Too fine or too coarse linkages
* **Change Impact**: Cascading effects of changes
* **Multiple Projects**: Cross-project traceability
* **Tool Lock-in**: Vendor-specific formats

**Mitigation Strategies:**

* Invest in integrated ALM tools
* Automate traceability where possible
* Establish clear linking policies
* Regular traceability audits
* Train team on importance and methods
* Use standard interfaces (ReqIF, OSLC)

Best Practices
--------------

1. **Start Early**: Establish traceability from project start
2. **Tool Selection**: Choose appropriate traceability tools
3. **Define Policy**: Clear rules for creating links
4. **Regular Updates**: Keep traceability current
5. **Bidirectional**: Always trace both ways
6. **Automated Checks**: Use tool validation features
7. **Reports**: Generate regular traceability reports
8. **Training**: Ensure team understands importance
9. **Reviews**: Include traceability in peer reviews
10. **Metrics**: Track and improve traceability health

Compliance Requirements
-----------------------

ISO 26262
~~~~~~~~~

* Traceability between safety requirements and design
* Verification of completeness
* Configuration management
* Change impact analysis

ASPICE
~~~~~~

* REQ.2: Requirements management
* SYS.3: System architecture and traceability
* SWE.5: Software integration and testing
* VAL.3: Validation and traceability

Automotive SPICE Level Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* **Level 1**: Traceability exists
* **Level 2**: Traceability managed and updated
* **Level 3**: Process defined and consistently used
* **Level 4**: Traceability measured and controlled
* **Level 5**: Continuous improvement of traceability

Next Steps
----------

Apply traceability throughout:

* :doc:`../design/index` - Design traceability
* :doc:`../testing/index` - Test traceability
* :doc:`../validation/index` - Validation evidence
