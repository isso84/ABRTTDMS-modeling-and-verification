# ABRTTDMS Case Study - Complete Specifications and Verification Results
**Note:** This case study is adapted from the Mobile-C traffic detection system available at: https://www.mobilec.org/apps/lbds/

## 1. System Overview

The Agent-Based Real-Time Traffic Detection and Management System (ABRTTDMS) is an IEEE FIPA compliant multi-agent application designed to provide real-time traffic conditions, predict potential incidents, and alert the Traffic Management Center (TMC) about predefined events. The system demonstrates the key characteristics of mobile agent-based software systems: **mobility**, **concurrency**, and **communication** (both local and remote, synchronous and asynchronous).

## 2. System Architecture

The system employs a hierarchical multi-level architecture consisting of **four types of stationary agents** and **one type of mobile agent**:

### 2.1 Stationary Agents

1. **Laser Detector Agent (LRDA)** - Located at laser detector stations on the freeway
2. **Video Camera Detector Agent (VCDA)** - Located at video camera positions
3. **Traffic Detection Execution System Agent (TDESA)** - Coordinates agents within a freeway sub-network
4. **Traffic Management Center Agent (TMCA)** - Central coordination at the TMC facility

### 2.2 Mobile Agent

5. **Mobile Information Collector (MIC)** - Dynamically created by TMCA to perform specific missions at external agencies

## 3. Agent Descriptions

### 3.1 Laser Detector Agent (LRDA)

**Location:** Fixed at laser detector stations along the freeway

**Responsibilities:**
- Process real-time data from physical laser detectors
- Perform vehicle re-identification between detection points
- Estimate travel time and vehicle density on freeway segments
- Detect incidents based on travel time anomalies
- Send alerts to TDESA when predefined events occur (excessive travel time, abnormal density)

**Key Behaviors:**
- Continuously monitors laser detector hardware
- Compares current travel times against normal thresholds
- When travel time exceeds reasonable window, sends incident alert to TDESA via remote communication
- Adapts detection algorithms dynamically based on traffic patterns

### 3.2 Video Camera Detector Agent (VCDA)

**Location:** Fixed at video camera installations

**Responsibilities:**
- Control video cameras to verify suspected incidents
- Analyze video streams to obtain detailed incident information
- Provide exact incident location and type
- Respond to verification requests from TDESA

**Key Behaviors:**
- Receives verification requests from TDESA (remote communication)
- Selects appropriate camera(s) to cover suspected incident area
- Applies video analysis algorithms to confirm incidents
- Sends verification results back to TDESA (remote communication)

### 3.3 Traffic Detection Execution System Agent (TDESA)

**Location:** One per freeway sub-network

**Responsibilities:**
- Coordinate all LRDAs and VCDAs within its sub-network
- Maintain registry of subordinate agents and their capabilities
- Communicate with TMCA (upper level)
- Process incident alerts from LRDAs
- Request incident verification from VCDAs
- Escalate confirmed incidents to TMCA

**Key Behaviors:**
- Registers subordinate agents when they join the system
- Receives asynchronous incident alerts from LRDAs
- Coordinates with VCDAs for incident verification (synchronous communication)
- Performs concurrent processing: verifying incidents while escalating to TMCA
- Sends consolidated incident reports to TMCA (remote communication)

### 3.4 Traffic Management Center Agent (TMCA)

**Location:** Central Traffic Management Center

**Responsibilities:**
- Interface with TMC personnel through graphical user interface (GUI)
- Monitor all TDESA agents in the region
- Receive and analyze incident reports from TDESA
- Create and dispatch mobile agents (MICs) for specific missions
- Track active mobile agents
- Receive results from returning mobile agents
- Present information to TMC personnel for decision-making

**Key Behaviors:**
- Displays real-time system status on GUI
- Processes incident reports from multiple TDESAs
- Creates MIC agents with specific mission parameters upon user command
- Dispatches MICs to external agencies (police stations, emergency services, maintenance departments)
- Receives progress updates from MICs during their missions
- Displays consolidated results when MICs complete their missions

### 3.5 Mobile Information Collector (MIC)

**Initial Location:** TMCA (created on demand)

**Responsibilities:**
- Travel to specified external agencies
- Authenticate at each visited location
- Collect specific information as requested by TMC personnel
- Send progress updates to TMCA during mission
- Consolidate collected data from multiple sources
- Return to TMCA with comprehensive report
- Operate autonomously when disconnected from TMCA

**Key Behaviors:**
- Migrates from TMCA to first agency (migration action)
- Authenticates and collects data at each agency
- Sends remote communications (progress updates) to TMCA while on mission
- Migrates between multiple agencies following predefined itinerary
- Performs parallel data validation after visiting all agencies
- Handles network failures with retry mechanisms
- Returns to TMCA with consolidated results (migration action)
- Transfers final report to TMCA (remote communication)

## 4. Detailed Scenario: Multi-Agency Data Collection Mission

### 4.1 Scenario Context

An incident is detected and confirmed on the freeway. The TMC personnel need comprehensive information from multiple external agencies to coordinate an effective response. A Mobile Information Collector (MIC) is created to visit three agencies sequentially and return with consolidated information.

### 4.2 Scenario Execution Flow

#### **Phase 1: Incident Detection (LRDA)**

1. **LRDA** continuously monitors laser detector data
2. LRDA detects vehicle with excessive travel time between detection points
3. LRDA calculates: Actual_Travel_Time (15 minutes) >> Normal_Travel_Time (5-7 minutes)
4. LRDA transitions: `Monitoring → Analyzing → DetectingIncident`
5. **Remote Communication (R):** LRDA sends `sendIncidentAlert(TDESA)` message
6. LRDA transitions: `DetectingIncident → Monitoring`

#### **Phase 2: Incident Verification (TDESA and VCDA)**

7. **TDESA** receives incident alert from LRDA
8. TDESA transitions: `NormalOperation → IncidentProcessing`
9. **Remote Communication (R):** TDESA sends `sendVerificationRequest(VCDA)` to appropriate VCDA
10. **VCDA** receives verification request
11. VCDA transitions: `Idle → Verifying → AnalyzingVideo`
12. VCDA captures video stream and performs analysis
13. VCDA confirms incident (multi-vehicle accident, 2 lanes blocked)
14. **Remote Communication (R):** VCDA sends `sendVerificationReport(TDESA)`
15. VCDA transitions: `AnalyzingVideo → Idle`

#### **Phase 3: Incident Escalation (TDESA to TMCA)**

16. **TDESA** receives verification report from VCDA
17. TDESA performs parallel processing:
    - **Branch 1:** Analyzes verification report
    - **Branch 2:** Prepares escalation to TMCA
18. **Remote Communication (R):** TDESA sends `sendIncidentReport(TMCA)`
19. TDESA transitions: `IncidentProcessing → NormalOperation`

#### **Phase 4: Incident Handling and MIC Creation (TMCA)**

20. **TMCA** receives incident report from TDESA
21. TMCA transitions: `NormalMonitoring → IncidentHandling`
22. TMCA displays incident information in GUI
23. TMC personnel reviews incident and decides to gather additional information
24. User issues command to create mobile agent
25. TMCA transitions: `IncidentHandling → NormalMonitoring → CreatingMIC`
26. TMCA creates MIC with mission parameters:
    - **Itinerary:** Police_Station_District_7 → Emergency_Services_Center → Road_Maintenance_Depot → TMCA
    - **Required Information:** Incident details, ambulance status, cleanup equipment availability
    - **Credentials:** Authentication tokens for each agency
    - **Time Limit:** Maximum mission duration
27. TMCA transitions: `CreatingMIC → DispatchingMIC`
28. **Remote Communication (R):** TMCA sends `sendDispatchCommand(MIC)`
29. TMCA transitions: `DispatchingMIC → TrackingMIC`

#### **Phase 5: MIC Mission Execution - Location 1 (Police Station)**

30. **MIC** receives dispatch command
31. MIC transitions: `Created → TravelingToPolice`
32. **Migration (M):** MIC migrates from TMCA to Police_Station_District_7
33. MIC transitions: `TravelingToPolice → PoliceStationOperations`
34. MIC enters composite state: `PoliceStationOperations`
35. MIC authenticates with police agency system
36. MIC queries police database for incident information
37. MIC collects data: units dispatched, estimated clearance time, injury status
38. MIC packages collected data
39. **Remote Communication (R):** MIC sends `sendProgressUpdate(TMCA)` - Progress update #1
40. MIC exits: `PoliceStationOperations`
41. **TMCA** receives progress update, displays in GUI

#### **Phase 6: MIC Mission Execution - Location 2 (Emergency Services)**

42. MIC transitions: `PoliceStationOperations → TravelingToEmergency`
43. **Migration (M):** MIC migrates from Police_Station_District_7 to Emergency_Services_Center
44. MIC transitions: `TravelingToEmergency → EmergencyServicesOperations`
45. MIC enters composite state: `EmergencyServicesOperations`
46. MIC authenticates with emergency services system
47. MIC queries for ambulance status and availability
48. MIC collects data: ambulances dispatched, hospital transport status, additional units available
49. MIC packages collected data
50. **Remote Communication (R):** MIC sends `sendProgressUpdate(TMCA)` - Progress update #2
51. MIC exits: `EmergencyServicesOperations`
52. **TMCA** receives progress update, displays in GUI

#### **Phase 7: MIC Mission Execution - Location 3 (Maintenance Depot)**

53. MIC transitions: `EmergencyServicesOperations → TravelingToMaintenance`
54. **Migration (M):** MIC migrates from Emergency_Services_Center to Road_Maintenance_Depot
55. MIC transitions: `TravelingToMaintenance → MaintenanceDepotOperations`
56. MIC enters composite state: `MaintenanceDepotOperations`
57. MIC authenticates with maintenance depot system
58. MIC queries for cleanup equipment status
59. MIC collects data: tow trucks available, estimated arrival time, road closure equipment
60. MIC packages collected data
61. **Remote Communication (R):** MIC sends `sendProgressUpdate(TMCA)` - Progress update #3
62. MIC exits: `MaintenanceDepotOperations`
63. **TMCA** receives progress update, displays in GUI

#### **Phase 8: Data Consolidation (Parallel Processing)**

64. MIC transitions: `MaintenanceDepotOperations → ConsolidatingAllData`
65. MIC enters **parallel composite state:** `ConsolidatingAllData`
66. **Concurrent execution** of three validation processes:
    - **Thread 1:** Validates police data (completeness and consistency checks)
    - **Thread 2:** Validates emergency services data (completeness and consistency checks)
    - **Thread 3:** Validates maintenance depot data (completeness and consistency checks)
67. All three validations complete successfully
68. MIC transitions: `ConsolidatingAllData → MergingResults`
69. MIC merges validated data from all three sources
70. MIC transitions: `MergingResults → CreatingFinalReport`
71. MIC creates comprehensive consolidated report

#### **Phase 9: Return to TMCA**

72. MIC transitions: `CreatingFinalReport → ReturningToTMCA`
73. **Migration (M):** MIC migrates from Road_Maintenance_Depot back to TMCA
74. MIC transitions: `ReturningToTMCA → ReportingToTMCA`
75. **Remote Communication (R):** MIC sends `sendResults(TMCA)` - Final consolidated report
76. **TMCA** receives final results
77. TMCA transitions: `TrackingMIC → ReceivingResults`
78. TMCA displays consolidated report in GUI
79. TMCA terminates MIC agent
80. TMCA transitions: `ReceivingResults → MonitoringSystem`
81. MIC transitions: `ReportingToTMCA → Terminated → [*]`

#### **Phase 10: Decision Making**

82. TMC personnel review consolidated report containing:
    - Police: 2 patrol cars on scene, 45-minute estimated clearance
    - Emergency: 1 ambulance dispatched, 2 minor injuries, 1 serious injury transported
    - Maintenance: 2 tow trucks available, 20-minute ETA
83. Based on complete information, personnel make informed decisions:
    - Activate variable message signs to divert traffic
    - Notify public through traveler information system
    - Coordinate lane closure with police
    - Dispatch additional support if needed

### 4.3 Failure Handling Scenarios

#### **Network Failure During Migration**

- If MIC experiences network failure during any migration (e.g., TravelingToEmergency)
- MIC transitions: `TravelingToEmergency → WaitingForNetwork`
- MIC waits for network restoration
- When network restored: `WaitingForNetwork → RetryingMigration → TravelingToEmergency`
- If timeout exceeded: `WaitingForNetwork → AbortingMission → ReturningToTMCA`

#### **Authentication Failure at Agency**

- If MIC authentication fails at any agency (e.g., at Emergency Services)
- MIC transitions: `AuthenticatingEmergency → WaitingRetryAuth`
- MIC retries after timeout
- If successful: `WaitingRetryAuth → AuthenticatingEmergency → QueryingEmergencyStatus`
- If max retries exceeded: `WaitingRetryAuth → AbortingMission → ReturningToTMCA`

## 5. Key System Properties

### 5.1 Mobility Properties

- **4 Migration Actions (M)** in complete MIC mission:
  1. TMCA → Police_Station_District_7
  2. Police_Station_District_7 → Emergency_Services_Center
  3. Emergency_Services_Center → Road_Maintenance_Depot
  4. Road_Maintenance_Depot → TMCA

- MIC physically moves across network infrastructure
- Agent code and state migrate to each location
- Autonomous operation at each destination

### 5.2 Communication Properties

#### Remote Communications (R):
- **LRDA → TDESA:** Asynchronous incident alerts
- **TDESA → VCDA:** Synchronous verification requests
- **VCDA → TDESA:** Synchronous verification responses
- **TDESA → TMCA:** Asynchronous incident reports
- **TMCA → MIC:** Dispatch command
- **MIC → TMCA:** Progress updates (3) and final results (1) - Total 4 remote communications

#### Local Communications:
- Internal state transitions within composite states
- Authentication exchanges at agencies
- Database queries at each location

### 5.3 Concurrency Properties

- **TDESA:** Parallel processing of incident verification and escalation
- **MIC:** Parallel validation of three data sources (police, emergency, maintenance)
- **System-wide:** Multiple agents operate concurrently without interference

### 5.4 Verification Properties

The system demonstrates properties suitable for formal verification using π-calculus:

- **Safety:** No unauthorized data access, no data corruption during migration
- **Liveness:** Every incident eventually detected, every MIC eventually completes or times out
- **Mobility:** MIC successfully migrates through predefined itinerary
- **Concurrent Correctness:** Parallel validations produce consistent results

## 6. System Interaction Summary

| Agent Type | Role | Mobility | Communication Type |
|------------|------|----------|-------------------|
| LRDA | Incident Detection | Stationary | Remote (R) - Send alerts |
| VCDA | Incident Verification | Stationary | Remote (R) - Receive requests, send reports |
| TDESA | Sub-network Coordination | Stationary | Remote (R) - Coordinate between layers |
| TMCA | Central Management | Stationary | Remote (R) - Command and control |
| MIC | Information Collection | Mobile | Migration (M) + Remote (R) |

## 7. Comparison with Voting System

The ABRTTDMS case study adds realistic complexity in comparison of the voting system :
- Hierarchical coordination (LRDA/VCDA → TDESA → TMCA)
- Easy extension to multiple mobile agent instances (concurrent MICs)
- Parallel data validation
- Real-time monitoring and verification loops
- Failure handling with autonomous recovery

---

The complete mobile state diagrams (MSDs) for all agents are given bellow.

## 8. ABRTTDMS Modeling using MSDs 
We provide here the diagrams used to model the case study modeled in our tool:

<img width="1233" height="319" alt="graphviz (1)" src="https://github.com/user-attachments/assets/1837a6dc-94d6-46c9-8eee-d1873fbe9478" />

*Figure 1: Laser Detector Agent (LRDA) MSD.*

<img width="787" height="736" alt="graphviz (2)" src="https://github.com/user-attachments/assets/5713e450-e3b9-4fad-a85a-6dc768067455" />

*Figure 2: Video Camera Detector Agent (VCDA) MSD.*

<img width="627" height="1248" alt="graphviz (1)" src="https://github.com/user-attachments/assets/9a9f2d55-b31e-481a-8ed6-cf5487ee812c" />

*Figure 3: Traffic Detection Execution System Agent (TDESA) MSD.*

<img width="912" height="960" alt="graphviz (3)" src="https://github.com/user-attachments/assets/49e74009-66f6-4c43-b36e-994756e4cc61" />

*Figure 4: Traffic Management Center Agent (TMCA) MSD.*

<img width="6000" height="3500" alt="graphviz (4)" src="https://github.com/user-attachments/assets/8b674d49-47e8-4477-9b92-e2ffe78dbe2d" />

*Figure 5: Mobile Information Collector (MIC) MSD.*

## 9. ABRTTDMS MSDs-generated pi-calculus code

Using our developed tool, we can immediately generate the pi-calculus code corresponding to the diagrams.
The code is mainly shown and explained in this document: [pdf](https://github.com/isso84/ABRTTDMS-modeling-and-verification/blob/main/paper_ACM_approch_only_Generated_picalculus.pdf).
The syntax of the code in the document is abstract; however, the tool automatically generates the concrete syntax (e.g., the restriction $\nu$ as ^, the output action $\overline{x}$ as 'x, the internal action $\tau$ as t. Each process identifier expression starts with the keyword $agent$, and processes are closed), which is accepted by analysis tools. It is given in this textual file: [txt](https://github.com/isso84/ABRTTDMS-modeling-and-verification/blob/main/micsystem.pic) 

## 10. ABRTTDMS verification in CADP

The analysis and verification of the ABRTTDMS system in CADP start by converting the Pi-calculus code into LNT code using the CADP tool: [PIC2LNT](https://convecs.inria.fr/software/pic2lnt/)

This results in the generation of a labelled transition system (LTS), which represents the dynamic behavior of the LNT code with a set of states and transitions (the system state space).

After that, we need to construct the BCG (Binary Coded Graphs), which is a binary file format for LTSs that enables numerous types of analysis and verification. In fact, BCG is both a format for the LTS representation and a set of libraries and programs dealing with LTSs (information, display, edition, minimization, etc.).

BCGs are generated from higher-level models of concurrent systems. We show the BCG graph constructed by CADP for our ABRTTDMS system in the attached file: [PS](https://github.com/isso84/ABRTTDMS-modeling-and-verification/blob/main/aissam_bcg_draw_rZT1tb.ps). The BCG file itself is downloadable here: [BCG](https://github.com/isso84/ABRTTDMS-modeling-and-verification/blob/main/micsystem.bcg)

As we can notice, the LTS has a huge number of states and transitions, so the graph is very dense. CADP offers the possibility to reduce this huge number by using Strong Equivalence, which results in another equivalent graph with fewer states and transitions.

Then, CADP offers multiple built-in verification tools, as indicated in the following figure, and we can start checking whatever properties we want using these tools.
<img width="1249" height="796" alt="Screenshot from 2026-03-10 10-33-53" src="https://github.com/user-attachments/assets/5386a7cd-fd35-444d-b1a1-65da11f30bdd" />

### 10.1 Model checking
Model checking is straightforward in CADP, which offers multiple cutting-edge model checking algorithms. The tool also provides the possibility of using multiple formats to express the properties we are interested in verifying, such as .mcl, .xtl, and even automated scripts using SVG. As model checking is considered one of the main objectives of our approach, we show in the following lines examples of the verification of some properties. We use the option "verify temporal formulas", where we can choose the properties to verify and the algorithms we want to apply, as indicated in the figure:

<img width="452" height="619" alt="Screenshot from 2026-03-11 06-07-27" src="https://github.com/user-attachments/assets/f193c91d-e320-4f6e-a85e-5b8e13cca843" />

## Property Specifications
The specifications of the properties can be downloaded from here: [PropertiesToVerify](https://github.com/isso84/ABRTTDMS-modeling-and-verification/blob/main/propertiesToVerify.rar)

### Safety Properties

**SP1.mcl**(* SP1: MIC never executes a query without prior successful authentication *)
```mcl
[
  (not 'PUBLIC .*authenticationSuccess.*')* .
  'PUBLIC .*queryExecuted.*'
] false
```

**SP2.mcl**(* SP2: packagingComplete always occurs before the next location arrival *)
```mcl
[
  'PUBLIC .*arrivedAtPolice.*' .
  (not 'PUBLIC .*packagingComplete.*')* .
  'PUBLIC .*arrivedAtEmergency.*'
] false
and
[
  'PUBLIC .*arrivedAtEmergency.*' .
  (not 'PUBLIC .*packagingComplete.*')* .
  'PUBLIC .*arrivedAtMaintenance.*'
] false
```

**SP3.mcl**(* SP3: incidentReport is never sent before verificationReport is received *)
```mcl
[
  (not 'PUBLIC .*verificationReport.*')* .
  'PUBLIC .*incidentReport.*'
] false
```

**SP4.mcl**(* SP4: allValidationsComplete never fires twice without mergeComplete in between *)
```mcl
[
  'PUBLIC .*allValidationsComplete.*' .
  (not 'PUBLIC .*mergeComplete.*')* .
  'PUBLIC .*allValidationsComplete.*'
] false
```
The results of checking the safety properties in CADP are given in the following figure:
<img width="1269" height="815" alt="Screenshot from 2026-03-10 09-58-54" src="https://github.com/user-attachments/assets/2bdb03de-cbc3-4ff9-87bb-5437c06260b8" />

### Liveness Properties

**LP1.mcl**(* LP1: after incidentAlert, arrivedAtTMCA is inevitably reachable *)
```mcl
(* LP1: After incidentAlert, arrivedAtTMCA is inevitably reachable *)

[
  true* .
  'PUBLIC .*incidentAlert.*'
]
  (
    [
      (not 'PUBLIC .*arrivedAtTMCA.*')*
    ] false
  )
```

**LP2.mcl**(* LP2: after dispatchCommand, either releaseResources or timeoutExceeded is reached *)
```mcl
(* LP2: After dispatchCommand, either releaseResources or timeoutExceeded is reached *)

[
  true* .
  'PUBLIC .*dispatchCommand.*'
]
  (
    [
      (not 'PUBLIC .*releaseResources.*')* .
      (not 'PUBLIC .*timeoutExceeded.*')*
    ] false
  )
```

**LP3.mcl**(* LP3: after verificationRequest, verificationReport is inevitably reachable *)
```mcl
(* LP3: After verificationRequest, verificationReport is inevitably reachable *)

[
  true* .
  'PUBLIC .*verificationRequest.*'
]
  (
    [
      (not 'PUBLIC .*verificationReport.*')*
    ] false
  )
```

**LP4.mcl**(* LP4: after networkFailure followed by retryTimerExpired, system is operational *)
```mcl
(* LP4: After networkFailure, retry mechanism eventually activates *)

[
  true* .
  'PUBLIC .*networkFailure.*'
]
  (
    [
      (not 'PUBLIC .*retryTimerExpired.*')*
    ] false
  )
```

The results of checking the liveness properties in CADP are given in the following figure:
<img width="1157" height="729" alt="Screenshot from 2026-03-11 05-08-14" src="https://github.com/user-attachments/assets/54575c91-02ee-421e-97f5-694aca0dcd94" />


### Mobility Properties

**MP1.mcl**(* MP1a: Police is always visited before Emergency *)
```mcl
(* MP1: MIC follows itinerary Police -> Emergency -> Maintenance -> TMCA *)

(* Part 1: Police must come before Emergency *)
[
  (not 'PUBLIC .*arrivedAtPolice.*')* .
  'PUBLIC .*arrivedAtEmergency.*'
] false
and
(* Part 2: Emergency must come before Maintenance *)
[
  (not 'PUBLIC .*arrivedAtEmergency.*')* .
  'PUBLIC .*arrivedAtMaintenance.*'
] false
and
(* Part 3: After Police, Emergency is eventually reached *)
[
  true* .
  'PUBLIC .*arrivedAtPolice.*'
]
  (
    [
      (not 'PUBLIC .*arrivedAtEmergency.*')*
    ] false
  )
and
(* Part 4: After Emergency, Maintenance is eventually reached *)
[
  true* .
  'PUBLIC .*arrivedAtEmergency.*'
]
  (
    [
      (not 'PUBLIC .*arrivedAtMaintenance.*')*
    ] false
  )
and
(* Part 5: After Maintenance, TMCA is eventually reached *)
[
  true* .
  'PUBLIC .*arrivedAtMaintenance.*'
]
  (
    [
      (not 'PUBLIC .*arrivedAtTMCA.*')*
    ] false
  )
```

**MP2.mcl**(* MP2: after prepareFailureReport (AbortingMission), arrivedAtTMCA is reachable *)
```mcl
(* MP2: After mission abort, arrivedAtTMCA is reachable *)

[
  true* .
  'PUBLIC .*prepareFailureReport.*'
]
  (
    [
      (not 'PUBLIC .*arrivedAtTMCA.*')*
    ] false
  )
```

**MP3.mcl**(* MP3a: agent cannot be at Police and Emergency at the same time *)
```mcl
(* MP3: Agent completes operations at each location before moving to next *)

(* Part 1: Cannot arrive at Emergency before completing Police mission *)
[
  'PUBLIC .*arrivedAtPolice.*' .
  (not 'PUBLIC .*missionStep1Complete.*')* .
  'PUBLIC .*arrivedAtEmergency.*'
] false
and
(* Part 2: Cannot arrive at Maintenance before completing Emergency mission *)
[
  'PUBLIC .*arrivedAtEmergency.*' .
  (not 'PUBLIC .*missionStep2Complete.*')* .
  'PUBLIC .*arrivedAtMaintenance.*'
] false
and
(* Part 3: Cannot loop back to Police before completing Maintenance mission *)
[
  'PUBLIC .*arrivedAtMaintenance.*' .
  (not 'PUBLIC .*missionStep3Complete.*')* .
  'PUBLIC .*arrivedAtPolice.*'
] false
```

The results of checking the mobility properties in CADP are given in the following figure:

<img width="1157" height="729" alt="Screenshot from 2026-03-11 05-23-52" src="https://github.com/user-attachments/assets/39ea9f1e-5a7f-44b5-aae1-a0cfb0c9cb62" />

### Details of Analysis and Verification
The details of Analysis and verification for all properties are given here (Verification Execution Log):

```bash
aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ export PIC=~/pic2lnt

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time pic2lnt micsystem.pic

real    0m0.183s
user    0m0.174s
sys     0m0.007s

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time $PIC/com/pic2bcg micsystem.pic

// ...
// ... detailed information about the construction of the BCG ...
// ...

     termination on SOFTWARE_TERMINATION signal (files completed)
     quit

real    7m38.166s
user    7m33.350s
sys     0m4.320s

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ bcg_info micsystem.bcg

./micsystem.bcg:
created by caesar
    79706 states
    79708 transitions
    39855 labels

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time bcg_open "micsystem.bcg" evaluator -bfs -diag evaluator.bcg "./SP1.mcl"

bcg_open: using ``/home/aissam/cadp/bin.x64/evaluator.a''
bcg_open: running ``evaluator -bfs -diag evaluator.bcg ./SP1.mcl'' for ``./micsystem.bcg''

TRUE
(consult diagnostic in file ``evaluator.bcg'')

real    0m22.983s
user    0m22.377s
sys     0m0.424s

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time bcg_open "micsystem.bcg" evaluator -bfs -diag evaluator.bcg "./SP2.mcl"

bcg_open: using ``/home/aissam/cadp/bin.x64/evaluator.a''
bcg_open: running ``evaluator -bfs -diag evaluator.bcg ./SP2.mcl'' for ``./micsystem.bcg''

TRUE
(empty diagnostic in file ``evaluator.bcg'')

real    0m0.528s
user    0m0.236s
sys     0m0.176s

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time bcg_open "micsystem.bcg" evaluator -bfs -diag evaluator.bcg "./SP3.mcl"

bcg_open: using ``/home/aissam/cadp/bin.x64/evaluator.a''
bcg_open: running ``evaluator -bfs -diag evaluator.bcg ./SP3.mcl'' for ``./micsystem.bcg''

TRUE
(consult diagnostic in file ``evaluator.bcg'')

real    0m21.524s
user    0m21.098s
sys     0m0.306s

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time bcg_open "micsystem.bcg" evaluator -bfs -diag evaluator.bcg "./SP4.mcl"

bcg_open: using ``/home/aissam/cadp/bin.x64/evaluator.a''
bcg_open: running ``evaluator -bfs -diag evaluator.bcg ./SP4.mcl'' for ``./micsystem.bcg''

TRUE
(empty diagnostic in file ``evaluator.bcg'')

real    0m0.587s
user    0m0.227s
sys     0m0.221s

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time bcg_open "micsystem.bcg" evaluator -bfs -diag evaluator.bcg "./LP1.mcl"

bcg_open: using ``/home/aissam/cadp/bin.x64/evaluator.a''
bcg_open: running ``evaluator -bfs -diag evaluator.bcg ./LP1.mcl'' for ``./micsystem.bcg''

TRUE
(consult diagnostic in file ``evaluator.bcg'')

real    0m20.554s
user    0m19.938s
sys     0m0.466s

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time bcg_open "micsystem.bcg" evaluator -bfs -diag evaluator.bcg "./LP2.mcl"

bcg_open: using ``/home/aissam/cadp/bin.x64/evaluator.a''
bcg_open: running ``evaluator -bfs -diag evaluator.bcg ./LP2.mcl'' for ``./micsystem.bcg''

TRUE
(consult diagnostic in file ``evaluator.bcg'')

real    0m23.119s
user    0m22.470s
sys     0m0.502s

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time bcg_open "micsystem.bcg" evaluator -bfs -diag evaluator.bcg "./LP3.mcl"

bcg_open: using ``/home/aissam/cadp/bin.x64/evaluator.a''
bcg_open: running ``evaluator -bfs -diag evaluator.bcg ./LP3.mcl'' for ``./micsystem.bcg''

TRUE
(consult diagnostic in file ``evaluator.bcg'')

real    0m24.427s
user    0m23.953s
sys     0m0.331s

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time bcg_open "micsystem.bcg" evaluator -bfs -diag evaluator.bcg "./LP4.mcl"

bcg_open: using ``/home/aissam/cadp/bin.x64/evaluator.a''
bcg_open: running ``evaluator -bfs -diag evaluator.bcg ./LP4.mcl'' for ``./micsystem.bcg''

TRUE
(consult diagnostic in file ``evaluator.bcg'')

real    0m28.057s
user    0m27.587s
sys     0m0.324s

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time bcg_open "micsystem.bcg" evaluator -bfs -diag evaluator.bcg "./MP1.mcl"

bcg_open: using ``/home/aissam/cadp/bin.x64/evaluator.a''
bcg_open: running ``evaluator -bfs -diag evaluator.bcg ./MP1.mcl'' for ``./micsystem.bcg''

TRUE
(consult diagnostic in file ``evaluator.bcg'')

real    2m52.310s
user    2m51.753s
sys     0m0.389s

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time bcg_open "micsystem.bcg" evaluator -bfs -diag evaluator.bcg "./MP2.mcl"

bcg_open: using ``/home/aissam/cadp/bin.x64/evaluator.a''
bcg_open: running ``evaluator -bfs -diag evaluator.bcg ./MP2.mcl'' for ``./micsystem.bcg''

TRUE
(consult diagnostic in file ``evaluator.bcg'')

real    0m22.512s
user    0m21.911s
sys     0m0.414s

aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ time bcg_open "micsystem.bcg" evaluator -bfs -diag evaluator.bcg "./MP3.mcl"

bcg_open: using ``/home/aissam/cadp/bin.x64/evaluator.a''
bcg_open: running ``evaluator -bfs -diag evaluator.bcg ./MP3.mcl'' for ``./micsystem.bcg''

TRUE
(empty diagnostic in file ``evaluator.bcg'')

real    0m0.640s
user    0m0.280s
sys     0m0.233s
aissam@aissam-VirtualBox:~/pic2lnt/MICsystem$ xeuca
//...
//...use the graphical editor XEUCA for additional built-in one-click verification...
//...
```






### 10.2 Other verification capabilities

As indicated earlier, CADP offers a large palette of verification tools. Thus, it is also possible to check in ABRTTDMS the existence of any deadlock, unreachable states, livelock, and other properties using built-in verification tools inside CADP, as shown in the first figure.

Therefore, it is possible to simulate the behavior of agents in ABRTTDMS by executing their pi-calculus code using the "Execution sequences" or "Find path to state" options.

Another option is to draw in real time the LTS of the BCG and edit it immediately if we are interested in visualizing specific parts of the LTS in order to better understand the system.

We can also use another very interesting tool in pi-calculus, which is equivalence checking. In fact, CADP offers many algorithms to check different types of equivalence. For example, we can check that the agents LRDA and TMCA have similar behavior in a part of their executions. Observational equivalence checking in CADP can detect such a property easily.

Finally, we can also show the performance statistics of every verification tool executed (a file is generated for every tool executed and contains all the details of the operation), or obtain general statistics about the whole process.
