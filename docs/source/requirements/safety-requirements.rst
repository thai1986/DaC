Safety Requirements
===================

Safety requirements are critical for automotive systems, governing fault detection, failure mitigation, 
and compliance with ISO 26262 functional safety standards.

Overview
--------

Safety requirements ensure the vehicle operates safely even under fault conditions. They are classified 
by Automotive Safety Integrity Level (ASIL) from QM (Quality Management) to ASIL D (highest rigor).

ISO 26262 Framework
--------------------

ASIL Classification
~~~~~~~~~~~~~~~~~~~

.. list-table::
   :header-rows: 1
   :widths: 15 25 30 30

   * - ASIL
     - Severity
     - Exposure
     - Example Systems
   * - QM
     - No injury
     - -
     - Infotainment, comfort features
   * - A
     - Light injuries
     - Low probability
     - Interior lighting, rear wiper
   * - B
     - Moderate injuries
     - Medium probability
     - Brake lights, turn signals
   * - C
     - Severe injuries
     - High probability
     - Cruise control, HVAC
   * - D
     - Life threatening
     - High probability
     - Steering, braking, airbags

Safety Goals
------------

High-Level Safety Objectives
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   SG-001: Unintended Acceleration Prevention
   The vehicle shall prevent unintended acceleration that could lead to collision.
   ASIL: D
   
   SG-002: Brake Failure Prevention
   The braking system shall provide adequate stopping capability under all conditions.
   ASIL: D
   
   SG-003: Steering Loss Prevention
   The steering system shall maintain controllability in all operating modes.
   ASIL: D
   
   SG-004: Airbag Deployment Reliability
   Airbags shall deploy when needed and shall not deploy inadvertently.
   ASIL: D
   
   SG-005: Battery Safety (EV)
   The high-voltage battery system shall prevent fire and electric shock hazards.
   ASIL: D

Functional Safety Requirements
-------------------------------

Powertrain Safety
~~~~~~~~~~~~~~~~~

.. code-block:: text

   SAF-PT-001: Throttle Position Monitoring
   The system shall monitor throttle position using dual independent sensors.
   - Compare sensor readings continuously
   - Detect discrepancy >5%
   - Enter limp-home mode if fault detected
   - Limit torque to <30% when in degraded mode
   ASIL: D | Parent: SG-001
   
   SAF-PT-002: Unintended Acceleration Detection
   The system shall detect unintended acceleration by:
   - Monitoring pedal position vs. expected torque
   - Detecting stuck throttle conditions
   - Comparing driver intent with vehicle response
   - Activating fail-safe within 100ms
   ASIL: D | Parent: SG-001
   
   SAF-PT-003: Engine Torque Limitation
   When fault detected, the system shall:
   - Limit engine torque to safe level
   - Maintain minimum mobility (limp-home)
   - Prevent further acceleration
   - Alert driver of degraded mode
   ASIL: D | Parent: SG-001

Braking System Safety
~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   SAF-BRK-001: Dual-Circuit Brake System
   The hydraulic brake system shall:
   - Implement dual independent circuits
   - Maintain 50% braking if one circuit fails
   - Detect circuit failures within one pedal application
   - Activate warning lamp immediately
   ASIL: D | Parent: SG-002
   
   SAF-BRK-002: Electronic Brake Redundancy
   Electronic brake systems shall:
   - Provide independent hydraulic backup
   - Detect electronic system failures
   - Fallback to conventional braking <500ms
   - Maintain adequate stopping distance
   ASIL: D | Parent: SG-002
   
   SAF-BRK-003: Brake System Monitoring
   The system shall monitor:
   - Brake fluid level (optical sensor)
   - Brake pad wear (sensor or estimation)
   - Hydraulic pressure (dual sensors)
   - ABS wheel speed sensors (plausibility checks)
   ASIL: C | Parent: SG-002
   
   SAF-BRK-004: Brake-by-Wire Safety
   For brake-by-wire systems:
   - Implement triple-modular redundancy (TMR)
   - Voting logic for fault tolerance
   - Mechanical backup brake system
   - Fault detection coverage >99%
   ASIL: D | Parent: SG-002

Steering System Safety
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   SAF-STR-001: Power Steering Failure Handling
   If power steering fails, the system shall:
   - Maintain mechanical steering capability
   - Increase steering effort is acceptable
   - Warn driver immediately
   - Maintain controllability at all speeds
   ASIL: D | Parent: SG-003
   
   SAF-STR-002: Steering Angle Sensor Redundancy
   The system shall:
   - Use dual steering angle sensors
   - Cross-check sensor readings
   - Detect discrepancy >3°
   - Enter safe state if fault detected
   ASIL: D | Parent: SG-003
   
   SAF-STR-003: Steer-by-Wire Safety
   For steer-by-wire systems:
   - Implement quadruple redundancy
   - Diverse hardware and software
   - Independent power supplies
   - Mechanical fallback mechanism
   ASIL: D | Parent: SG-003

ADAS Safety Requirements
-------------------------

.. code-block:: text

   SAF-ADAS-001: Sensor Fault Detection
   The system shall detect sensor faults:
   - Radar: Signal loss, blocked sensor, misalignment
   - Camera: Blinded, obscured, calibration error
   - Lidar: Range degradation, contamination
   - Ultrasonic: Failure, reduced sensitivity
   - Detection time: <200ms
   - Deactivate affected ADAS feature
   - Inform driver of degraded capability
   ASIL: D | Parent: Multiple SGs
   
   SAF-ADAS-002: Sensor Fusion Plausibility
   The system shall:
   - Cross-check data from multiple sensors
   - Detect inconsistent object reports
   - Weight sensor confidence based on conditions
   - Reject implausible data
   - Maintain safety even with single sensor failure
   ASIL: D | Parent: Multiple SGs
   
   SAF-ADAS-003: AEB False Activation Prevention
   The system shall prevent false AEB activation:
   - Require multi-sensor confirmation
   - Validate collision probability >90%
   - Implement graduated warnings before braking
   - Allow driver override capability
   - Log all AEB activations
   ASIL: D | Parent: SG-002
   
   SAF-ADAS-004: Driver Monitoring
   For Level 2+ automation, the system shall:
   - Monitor driver attention (camera, touch sensors)
   - Detect driver unresponsiveness
   - Provide escalating warnings
   - Execute safe stop if driver not responding
   - Frequency: Continuous, assessment every 5s
   ASIL: D | Parent: SG-003

Airbag System Safety
~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   SAF-AIR-001: Crash Detection Accuracy
   The system shall:
   - Detect crashes requiring airbag deployment
   - Use accelerometer arrays (front, side, rear)
   - Make deployment decision within 15ms
   - Prevent deployment in non-crash events (>99.9%)
   - Deploy appropriate airbags based on crash type
   ASIL: D | Parent: SG-004
   
   SAF-AIR-002: Inadvertent Deployment Prevention
   The system shall prevent false deployment:
   - Require multi-point crash confirmation
   - Implement voting logic
   - Distinguish crashes from potholes/speed bumps
   - Maintain squib circuit safety
   - False deployment rate: <1 in 10 million
   ASIL: D | Parent: SG-004
   
   SAF-AIR-003: System Self-Test
   At ignition on, the system shall:
   - Test all crash sensors
   - Verify squib circuits
   - Check ECU functionality
   - Complete test within 3 seconds
   - Illuminate warning lamp if fault detected
   ASIL: D | Parent: SG-004

Battery Safety (Electric Vehicles)
-----------------------------------

.. code-block:: text

   SAF-BMS-001: Cell Voltage Limits
   The BMS shall:
   - Monitor each cell voltage continuously
   - Enforce limits: 2.5V (min) to 4.2V (max)
   - Disconnect battery if limit exceeded
   - Prevent overcharge/over-discharge
   - Response time: <100ms
   ASIL: D | Parent: SG-005
   
   SAF-BMS-002: Thermal Runaway Prevention
   The system shall:
   - Monitor cell temperatures (<1°C accuracy)
   - Detect abnormal temperature rise (>5°C/min)
   - Isolate affected cell groups
   - Activate fire suppression if needed
   - Alert occupants and first responders
   ASIL: D | Parent: SG-005
   
   SAF-BMS-003: High Voltage Isolation Monitoring
   The system shall:
   - Monitor HV isolation to chassis
   - Detect isolation faults >100Ω/V
   - Disconnect HV system if fault detected
   - Prevent electric shock hazard
   - Test frequency: Every 1 second
   ASIL: D | Parent: SG-005
   
   SAF-BMS-004: Crash Detection Response
   Upon crash detection, the system shall:
   - Disconnect HV battery within 50ms
   - Open all contactors
   - Discharge HV capacitors
   - Illuminate HV warning indicators
   - Communicate status to emergency responders
   ASIL: D | Parent: SG-005
   
   SAF-BMS-005: Short Circuit Protection
   The system shall:
   - Detect short circuit conditions (<10μs)
   - Open protective fuses/pyro-fuses
   - Limit short circuit current
   - Prevent battery damage and fire
   - Multiple levels of protection
   ASIL: D | Parent: SG-005

Fault Detection and Handling
-----------------------------

Fault Detection Coverage
~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   SAF-FD-001: Diagnostic Coverage Requirements
   For ASIL D functions:
   - Single Point Fault Metric (SPFM) > 99%
   - Latent Fault Metric (LFM) > 90%
   - Implement comprehensive diagnostics
   - Periodic testing of safety mechanisms
   
   SAF-FD-002: Fault Detection Time
   Critical faults shall be detected:
   - Immediately: <10ms (steering, braking)
   - Quickly: <100ms (powertrain)
   - Regularly: <1 second (monitoring functions)
   - Latent: Within one driving cycle

Safe States
~~~~~~~~~~~

.. code-block:: text

   SAF-SS-001: Degraded Mode Operation
   When non-critical fault detected:
   - Continue operation with limitations
   - Inform driver of reduced capability
   - Maintain essential vehicle functions
   - Allow vehicle to reach service location
   
   SAF-SS-002: Limp-Home Mode
   When significant fault detected:
   - Reduce maximum speed to 45 mph
   - Limit acceleration
   - Maintain basic mobility
   - Activate hazard warning indication
   
   SAF-SS-003: Immediate Shutdown
   When critical fault detected:
   - Execute controlled shutdown
   - Move to roadside if possible
   - Activate emergency flashers
   - Apply parking brake
   - Alert emergency services if available

Fault Tolerant Architecture
----------------------------

Redundancy Strategies
~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   SAF-RED-001: Dual Redundancy
   For ASIL C/D systems:
   - Implement dual sensing
   - Cross-check measurements
   - Use diverse sensor technologies
   - Example: Brake pressure (2 sensors)
   
   SAF-RED-002: Triple Modular Redundancy (TMR)
   For highest criticality:
   - Three independent channels
   - Voting logic for output
   - Continue with single failure
   - Example: Steer-by-wire controllers
   
   SAF-RED-003: Graceful Degradation
   Design systems for:
   - Partial functionality when faulted
   - Prioritize safety over performance
   - Maintain minimum safe operation
   - Clear indication of degraded state

Safety Validation Requirements
-------------------------------

.. code-block:: text

   SAF-VAL-001: Fault Injection Testing
   Safety systems shall be validated by:
   - Injecting representative faults
   - Verifying fault detection
   - Confirming safe state transitions
   - Testing all fault scenarios
   - Coverage: 100% of defined faults
   
   SAF-VAL-002: FMEA Analysis
   Conduct Failure Mode and Effects Analysis:
   - Identify potential failure modes
   - Assess severity and likelihood
   - Determine detection methods
   - Implement mitigation strategies
   - Document ASIL requirements
   
   SAF-VAL-003: FTA Analysis
   Conduct Fault Tree Analysis:
   - Top events: Safety goals
   - Trace to basic events
   - Calculate failure probabilities
   - Verify ASIL requirements met
   - Identify single points of failure

Documentation Requirements
---------------------------

.. code-block:: text

   SAF-DOC-001: Safety Case
   Develop comprehensive safety case including:
   - Safety goals and requirements
   - Design documentation
   - Analysis results (FMEA, FTA, FIT)
   - Verification evidence
   - Validation results
   
   SAF-DOC-002: Safety Manual
   Provide safety manual containing:
   - Safe use instructions
   - Safety-related characteristics
   - Integration requirements
   - Maintenance procedures
   - Decommissioning guidance

Cybersecurity Safety Requirements
----------------------------------

.. code-block:: text

   SAF-CYB-001: Safety-Critical Communication Protection
   Safety messages shall be protected by:
   - Authentication (prevent spoofing)
   - Freshness checks (prevent replay)
   - Integrity checks (prevent tampering)
   - Encryption (prevent eavesdropping)
   ASIL: D
   
   SAF-CYB-002: Intrusion Detection Safety Response
   If cybersecurity attack detected:
   - Isolate affected systems
   - Maintain core safety functions
   - Alert driver and manufacturer
   - Log attack details
   - Enter safe operating mode
   ASIL: D

Testing Traceability
--------------------

.. list-table::
   :header-rows: 1
   :widths: 25 20 30 25

   * - Safety Requirement
     - ASIL
     - Verification Method
     - Test Reference
   * - SAF-BRK-001
     - D
     - Test + Analysis
     - STC-BRK-001
   * - SAF-ADAS-003
     - D
     - Test + FMEAc
     - STC-AEB-010
   * - SAF-BMS-001
     - D
     - Test
     - STC-BMS-001

Best Practices
--------------

1. **Early Safety Analysis**: Start HARA and FMEA early
2. **Independence**: Safety validation independent from development
3. **Conservative Design**: Fail-safe, not fail-operational
4. **Defense in Depth**: Multiple layers of protection
5. **Clear Responsibility**: Assign safety responsibilities
6. **Continuous Monitoring**: Runtime diagnostics
7. **Change Management**: Impact analysis for all changes
8. **Lessons Learned**: Incorporate field experience

Next Steps
----------

Safety requirements drive:

* Hardware and software safety mechanisms
* :doc:`../testing/safety-testing` - Safety validation testing
* :doc:`../validation/index` - Safety case development
