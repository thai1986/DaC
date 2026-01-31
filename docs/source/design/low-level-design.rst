Low-Level Design
================

Low-level design (LLD) provides detailed specifications for implementing individual components.

Overview
--------

LLD translates high-level architecture into detailed specifications including:

* Algorithms and logic
* Data structures
* State machines
* Detailed interfaces
* Implementation guidelines

This phase corresponds to Unit Testing in the V-Model.

LLD Components
--------------

For each software component, LLD specifies:

**Functional Behavior**
  * Detailed algorithms
  * Pseudo-code or flowcharts
  * Logic flow
  * Decision trees

**Data Structures**
  * Variable definitions
  * Data types
  * Arrays and buffers
  * Memory layout

**Interfaces**
  * Function signatures
  * Parameters and return values
  * Error codes
  * Timing constraints

**Error Handling**
  * Fault detection
  * Error responses
  * Recovery mechanisms
  * Diagnostic outputs

Example LLD: TTC Calculation
-----------------------------

Component Specification
~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   Component: Time-to-Collision Calculator
   ID: LLD-AEB-TTC-001
   Parent: HLD-AEB-001 (Threat Assessment)
   Requirements: SYS-ADAS-001, SAF-ADAS-001
   ASIL: D
   
   Purpose:
   Calculate time-to-collision with detected objects
   to determine threat level for AEB activation.

Functional Description
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   Inputs:
   - object_range: Distance to object (meters)
   - object_velocity: Object velocity (m/s, negative = approaching)
   - ego_velocity: Vehicle velocity (m/s)
   - object_confidence: Detection confidence (0-100%)
   
   Outputs:
   - ttc: Time-to-collision (seconds)
   - threat_level: LOW/MEDIUM/HIGH/CRITICAL
   - valid: Boolean indicating valid calculation
   
   Processing:
   1. Validate inputs (range checks, plausibility)
   2. Calculate relative velocity
   3. Calculate TTC using closing velocity
   4. Apply filtering to reduce noise
   5. Determine threat level based on TTC thresholds
   6. Return results with validity flag

Algorithm Specification
~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: c

   /*
    * Function: calculate_ttc
    * ASIL: D
    * Execution Time: <1ms
    * Memory: Stack only, no dynamic allocation
    */
   
   typedef struct {
       float ttc;              // Time to collision (s)
       ThreatLevel threat;     // Threat classification
       bool valid;             // Calculation validity
   } TTC_Result;
   
   TTC_Result calculate_ttc(
       float object_range,      // meters, [0, 300]
       float object_velocity,   // m/s, [-50, 50]
       float ego_velocity,      // m/s, [0, 55]
       uint8_t object_confidence // [0, 100]
   ) {
       TTC_Result result = {0};
       
       // Input validation
       if (!validate_inputs(object_range, object_velocity, 
                           ego_velocity, object_confidence)) {
           result.valid = false;
           return result;
       }
       
       // Calculate relative velocity (closing velocity)
       float relative_velocity = ego_velocity + object_velocity;
       
       // TTC calculation (avoid division by zero)
       if (relative_velocity > MIN_CLOSING_VELOCITY) {
           result.ttc = object_range / relative_velocity;
           
           // Apply first-order IIR filter for smoothing
           static float filtered_ttc = 999.0f;
           filtered_ttc = (FILTER_ALPHA * result.ttc) + 
                         ((1.0f - FILTER_ALPHA) * filtered_ttc);
           result.ttc = filtered_ttc;
           
           // Classify threat level
           result.threat = classify_threat(result.ttc, 
                                          object_confidence);
           result.valid = true;
       } else {
           // No collision threat (diverging or parallel)
           result.ttc = 999.0f;  // Sentinel value
           result.threat = THREAT_LOW;
           result.valid = true;
       }
       
       return result;
   }

Helper Functions
~~~~~~~~~~~~~~~~

.. code-block:: c

   /*
    * Input validation with range and plausibility checks
    */
   bool validate_inputs(float range, float obj_vel, 
                       float ego_vel, uint8_t confidence) {
       // Range checks
       if (range < 0.0f || range > MAX_DETECTION_RANGE)
           return false;
       if (obj_vel < MIN_OBJ_VELOCITY || obj_vel > MAX_OBJ_VELOCITY)
           return false;
       if (ego_vel < 0.0f || ego_vel > MAX_EGO_VELOCITY)
           return false;
       if (confidence > 100)
           return false;
       
       // Plausibility checks
       if (confidence < MIN_CONFIDENCE_THRESHOLD)
           return false;
       
       return true;
   }
   
   /*
    * Classify threat based on TTC and confidence
    */
   ThreatLevel classify_threat(float ttc, uint8_t confidence) {
       // Higher confidence required for shorter TTC
       float confidence_factor = confidence / 100.0f;
       
       if (ttc < CRITICAL_TTC && confidence > CRITICAL_CONFIDENCE)
           return THREAT_CRITICAL;
       else if (ttc < HIGH_TTC && confidence > HIGH_CONFIDENCE)
           return THREAT_HIGH;
       else if (ttc < MEDIUM_TTC && confidence > MEDIUM_CONFIDENCE)
           return THREAT_MEDIUM;
       else
           return THREAT_LOW;
   }

Constants and Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: c

   // Physical constants
   #define MAX_DETECTION_RANGE     300.0f  // meters
   #define MIN_OBJ_VELOCITY        -50.0f  // m/s
   #define MAX_OBJ_VELOCITY         50.0f  // m/s
   #define MAX_EGO_VELOCITY         55.0f  // m/s (200 km/h)
   
   // Algorithm parameters
   #define MIN_CLOSING_VELOCITY     0.5f   // m/s
   #define FILTER_ALPHA             0.3f   // IIR filter coefficient
   #define MIN_CONFIDENCE_THRESHOLD 50     // percent
   
   // Threat thresholds
   #define CRITICAL_TTC             1.0f   // seconds
   #define HIGH_TTC                 2.0f   // seconds
   #define MEDIUM_TTC               3.0f   // seconds
   
   #define CRITICAL_CONFIDENCE      95     // percent
   #define HIGH_CONFIDENCE          85     // percent
   #define MEDIUM_CONFIDENCE        70     // percent

State Machine
~~~~~~~~~~~~~

.. code-block:: text

   TTC Calculator States:
   
   ┌─────────────┐
   │  INIT       │ ← System startup
   └──────┬──────┘
          │
   ┌──────▼──────┐
   │  IDLE       │ ← Waiting for valid inputs
   └──────┬──────┘
          │ Valid inputs received
   ┌──────▼──────┐
   │  CALCULATING│ ← Active TTC calculation
   └──────┬──────┘
          │
          ├─→ Valid result → Output TTC
          │
          └─→ Invalid inputs → Error state

Timing Specification
~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   Execution Requirements:
   - Worst-case execution time: <1ms
   - Typical execution time: 0.3ms
   - Call frequency: 50Hz (20ms period)
   - Deadline: Must complete within period
   
   Resource Usage:
   - Stack: 128 bytes
   - Static memory: 32 bytes (filter state)
   - No heap allocation
   - No blocking calls

Error Handling
~~~~~~~~~~~~~~

.. code-block:: text

   Error Conditions:
   
   ERR-TTC-001: Invalid Range
   - Detection: range < 0 or > MAX_DETECTION_RANGE
   - Response: Return invalid result, log error
   - Recovery: Wait for valid input
   
   ERR-TTC-002: Invalid Velocity
   - Detection: Velocity out of range
   - Response: Return invalid result, log error
   - Recovery: Wait for valid input
   
   ERR-TTC-003: Low Confidence
   - Detection: confidence < MIN_CONFIDENCE_THRESHOLD
   - Response: Return invalid result
   - Recovery: Automatic when confidence improves
   
   ERR-TTC-004: Sensor Loss
   - Detection: No input updates for 200ms
   - Response: Set threat to UNKNOWN, warn driver
   - Recovery: Resume when sensor data available

Unit Test Specification
~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   Test Case: TC-UT-TTC-001
   Objective: Verify TTC calculation accuracy
   
   Test Data:
   - object_range = 100.0m
   - object_velocity = 0.0 m/s (stationary)
   - ego_velocity = 25.0 m/s (90 km/h)
   - confidence = 95%
   
   Expected Result:
   - ttc = 4.0 seconds ±0.1s
   - threat = THREAT_MEDIUM
   - valid = true
   
   ---
   
   Test Case: TC-UT-TTC-002
   Objective: Verify boundary condition (zero closing velocity)
   
   Test Data:
   - object_range = 50.0m
   - object_velocity = 15.0 m/s (same speed)
   - ego_velocity = 15.0 m/s
   - confidence = 90%
   
   Expected Result:
   - ttc = 999.0 (no collision)
   - threat = THREAT_LOW
   - valid = true
   
   ---
   
   Test Case: TC-UT-TTC-003
   Objective: Verify input validation
   
   Test Data:
   - object_range = -10.0m (invalid)
   - object_velocity = 0.0 m/s
   - ego_velocity = 20.0 m/s
   - confidence = 95%
   
   Expected Result:
   - valid = false
   - Error logged: ERR-TTC-001

Code Coverage Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~

For ASIL D:

* **Statement Coverage**: 100%
* **Branch Coverage**: 100%
* **MC/DC Coverage**: 100%
* **Function Coverage**: 100%

MISRA C Compliance
~~~~~~~~~~~~~~~~~~

.. code-block:: text

   Compliance with MISRA C:2012
   
   Mandatory Rules: 100% compliance
   Required Rules: 100% compliance
   Advisory Rules: Deviations documented
   
   Static Analysis:
   - No critical or high severity warnings
   - Medium severity reviewed and justified
   - Analysis tool: PC-Lint Plus or equivalent

LLD Documentation Template
--------------------------

.. code-block:: text

   LOW-LEVEL DESIGN DOCUMENT
   
   1. Component Overview
      1.1 Component ID and Name
      1.2 Purpose
      1.3 Parent HLD Reference
      1.4 Requirements Traceability
      1.5 ASIL Classification
   
   2. Functional Description
      2.1 Inputs
      2.2 Outputs
      2.3 Processing Steps
      2.4 Algorithms
   
   3. Detailed Design
      3.1 Pseudo-code or Flowcharts
      3.2 Data Structures
      3.3 State Machines
      3.4 Constants and Configuration
   
   4. Interface Specification
      4.1 Function Signatures
      4.2 Parameters
      4.3 Return Values
      4.4 Error Codes
   
   5. Timing and Performance
      5.1 Execution Time
      5.2 Call Frequency
      5.3 Resource Usage
      5.4 Constraints
   
   6. Error Handling
      6.1 Error Conditions
      6.2 Detection Methods
      6.3 Recovery Actions
      6.4 Diagnostics
   
   7. Safety Mechanisms
      7.1 Fault Detection
      7.2 Redundancy
      7.3 Plausibility Checks
      7.4 Safe States
   
   8. Unit Test Specification
      8.1 Test Cases
      8.2 Coverage Requirements
      8.3 Test Environment
   
   9. Implementation Notes
      9.1 Coding Standards
      9.2 Special Considerations
      9.3 Known Limitations
   
   10. Appendices
       10.1 Variable Dictionary
       10.2 Change History

Design Review Checklist
------------------------

* ✓ Requirements fully addressed
* ✓ Algorithm correctness verified
* ✓ Data structures appropriate
* ✓ Error handling complete
* ✓ Timing constraints met
* ✓ Resource usage acceptable
* ✓ Safety mechanisms adequate
* ✓ Code standards compliance
* ✓ Unit tests defined
* ✓ Traceability maintained

Best Practices
--------------

1. **Detailed Specifications**: Sufficient detail for implementation
2. **Algorithm Verification**: Validate algorithms before coding
3. **Error Handling**: Comprehensive error coverage
4. **Resource Management**: Define memory and timing budgets
5. **Safety Mechanisms**: Implement as designed
6. **Unit Testability**: Design for testability
7. **Code Standards**: Follow MISRA C or equivalent
8. **Documentation**: Keep LLD synchronized with code
9. **Peer Review**: Review before implementation
10. **Traceability**: Maintain links to requirements and tests

Next Steps
----------

Low-level design flows to:

* :doc:`../implementation/index` - Coding and implementation
* :doc:`../testing/unit-testing` - Unit testing
* Code reviews and static analysis
