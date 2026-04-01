```mermaid
flowchart TD
    Start([Start]) --> A[Initialize ESCOneEpReopenFlag = false]

    A --> B{EventTypeCode == 230<br/>OR 323<br/>OR 324?}
    B -- No --> End([End])
    B -- Yes --> C[Loop scoredStandardsDetails<br/>If ReopenFlag == 'Y'<br/>ESCOneEpReopenFlag = true]

    C --> D{ResubmissionFlag == 'Y'?}

    %% =========================
    %% RESUBMISSION = YES
    %% =========================
    D -- Yes --> E{EventSubmissionCount > 1?}

    %% ----- Submission Count > 1 -----
    E -- Yes --> F{ResponsibleGroup contains 'SIG'?}

    %% SIG path (>1)
    F -- Yes --> G{ReviewStatus == 'O'<br/>AND IRRoleTypeId > 0?}
    G -- Yes --> MSG1[/"Please wait for SIG/ENG to complete the IR."/]
    G -- No --> H{ReviewStatus == 'O'<br/>AND IRRoleTypeId == 0?}
    H -- Yes --> MSG2[/"Please cancel the IR to reopen the ESC."/]
    H -- No --> Continue1[Continue]

    %% AE path (>1)
    F -- No --> I{ResponsibleGroup contains 'AE'?}
    I -- Yes --> J{ReviewStatus == 'O'<br/>AND IRRoleTypeId > 0<br/>AND !AllEpReviewedFlag}
    J -- Yes --> MSG3[/"All EPs must be reviewed before reopening."/]
    J -- No --> K{ReviewStatus == 'O'<br/>AND IRRoleTypeId > 0<br/>AND !ESCOneEpReopenFlag}
    K -- Yes --> MSG4[/"At least one EP must be marked for reopen."/]
    K -- No --> Continue1

    %% DEFAULT (>1)
    I -- No --> L{!AllEpReviewedFlag}
    L -- Yes --> MSG5[/"All EPs must be reviewed before reopening."/]
    L -- No --> M{!ESCOneEpReopenFlag}
    M -- Yes --> MSG6[/"At least one EP must be marked for reopen."/]
    M -- No --> Continue1

    %% ----- Submission Count == 1 -----
    E -- No --> E1{EventSubmissionCount == 1?}

    E1 -- Yes --> F1{ResponsibleGroup contains 'SIG'?}

    %% SIG path (=1)
    F1 -- Yes --> G1{ReviewStatus == 'O'<br/>AND IRRoleTypeId > 0}
    G1 -- Yes --> Continue1
    G1 -- No --> H1{ReviewStatus == 'O'<br/>AND IRRoleTypeId == 0<br/>AND !AllEpReviewedFlag}
    H1 -- Yes --> MSG7[/"All EPs must be reviewed before reopening."/]
    H1 -- No --> Continue1

    %% AE path (=1)
    F1 -- No --> I1{ResponsibleGroup contains 'AE'?}
    I1 -- Yes --> J1{ReviewStatus == 'O'<br/>AND IRRoleTypeId > 0<br/>AND !AllEpReviewedFlag}
    J1 -- Yes --> MSG8[/"All EPs must be reviewed before reopening."/]
    J1 -- No --> K1{ReviewStatus == 'O'<br/>AND IRRoleTypeId > 0<br/>AND !ESCOneEpReopenFlag}
    K1 -- Yes --> MSG9[/"At least one EP must be marked for reopen."/]
    K1 -- No --> Continue1

    %% DEFAULT (=1)
    I1 -- No --> L1{!AllEpReviewedFlag}
    L1 -- Yes --> MSG10[/"All EPs must be reviewed before reopening."/]
    L1 -- No --> M1{!ESCOneEpReopenFlag}
    M1 -- Yes --> MSG11[/"At least one EP must be marked for reopen."/]
    M1 -- No --> Continue1

    %% =========================
    %% RESUBMISSION = NO
    %% =========================
    D -- No --> N{EventSubmissionCount == 1?}

    %% Submission == 1 (No Resubmission)
    N -- Yes --> O{ResponsibleGroup contains 'SIG'?}
    O -- Yes --> P{ReviewStatus == 'O'<br/>AND IRRoleTypeId == 0<br/>AND !AllEpReviewedFlag}
    P -- Yes --> MSG12[/"All EPs must be reviewed before reopening."/]
    P -- No --> Continue1

    O -- No --> Q{ResponsibleGroup == 'AE'?}
    Q -- Yes --> R{ReviewStatus == 'O'<br/>AND IRRoleTypeId > 0<br/>AND !AllEpReviewedFlag}
    R -- Yes --> MSG13[/"All EPs must be reviewed before reopening."/]
    R -- No --> S{ReviewStatus == 'O'<br/>AND IRRoleTypeId > 0<br/>AND !ESCOneEpReopenFlag}
    S -- Yes --> MSG14[/"At least one EP must be marked for reopen."/]
    S -- No --> Continue1

    %% Submission > 1 (No Resubmission)
    N -- No --> T{EventSubmissionCount > 1?}
    T -- Yes --> U{ResponsibleGroup contains 'SIG'?}

    U -- Yes --> V{ReviewStatus == 'O'<br/>AND IRRoleTypeId == 0<br/>AND !AllEpReviewedFlag}
    V -- Yes --> MSG15[/"Please contact your manager/lead to enable resubmission."/]
    V -- No --> Continue1

    U -- No --> W{ResponsibleGroup == 'AE'?}
    W -- Yes --> X{ReviewStatus == 'O'<br/>AND IRRoleTypeId > 0<br/>AND AllEpReviewedFlag<br/>AND !ESCOneEpReopenFlag}
    X -- Yes --> MSG16[/"Please contact your manager/lead to enable resubmission."/]
    X -- No --> Continue1

    %% =========================
    %% FINAL
    %% =========================
    Continue1 --> End
