# Agent-Based Real-Time Traffic Detection and Management System (ABRTTDMS)

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
- Multiple mobile agent instances (concurrent MICs)
- Parallel data validation
- Real-time monitoring and verification loops
- Failure handling with autonomous recovery

---

**Note:** This case study is adapted from the Mobile-C traffic detection system available at: https://www.mobilec.org/apps/lbds/

The complete state machine diagrams for all agents are given bellow.

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

Using our developed tool, we can immediatley generate the pi-calculus code corresponding to the diagrams. 
The code is partially shown and explained in this document: [pdf](https://github.com/isso84/ABRTTDMS-modeling-and-verification/blob/main/paper_ACM_approch_only_Generated_picalculus.pdf).

## 10. ABRTTDMS verification in CADP


