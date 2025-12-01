This document describes the workflow for creating Custom Board Support for MotorBench v2.5 in order to test and evaluate the Atmel Low Voltage BLDC Motor Control Starter Kit. It includes hardware setup, YAML creation, verification steps, X2C Scope integration, and related references.

----------------------------------------------------------------------
OVERVIEW
----------------------------------------------------------------------

This project covers:
- Setting up MotorBench 2.50 for custom board support
- Creating a custom YAML for the Atmel LV BLDC starter kit
- Checking current sensing, DC-link sensing, and PWM correctness
- Running forced-commutation tests before closed-loop
- Using X2C Scope for runtime control (no hardware buttons)
- Using MCAF Test Harness for evaluation
- Providing project files (CustomBoardTest1.X) and motor XML

----------------------------------------------------------------------  
HARDWARE USED  
----------------------------------------------------------------------

- ATSAMD21BLDC24V-STK1 Atmel LV BLDC Starter Kit  
- dsPIC33CK64MC105 Curiosity Nano 2  
- SCT013-050 current transformer  
- Breadboard and jumper wires  
- PC with MPLAB X and MotorBench v2.5 beta  

The system reads two phase currents (up to approx. 8.25 A) and the DC-link voltage (up to approx. 50 V).

----------------------------------------------------------------------  
REQUIRED DOCUMENTATION  
----------------------------------------------------------------------

Download and read before starting:

1. MotorBench 2.50 – Information Guide  

2. Board Definition Notes (Dec 19, 2024)  

3. Atmel LV BLDC YAML (Custom Board)  

4. Atmel BLDC Motor XML settings  

5. Atmel LV BLDC Hardware User’s Guide  
   https://ww1.microchip.com/downloads/aemDocuments/documents/OTH/ProductDocuments/UserGuides/Atmel-42681-SMART-ARM-based-Motor-Control-Kit_UserGuide.pdf

----------------------------------------------------------------------  
CREATING THE CUSTOM BOARD YAML  
----------------------------------------------------------------------

The YAML was created in VS Code using the StarterBoard example from MotorBench.

Notes:
- Indentation must use spaces (no tabs).  
- Reference examples can be obtained by exporting the YAMLs for MCLV-2, MCLV-48V, or MCHV boards.  
- The completed file is: Atmel-LVBLDC.yaml  

ATPLL estimator was chosen due to better startup behavior.  
Startup velocity was increased from the default.

----------------------------------------------------------------------  
X2C SCOPE CONTROL (NO HARDWARE UI)  
----------------------------------------------------------------------

Because no buttons or potentiometers are used, all runtime interaction is done through X2C Scope.

Important MCAF variables:

hardwareUIenabled = 0  
runMotorRequest = start motor  
stopMotorRequest = stop motor  
VelocityReference = desired speed (negative value reverses direction)

Notes:
- motorDirection and direction variables are not used.  
- Direction is controlled only by the sign of VelocityReference.  

----------------------------------------------------------------------  
USING THE TEST HARNESS  
----------------------------------------------------------------------

Enable by editing "options.h":

#define MCAF_TEST_HARNESS

This activates the MCAF test harness for controlled testing.

----------------------------------------------------------------------  
PRE–CLOSED-LOOP VERIFICATION  
----------------------------------------------------------------------

Before enabling closed-loop control, these steps were completed:

1. Voltage mode and forced commutation (see Board Definition Notes).  
2. Verify PWM polarity and timing.  
3. Verify phase current scaling using SCT013-050.  
4. Verify DC-link voltage scaling.  
5. After all readings matched expectations, closed loop was tested.

Closed-loop FOC worked without extensive tuning.

----------------------------------------------------------------------  
PROJECT FILES  
----------------------------------------------------------------------

MPLAB X Project (CustomBoardTest1.X):  

Motor parameters (AtmelBLDC-motor.xml):  

----------------------------------------------------------------------  
REPOSITORY STRUCTURE (SUGGESTED)  
----------------------------------------------------------------------

Custom-BoardSupport-MotorBench-AtmelLVBLDC/
│
├── README.txt
├── Atmel-LVBLDC.yaml
├── CustomBoardTest1.X/
│   └── ...
├── AtmelBLDC-motor.xml
└── docs/
    ├── wiring_photos/
    ├── board-definition-notes.pdf
    └── MB2.5-information-guide.pdf

----------------------------------------------------------------------  
NOTES AND OBSERVATIONS  
----------------------------------------------------------------------

- ATPLL provided better startup performance than default estimators.  
- Current sensors saturate around 8–8.5 A; avoid exceeding this.  
- Direction is changed using negative speed, not configuration flags.  
- YAML spacing is critical; tabs cause MotorBench to reject the file.  
- Closed loop worked once current and voltage scaling were verified.


----------------------------------------------------------------------  
ACKNOWLEDGMENTS  
----------------------------------------------------------------------

This work was completed using MotorBench v2.5 , the Microchip Motor Control Application Framework (MCAF), and the Atmel LV BLDC Motor Control Starter Kit.  
