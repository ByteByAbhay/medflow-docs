# 📊 Phase 2: IPD Admission - Workflow Diagrams

**Document Version:** 1.0  
**Last Updated:** February 27, 2026  
**Status:** Draft

---

## 1. Document Purpose

This document provides **visual workflow diagrams** for the IPD Admission Module, illustrating process flows, decision points, and system interactions.

---

## 2. Complete Admission Workflow

### 2.1 End-to-End Admission Process

```mermaid
flowchart TD
    Start([Patient Requires Admission]) --> Source{Admission Source}
    
    Source -->|From OPD| OPD[Doctor Creates Admission Request]
    Source -->|Emergency| Emergency[Direct Emergency Admission]
    
    OPD --> Request[Admission Request Created]
    Request --> Queue[Request in Approval Queue]
    Queue --> Review{Nurse Reviews}
    
    Review -->|Approve| Approved[Status: Approved]
    Review -->|Reject| Rejected[Status: Rejected]
    Review -->|Need Info| MoreInfo[Request More Info]
    
    Rejected --> NotifyDoc[Notify Doctor]
    NotifyDoc --> End1([End])
    
    MoreInfo --> Doctor[Doctor Provides Info]
    Doctor --> Queue
    
    Approved --> BedAlloc[Bed Allocation Queue]
    Emergency --> BedAlloc
    
    BedAlloc --> SelectBed[Nurse Selects Available Bed]
    SelectBed --> AllocBed{Bed Available?}
    
    AllocBed -->|No| WaitBed[Wait for Bed]
    AllocBed -->|Yes| LockBed[Lock Bed]
    
    WaitBed --> BedAlloc
    
    LockBed --> AdmitForm[Fill Admission Form]
    AdmitForm --> Consent{All Consents?}
    
    Consent -->|No| GetConsent[Obtain Consents]
    GetConsent --> AdmitForm
    
    Consent -->|Yes| Vitals[Record Vitals]
    Vitals --> CreateAdm[Create Admission Record]
    CreateAdm --> UpdateBed[Update Bed Status: Occupied]
    UpdateBed --> NotifyWard[Notify Ward Nurse]
    NotifyWard --> Admitted[Status: Admitted]
    
    Admitted --> Care[Patient Care Period]
    Care --> DischargeReady{Ready for Discharge?}
    
    DischargeReady -->|No| Care
    DischargeReady -->|Yes| InitDischarge[Doctor Initiates Discharge]
    
    InitDischarge --> DischargeSummary[Create Discharge Summary]
    DischargeSummary --> BillingCheck{Billing Cleared?}
    
    BillingCheck -->|No| GenerateBill[Generate Final Bill]
    GenerateBill --> CollectPayment[Collect Payment]
    CollectPayment --> BillingCheck
    
    BillingCheck -->|Yes| CompleteDischarge[Complete Discharge]
    CompleteDischarge --> ReleaseBed[Release Bed]
    ReleaseBed --> Discharged[Status: Discharged]
    Discharged --> End2([End])
```

---

## 3. Detailed Workflow Diagrams

### 3.1 Admission Request Creation (From OPD)

```mermaid
flowchart TD
    Start([Doctor Consultation]) --> Decision{Patient Needs Admission?}
    
    Decision -->|No| Continue[Continue OPD Care]
    Continue --> End1([End])
    
    Decision -->|Yes| SelectDecision[Select 'Admission Required']
    SelectDecision --> Form[Open Admission Request Form]
    
    Form --> FillReason[Enter Admission Reason]
    FillReason --> FillDiagnosis[Enter Provisional Diagnosis]
    FillDiagnosis --> SelectWard[Select Ward Type Preference]
    SelectWard --> SelectUrgency[Select Urgency Level]
    SelectUrgency --> EnterStay[Enter Estimated Stay]
    EnterStay --> WriteOrders[Write Admission Orders]
    WriteOrders --> SpecialReq[Add Special Requirements]
    SpecialReq --> DietInst[Add Diet Instructions]
    
    DietInst --> Validate{Form Valid?}
    
    Validate -->|No| ShowErrors[Show Validation Errors]
    ShowErrors --> Form
    
    Validate -->|Yes| Submit[Submit Request]
    Submit --> GenerateNumber[Generate Request Number]
    GenerateNumber --> SaveDB[Save to Database]
    SaveDB --> NotifyNurse[Notify Nurse]
    NotifyNurse --> ConfirmDoc[Confirm to Doctor]
    ConfirmDoc --> End2([Request Created])
```

---

### 3.2 Admission Request Approval Process

```mermaid
flowchart TD
    Start([Admission Request Received]) --> Queue[Request in Queue]
    Queue --> NurseView[Nurse Views Request]
    
    NurseView --> ReviewInfo[Review Patient Info]
    ReviewInfo --> ReviewReason[Review Admission Reason]
    ReviewReason --> ReviewOrders[Review Admission Orders]
    ReviewOrders --> CheckBeds[Check Bed Availability]
    
    CheckBeds --> Decision{Approval Decision}
    
    Decision -->|Approve| EnterComments[Enter Approval Comments]
    EnterComments --> Approve[Click Approve]
    Approve --> UpdateStatus1[Status: Approved]
    UpdateStatus1 --> MoveToBedQueue[Move to Bed Allocation Queue]
    MoveToBedQueue --> NotifyDoc1[Notify Doctor]
    NotifyDoc1 --> End1([Approved])
    
    Decision -->|Reject| EnterReason[Enter Rejection Reason]
    EnterReason --> Reject[Click Reject]
    Reject --> UpdateStatus2[Status: Rejected]
    UpdateStatus2 --> NotifyDoc2[Notify Doctor]
    NotifyDoc2 --> End2([Rejected])
    
    Decision -->|Need More Info| EnterQuestion[Enter Questions/Comments]
    EnterQuestion --> RequestInfo[Click Request More Info]
    RequestInfo --> UpdateStatus3[Status: Info Requested]
    UpdateStatus3 --> NotifyDoc3[Notify Doctor]
    NotifyDoc3 --> WaitResponse[Wait for Doctor Response]
    WaitResponse --> DocResponds[Doctor Provides Info]
    DocResponds --> Queue
```

---

### 3.3 Bed Allocation Process

```mermaid
flowchart TD
    Start([Approved Admission]) --> BedQueue[Admission in Bed Allocation Queue]
    BedQueue --> NurseSelect[Nurse Selects Admission]
    
    NurseSelect --> ViewPatient[View Patient Details]
    ViewPatient --> ViewRequest[View Admission Request]
    ViewRequest --> CheckPref[Check Ward Preference]
    
    CheckPref --> FilterBeds[Filter Available Beds]
    FilterBeds --> ShowBeds{Beds Available?}
    
    ShowBeds -->|No| CheckOther[Check Other Wards]
    CheckOther --> AnyBeds{Any Beds Available?}
    
    AnyBeds -->|No| WaitList[Add to Waiting List]
    WaitList --> NotifyDelay[Notify Doctor of Delay]
    NotifyDelay --> End1([Waiting for Bed])
    
    AnyBeds -->|Yes| ShowAlternate[Show Alternate Beds]
    ShowAlternate --> SelectBed
    
    ShowBeds -->|Yes| SelectBed[Nurse Selects Bed]
    
    SelectBed --> ViewBedDetails[View Bed Details]
    ViewBedDetails --> ConfirmBed{Confirm Bed?}
    
    ConfirmBed -->|No| FilterBeds
    ConfirmBed -->|Yes| LockBed[Lock Bed]
    
    LockBed --> SetDateTime[Set Admission Date/Time]
    SetDateTime --> AssignDoctor[Assign Attending Doctor]
    AssignDoctor --> AddNotes[Add Admission Notes]
    
    AddNotes --> FinalConfirm{Final Confirmation}
    
    FinalConfirm -->|Cancel| UnlockBed[Unlock Bed]
    UnlockBed --> FilterBeds
    
    FinalConfirm -->|Confirm| CreateAdmission[Create Admission Record]
    CreateAdmission --> UpdateBedStatus[Bed Status: Occupied]
    UpdateBedStatus --> UpdateRequestStatus[Request Status: Completed]
    UpdateRequestStatus --> NotifyWard[Notify Ward Nurse]
    NotifyWard --> NotifyDoctor[Notify Attending Doctor]
    NotifyDoctor --> End2([Patient Admitted])
```

---

### 3.4 Emergency Admission Process

```mermaid
flowchart TD
    Start([Emergency Patient Arrival]) --> Reception[Nurse Receives Patient]
    Reception --> Assess{Life Threatening?}
    
    Assess -->|Yes| ImmediateCare[Immediate Medical Care]
    ImmediateCare --> Stabilize[Stabilize Patient]
    Stabilize --> PostStabilize[After Stabilization]
    PostStabilize --> CreateAdm
    
    Assess -->|No| CreateAdm[Create Emergency Admission]
    
    CreateAdm --> SearchPatient{Existing Patient?}
    
    SearchPatient -->|Yes| SelectPatient[Select Patient]
    SearchPatient -->|No| QuickReg[Quick Registration]
    
    QuickReg --> SelectPatient
    SelectPatient --> EmergencyForm[Fill Emergency Admission Form]
    
    EmergencyForm --> EnterReason[Enter Emergency Reason]
    EnterReason --> EnterDiagnosis[Enter Provisional Diagnosis]
    EnterDiagnosis --> SelectDoctor[Select Admitting Doctor]
    SelectDoctor --> AutoApprove[Auto-Approve Request]
    
    AutoApprove --> FindBed[Find Emergency Bed]
    FindBed --> EmergencyBed{Emergency Bed Available?}
    
    EmergencyBed -->|No| FindAnyBed[Find Any Available Bed]
    FindAnyBed --> AnyBed{Any Bed Available?}
    
    AnyBed -->|No| Escalate[Escalate to Supervisor]
    Escalate --> ManualAlloc[Manual Bed Allocation]
    ManualAlloc --> AllocateBed
    
    AnyBed -->|Yes| AllocateBed[Allocate Bed]
    EmergencyBed -->|Yes| AllocateBed
    
    AllocateBed --> QuickVitals[Record Quick Vitals]
    QuickVitals --> MinimalConsent[Obtain Minimal Consent]
    MinimalConsent --> CreateRecord[Create Admission Record]
    CreateRecord --> UpdateBed[Update Bed Status]
    UpdateBed --> NotifyAll[Notify Ward & Doctor]
    NotifyAll --> Admitted[Status: Admitted]
    Admitted --> FullDocLater[Complete Documentation Later]
    FullDocLater --> End([Emergency Admission Complete])
```

---

### 3.5 Bed Transfer Process

```mermaid
flowchart TD
    Start([Transfer Request]) --> TransferType{Transfer Type}
    
    TransferType -->|Intra-Ward| IntraWard[Same Ward Transfer]
    TransferType -->|Inter-Ward| InterWard[Different Ward Transfer]
    
    IntraWard --> SelectPatient1[Select Patient]
    SelectPatient1 --> ViewAvailBeds1[View Available Beds in Ward]
    ViewAvailBeds1 --> SelectNewBed1[Select New Bed]
    SelectNewBed1 --> EnterReason1[Enter Transfer Reason]
    EnterReason1 --> ConfirmTransfer1[Confirm Transfer]
    ConfirmTransfer1 --> ExecuteTransfer1[Execute Transfer]
    ExecuteTransfer1 --> End1([Transfer Complete])
    
    InterWard --> SelectPatient2[Select Patient]
    SelectPatient2 --> SelectTargetWard[Select Target Ward]
    SelectTargetWard --> ViewAvailBeds2[View Available Beds]
    ViewAvailBeds2 --> SelectNewBed2[Select New Bed]
    SelectNewBed2 --> EnterDetailedReason[Enter Detailed Reason]
    EnterDetailedReason --> RequestApproval[Request Doctor Approval]
    
    RequestApproval --> DoctorReview{Doctor Reviews}
    
    DoctorReview -->|Reject| NotifyNurse[Notify Nurse]
    NotifyNurse --> End2([Transfer Rejected])
    
    DoctorReview -->|Approve| ApproveTransfer[Approve Transfer]
    ApproveTransfer --> ExecuteTransfer2[Execute Transfer]
    
    ExecuteTransfer2 --> UpdateFromBed[Release Old Bed]
    UpdateFromBed --> UpdateToBed[Occupy New Bed]
    UpdateToBed --> UpdateAdmission[Update Admission Record]
    UpdateAdmission --> LogTransfer[Log Transfer History]
    LogTransfer --> UpdateBilling[Update Billing - Room Rate Change]
    UpdateBilling --> NotifyWards[Notify Both Wards]
    NotifyWards --> End3([Transfer Complete])
```

---

### 3.6 Discharge Process

```mermaid
flowchart TD
    Start([Patient Ready for Discharge]) --> DoctorInitiate[Doctor Initiates Discharge]
    
    DoctorInitiate --> DischargeForm[Open Discharge Form]
    DischargeForm --> SelectType[Select Discharge Type]
    
    SelectType --> TypeCheck{Discharge Type}
    
    TypeCheck -->|Regular| RegularFlow[Regular Discharge Flow]
    TypeCheck -->|LAMA| LAMAFlow[LAMA Flow]
    TypeCheck -->|Death| DeathFlow[Death Flow]
    
    RegularFlow --> EnterDiagnosis[Enter Final Diagnosis]
    EnterDiagnosis --> WriteHospitalCourse[Write Hospital Course]
    WriteHospitalCourse --> ListProcedures[List Procedures Performed]
    ListProcedures --> SummarizeInvestigations[Summarize Investigations]
    SummarizeInvestigations --> PrescribeMeds[Prescribe Discharge Medications]
    PrescribeMeds --> DietAdvice[Add Diet Advice]
    DietAdvice --> ActivityRestrictions[Add Activity Restrictions]
    ActivityRestrictions --> WarningSigns[List Warning Signs]
    WarningSigns --> FollowUpInstructions[Add Follow-up Instructions]
    FollowUpInstructions --> SetFollowUpDate[Set Follow-up Date]
    SetFollowUpDate --> ValidateForm
    
    LAMAFlow --> LAMAForm[Generate LAMA Form]
    LAMAForm --> GetSignature[Obtain Patient/Relative Signature]
    GetSignature --> UploadSignature[Upload Signed Form]
    UploadSignature --> MinimalSummary[Minimal Discharge Summary]
    MinimalSummary --> ValidateForm
    
    DeathFlow --> DeathDetails[Enter Death Details]
    DeathDetails --> DeathTime[Record Death Time]
    DeathTime --> DeathCause[Enter Cause of Death]
    DeathCause --> DeathSummary[Write Death Summary]
    DeathSummary --> ValidateForm
    
    ValidateForm{Form Valid?}
    
    ValidateForm -->|No| ShowErrors[Show Validation Errors]
    ShowErrors --> DischargeForm
    
    ValidateForm -->|Yes| SaveSummary[Save Discharge Summary]
    SaveSummary --> GeneratePDF[Generate Summary PDF]
    GeneratePDF --> UpdateStatus[Status: Discharge Pending]
    UpdateStatus --> NotifyBilling[Notify Billing Team]
    
    NotifyBilling --> BillingProcess[Reception Billing Process]
    BillingProcess --> CalculateCharges[Calculate All Charges]
    CalculateCharges --> GenerateFinalBill[Generate Final Bill]
    GenerateFinalBill --> CheckDeposit{Deposit Sufficient?}
    
    CheckDeposit -->|Yes| AdjustDeposit[Adjust Deposit]
    CheckDeposit -->|No| CollectBalance[Collect Balance Payment]
    
    AdjustDeposit --> RefundCheck{Refund Due?}
    RefundCheck -->|Yes| ProcessRefund[Process Refund]
    RefundCheck -->|No| BillingCleared
    
    CollectBalance --> BillingCleared[Mark Billing Cleared]
    ProcessRefund --> BillingCleared
    
    BillingCleared --> CompleteDischarge[Complete Discharge]
    CompleteDischarge --> ReleaseBed[Release Bed]
    ReleaseBed --> UpdateAdmStatus[Status: Discharged]
    UpdateAdmStatus --> RecordDischargeTime[Record Actual Discharge Time]
    RecordDischargeTime --> PrintDocuments[Print Discharge Documents]
    PrintDocuments --> HandoverDocs[Handover Documents to Patient]
    HandoverDocs --> End([Discharge Complete])
```

---

### 3.7 Billing Integration Flow

```mermaid
flowchart TD
    Start([Patient Admitted]) --> AdmissionFee[Add Admission Fee]
    AdmissionFee --> DailyCharges[Daily Charge Process]
    
    DailyCharges --> MidnightCheck{Midnight Trigger}
    MidnightCheck --> AddRoomRent[Add Room Rent Charge]
    AddRoomRent --> AddNursingCharge[Add Nursing Charge]
    AddNursingCharge --> UpdateTotal[Update Total Charges]
    UpdateTotal --> DailyCharges
    
    DailyCharges --> ServiceOrder{Service Ordered?}
    
    ServiceOrder -->|Consultation| AddConsultation[Add Consultation Charge]
    ServiceOrder -->|Investigation| AddInvestigation[Add Investigation Charge]
    ServiceOrder -->|Procedure| AddProcedure[Add Procedure Charge]
    ServiceOrder -->|Medication| AddMedication[Add Medication Charge]
    ServiceOrder -->|Other| AddOther[Add Other Service Charge]
    
    AddConsultation --> UpdateTotal2[Update Total]
    AddInvestigation --> UpdateTotal2
    AddProcedure --> UpdateTotal2
    AddMedication --> UpdateTotal2
    AddOther --> UpdateTotal2
    
    UpdateTotal2 --> RecalcBalance[Recalculate Balance]
    RecalcBalance --> CheckBalance{Balance Negative?}
    
    CheckBalance -->|Yes| AlertBilling[Alert Billing Team]
    AlertBilling --> RequestDeposit[Request Additional Deposit]
    RequestDeposit --> DepositCollected{Deposit Collected?}
    
    DepositCollected -->|Yes| AddDeposit[Add Deposit]
    AddDeposit --> UpdateBalance[Update Balance]
    UpdateBalance --> Continue[Continue]
    
    DepositCollected -->|No| FlagAccount[Flag Account]
    FlagAccount --> Continue
    
    CheckBalance -->|No| Continue
    
    Continue --> DischargeInit{Discharge Initiated?}
    
    DischargeInit -->|No| DailyCharges
    DischargeInit -->|Yes| FinalBilling[Generate Final Bill]
    
    FinalBilling --> End([Billing Complete])
```

---

### 3.8 Ward Dashboard Update Flow

```mermaid
flowchart TD
    Start([Event Triggered]) --> EventType{Event Type}
    
    EventType -->|New Admission| NewAdm[New Admission Event]
    EventType -->|Transfer| Transfer[Transfer Event]
    EventType -->|Discharge| Discharge[Discharge Event]
    EventType -->|Status Change| StatusChange[Status Change Event]
    
    NewAdm --> GetAdmData[Get Admission Data]
    GetAdmData --> GetBedInfo[Get Bed/Ward Info]
    GetBedInfo --> BroadcastNew[Broadcast to Ward Channel]
    BroadcastNew --> UpdateDashboard1[Update Ward Dashboard]
    UpdateDashboard1 --> UpdateBedBoard1[Update Bed Status Board]
    UpdateBedBoard1 --> End1([Dashboard Updated])
    
    Transfer --> GetTransferData[Get Transfer Data]
    GetTransferData --> GetOldWard[Get Old Ward Info]
    GetOldWard --> GetNewWard[Get New Ward Info]
    GetNewWard --> BroadcastOld[Broadcast to Old Ward]
    BroadcastOld --> BroadcastNew2[Broadcast to New Ward]
    BroadcastNew2 --> UpdateDashboard2[Update Both Dashboards]
    UpdateDashboard2 --> UpdateBedBoard2[Update Bed Status]
    UpdateBedBoard2 --> End2([Dashboards Updated])
    
    Discharge --> GetDischargeData[Get Discharge Data]
    GetDischargeData --> GetWardInfo[Get Ward Info]
    GetWardInfo --> BroadcastDischarge[Broadcast to Ward Channel]
    BroadcastDischarge --> RemoveFromDashboard[Remove from Dashboard]
    RemoveFromDashboard --> UpdateBedBoard3[Release Bed]
    UpdateBedBoard3 --> End3([Dashboard Updated])
    
    StatusChange --> GetStatusData[Get Status Data]
    GetStatusData --> BroadcastStatus[Broadcast Status Change]
    BroadcastStatus --> UpdateDashboard4[Update Dashboard]
    UpdateDashboard4 --> End4([Dashboard Updated])
```

---

## 4. Decision Trees

### 4.1 Bed Selection Decision Tree

```mermaid
flowchart TD
    Start([Select Bed for Patient]) --> CheckPref[Check Ward Preference]
    CheckPref --> PrefAvail{Preferred Ward Has Beds?}
    
    PrefAvail -->|Yes| CheckGender[Check Gender Restriction]
    PrefAvail -->|No| CheckSimilar[Check Similar Ward Type]
    
    CheckGender --> GenderMatch{Gender Matches?}
    
    GenderMatch -->|Yes| CheckRoom[Check Room Type]
    GenderMatch -->|No| CheckMixed[Check Mixed Gender Wards]
    
    CheckMixed --> MixedAvail{Mixed Ward Available?}
    MixedAvail -->|Yes| CheckRoom
    MixedAvail -->|No| CheckOther[Check Other Wards]
    
    CheckRoom --> RoomPref{Room Type Preference?}
    
    RoomPref -->|Single| FindSingle[Find Single Room]
    RoomPref -->|Double| FindDouble[Find Double Room]
    RoomPref -->|Any| FindAny[Find Any Available]
    
    FindSingle --> SingleAvail{Single Available?}
    SingleAvail -->|Yes| SelectBed[Select Bed]
    SingleAvail -->|No| OfferDouble[Offer Double/Triple]
    
    FindDouble --> DoubleAvail{Double Available?}
    DoubleAvail -->|Yes| SelectBed
    DoubleAvail -->|No| OfferOther[Offer Other Options]
    
    FindAny --> SelectBed
    OfferDouble --> SelectBed
    OfferOther --> SelectBed
    
    CheckSimilar --> SimilarAvail{Similar Ward Available?}
    SimilarAvail -->|Yes| CheckGender
    SimilarAvail -->|No| CheckOther
    
    CheckOther --> OtherAvail{Any Bed Available?}
    OtherAvail -->|Yes| ShowAll[Show All Available Beds]
    OtherAvail -->|No| NoBedsAvail[No Beds Available]
    
    ShowAll --> SelectBed
    SelectBed --> End1([Bed Selected])
    NoBedsAvail --> WaitingList[Add to Waiting List]
    WaitingList --> End2([Waiting for Bed])
```

---

### 4.2 Discharge Type Decision Tree

```mermaid
flowchart TD
    Start([Determine Discharge Type]) --> PatientStatus{Patient Status}
    
    PatientStatus -->|Recovered| CheckTreatment[Check Treatment Completion]
    PatientStatus -->|Deceased| DeathDischarge[Death Discharge]
    PatientStatus -->|Left Hospital| CheckConsent[Check if Against Advice]
    
    CheckTreatment --> TreatmentComplete{Treatment Complete?}
    
    TreatmentComplete -->|Yes| RegularDischarge[Regular Discharge]
    TreatmentComplete -->|No| CheckReason[Check Reason for Discharge]
    
    CheckReason --> ReasonType{Reason}
    
    ReasonType -->|Patient Request| PatientWants[Patient Wants to Leave]
    ReasonType -->|Transfer| TransferDischarge[Transfer to Another Facility]
    ReasonType -->|Financial| FinancialIssue[Cannot Afford Treatment]
    
    PatientWants --> LAMADischarge[LAMA Discharge]
    FinancialIssue --> LAMADischarge
    
    CheckConsent --> ConsentGiven{Medical Advice Followed?}
    
    ConsentGiven -->|No| LAMADischarge
    ConsentGiven -->|Yes| RegularDischarge
    
    TransferDischarge --> PrepareTransfer[Prepare Transfer Documents]
    PrepareTransfer --> End1([Transfer Discharge])
    
    RegularDischarge --> End2([Regular Discharge])
    LAMADischarge --> End3([LAMA Discharge])
    DeathDischarge --> End4([Death Discharge])
```

---

## 5. State Diagrams

### 5.1 Admission Request States

```mermaid
stateDiagram-v2
    [*] --> Pending: Request Created
    Pending --> Approved: Clerk Approves
    Pending --> Rejected: Clerk Rejects
    Pending --> InfoRequested: Clerk Requests Info
    InfoRequested --> Pending: Doctor Provides Info
    Approved --> Completed: Patient Admitted
    Rejected --> [*]
    Completed --> [*]
```

---

### 5.2 Admission States

```mermaid
stateDiagram-v2
    [*] --> Admitted: Bed Allocated
    Admitted --> Admitted: Patient Care
    Admitted --> DischargePending: Discharge Initiated
    DischargePending --> Discharged: Billing Cleared
    DischargePending --> Admitted: Discharge Cancelled
    Discharged --> [*]
    Admitted --> Absconded: Patient Left
    Admitted --> Death: Patient Deceased
    Absconded --> [*]
    Death --> [*]
```

---

### 5.3 Bed States

```mermaid
stateDiagram-v2
    [*] --> Available: Bed Created
    Available --> Reserved: Bed Reserved
    Available --> Occupied: Patient Admitted
    Reserved --> Occupied: Patient Admitted
    Reserved --> Available: Reservation Expired
    Occupied --> Available: Patient Discharged
    Available --> UnderMaintenance: Maintenance Required
    UnderMaintenance --> Available: Maintenance Complete
    Available --> Inactive: Bed Deactivated
    Inactive --> [*]
```

---

## 6. Sequence Diagrams

### 6.1 Bed Allocation Sequence

```mermaid
sequenceDiagram
    participant Clerk as Admission Clerk
    participant System as MedFlow System
    participant DB as Database
    participant WS as WebSocket
    participant Nurse as Ward Nurse
    
    Clerk->>System: Select Admission Request
    System->>DB: Fetch Admission Request
    DB-->>System: Request Data
    System-->>Clerk: Display Request Details
    
    Clerk->>System: View Available Beds
    System->>DB: Query Available Beds (Ward Type Filter)
    DB-->>System: Available Beds List
    System-->>Clerk: Display Available Beds
    
    Clerk->>System: Select Bed
    System->>DB: Lock Bed (Transaction Start)
    DB-->>System: Bed Locked
    
    Clerk->>System: Confirm Allocation
    System->>DB: Create Admission Record
    System->>DB: Update Bed Status (Occupied)
    System->>DB: Update Request Status (Completed)
    DB-->>System: Success
    System->>DB: Commit Transaction
    
    System->>WS: Emit 'admission:created' Event
    WS-->>Nurse: Notify New Admission
    
    System-->>Clerk: Admission Successful
```

---

## 7. Activity Diagrams

### 7.1 Daily Room Rent Calculation

```mermaid
flowchart TD
    Start([Midnight Trigger]) --> GetAdmissions[Get All Active Admissions]
    GetAdmissions --> Loop{For Each Admission}
    
    Loop --> GetBed[Get Bed Details]
    GetBed --> GetRoom[Get Room Details]
    GetRoom --> GetRate[Get Room Rate Per Day]
    
    GetRate --> CheckTransfer{Transfer Today?}
    
    CheckTransfer -->|Yes| ProRata[Calculate Pro-rata]
    CheckTransfer -->|No| FullDay[Full Day Rate]
    
    ProRata --> CreateCharge1[Create Room Rent Charge]
    FullDay --> CreateCharge1
    
    CreateCharge1 --> AddToAdmission[Add Charge to Admission]
    AddToAdmission --> UpdateTotal[Update Total Charges]
    UpdateTotal --> RecalcBalance[Recalculate Balance]
    
    RecalcBalance --> CheckBalance{Balance < Threshold?}
    
    CheckBalance -->|Yes| AlertBilling[Alert Billing Team]
    CheckBalance -->|No| Continue
    
    AlertBilling --> Continue[Continue]
    Continue --> Loop
    
    Loop --> End([Process Complete])
```

---

## 8. Use Case Diagram

```mermaid
flowchart TD
    subgraph Actors
        Doctor[Doctor]
        Clerk[Admission Clerk]
        Nurse[Ward Nurse]
        Billing[Billing Staff]
        Admin[Admin]
    end
    
    subgraph "IPD Admission System"
        UC1[Create Admission Request]
        UC2[Approve/Reject Request]
        UC3[Allocate Bed]
        UC4[View Ward Patients]
        UC5[Transfer Patient]
        UC6[Initiate Discharge]
        UC7[Collect Deposit]
        UC8[Generate Final Bill]
        UC9[Configure Wards/Rooms/Beds]
        UC10[View Reports]
    end
    
    Doctor --> UC1
    Doctor --> UC6
    
    Clerk --> UC2
    Clerk --> UC3
    
    Nurse --> UC4
    Nurse --> UC5
    
    Billing --> UC7
    Billing --> UC8
    
    Admin --> UC9
    Admin --> UC10
```

---

> **Note:** These diagrams are created using Mermaid syntax and will render in Markdown viewers that support Mermaid (GitHub, GitLab, VS Code with extensions, etc.).
