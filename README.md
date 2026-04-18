<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>SMILE Project | TaRL Complete M&E System – Full Cloud Sync</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,400;500;600;700;800&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Firebase SDKs -->
    <script src="https://www.gstatic.com/firebasejs/10.8.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.8.0/firebase-firestore-compat.js"></script>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { background: #eef5f0; font-family: 'Inter', sans-serif; color: #1a3a2e; padding: 16px; }
        .app-container { max-width: 1600px; margin: 0 auto; }
        .main-header { background: linear-gradient(115deg, #0b3326 0%, #1b5e45 100%); border-radius: 20px; padding: 18px 22px; margin-bottom: 20px; color: white; }
        .brand h1 { font-size: 0.95rem; font-weight: 700; display: flex; align-items: center; gap: 8px; flex-wrap: wrap; }
        .brand i { background: #ffcd6b; color: #1a4d34; border-radius: 50px; padding: 8px; }
        .sub { color: #c7f0e2; margin-top: 4px; font-size: 0.55rem; }
        .nav-pills { display: flex; flex-wrap: wrap; gap: 4px; margin-top: 12px; border-bottom: 1px solid rgba(255,255,255,0.2); padding-bottom: 6px; }
        .nav-btn { background: transparent; border: none; font-weight: 600; padding: 4px 10px; border-radius: 40px; font-size: 0.65rem; cursor: pointer; color: #f0faf5; transition: 0.2s; }
        .nav-btn i { margin-right: 4px; }
        .nav-btn.active { background: #ffcd6b; color: #1b422f; }
        .panel { display: none; animation: fadeIn 0.25s ease; }
        .panel.active-panel { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(6px);} to { opacity: 1; transform: translateY(0);} }
        .card { background: white; border-radius: 20px; padding: 20px; margin-bottom: 20px; box-shadow: 0 4px 12px rgba(0,0,0,0.04); overflow-x: auto; }
        .form-row { display: grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); gap: 12px; margin-bottom: 16px; align-items: flex-end; }
        .input-group { display: flex; flex-direction: column; gap: 4px; }
        .input-group label { font-weight: 700; font-size: 0.6rem; text-transform: uppercase; color: #548b74; }
        input, select, textarea { padding: 8px 10px; border-radius: 12px; border: 1.5px solid #e0ede5; font-size: 0.7rem; background: white; font-family: 'Inter', sans-serif; }
        textarea { resize: vertical; }
        button { background: #1e5e48; border: none; padding: 8px 16px; border-radius: 30px; font-weight: 600; color: white; cursor: pointer; font-size: 0.7rem; transition: 0.2s; }
        button i { margin-right: 5px; }
        button.btn-outline { background: white; border: 1.5px solid #caddd4; color: #1e5e48; }
        button.btn-small { padding: 4px 10px; font-size: 0.6rem; }
        button:hover { background: #0f4635; transform: translateY(-1px); }
        .stats-grid { display: flex; flex-wrap: wrap; gap: 12px; margin-bottom: 20px; }
        .stat-card { background: white; flex: 1; min-width: 130px; border-radius: 16px; padding: 12px; border-left: 4px solid #ffcd6b; }
        .stat-number { font-size: 1.4rem; font-weight: 800; color: #1e5e48; }
        .badge { display: inline-block; padding: 3px 10px; border-radius: 20px; font-size: 0.6rem; font-weight: 600; }
        .badge-success { background: #d4f0e3; color: #1a6e4a; }
        .badge-warning { background: #fff1db; color: #b55f00; }
        .badge-info { background: #eafaf2; color: #1e5e48; }
        .badge-danger { background: #f8d7da; color: #721c24; }
        .training-badge { background: #ffcd6b; color: #1b422f; padding: 2px 6px; border-radius: 12px; font-size: 0.55rem; margin-left: 6px; }
        .level-section { background: #f9fefb; border-radius: 12px; padding: 10px; margin-bottom: 10px; border: 1px solid #e0ede5; }
        .phase-selector { display: flex; gap: 10px; margin-bottom: 16px; background: #f0f7f3; padding: 10px; border-radius: 16px; flex-wrap: wrap; }
        .phase-btn { background: #caddd4; color: #1a3a2e; padding: 4px 16px; border-radius: 30px; cursor: pointer; font-size: 0.65rem; font-weight: bold; border: none; }
        .phase-btn.active-phase { background: #1e5e48; color: white; }
        .binary-group { display: flex; gap: 8px; flex-wrap: wrap; margin: 8px 0; }
        .binary-option { display: flex; align-items: center; gap: 4px; background: #f9fefb; padding: 3px 8px; border-radius: 20px; font-size: 0.65rem; cursor: pointer; }
        .summary-tabs { display: flex; gap: 8px; margin-bottom: 20px; flex-wrap: wrap; border-bottom: 1px solid #e0ede5; padding-bottom: 10px; }
        .summary-tab { background: #e8f3ef; padding: 6px 14px; border-radius: 30px; font-size: 0.7rem; font-weight: 600; cursor: pointer; color: #1e5e48; }
        .summary-tab.active-tab { background: #1e5e48; color: white; }
        .summary-content { display: none; }
        .summary-content.active-summary { display: block; }
        .level-breakdown-table { width: 100%; border-collapse: collapse; font-size: 0.7rem; }
        .level-breakdown-table th, .level-breakdown-table td { border: 1px solid #e0ede5; padding: 6px 8px; text-align: center; }
        .action-btn { background: #ffcd6b; color: #1b422f; padding: 3px 8px; border-radius: 15px; font-size: 0.6rem; margin: 0 2px; cursor: pointer; border: none; }
        .delete-btn { background: #c23b22; color: white; }
        .edit-icon { cursor: pointer; margin-left: 8px; font-size: 0.7rem; color: #ffcd6b; }
        .editable-field { cursor: pointer; border-bottom: 1px dashed #ffcd6b; }
        .approve-btn { background: #28a745; }
        .reject-btn { background: #dc3545; }
        .edit-modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 1000; justify-content: center; align-items: center; }
        .edit-modal-content { background: white; border-radius: 20px; padding: 20px; max-width: 500px; width: 90%; max-height: 80vh; overflow-y: auto; }
        .admin-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); gap: 16px; margin-top: 16px; }
        .save-indicator { position: fixed; bottom: 20px; right: 20px; background: #1e5e48; color: white; padding: 8px 16px; border-radius: 30px; font-size: 0.7rem; z-index: 1000; display: none; }
        .sync-status { position: fixed; bottom: 20px; left: 20px; background: #1e5e48; color: white; padding: 6px 12px; border-radius: 30px; font-size: 0.65rem; z-index: 1000; pointer-events: none; }
        @media (max-width: 768px) { .form-row { grid-template-columns: 1fr; } }
    </style>
</head>
<body>
<div class="app-container">
    <div class="main-header">
        <div class="brand"><h1><i class="fas fa-smile-wink"></i> <span id="projectNameHeader">SMILE Project - TaRL Oromia</span> <i class="fas fa-edit edit-icon" onclick="editProjectNameHeader()"></i></h1>
        <div class="sub" id="projectTaglineHeader">Strengthening Minds through Innovative Learning & Equity</div></div>
        <div class="nav-pills">
            <button class="nav-btn active" data-panel="planPanel"><i class="fas fa-calendar-alt"></i> Activities Plan</button>
            <button class="nav-btn" data-panel="woredaPanel"><i class="fas fa-map-marker-alt"></i> Woreda Summary</button>
            <button class="nav-btn" data-panel="zonalPanel"><i class="fas fa-globe"></i> Zonal Summary</button>
            <button class="nav-btn" data-panel="narrativePanel"><i class="fas fa-pen-fancy"></i> Reports & Media</button>
            <button class="nav-btn" data-panel="progressPanel"><i class="fas fa-chart-line"></i> Dashboard</button>
            <button class="nav-btn" data-panel="studentPanel"><i class="fas fa-child"></i> Student Registration</button>
            <button class="nav-btn" data-panel="cwdPanel"><i class="fas fa-wheelchair"></i> CWD Services</button>
            <button class="nav-btn" data-panel="projectPanel"><i class="fas fa-building"></i> Project Profile</button>
            <button class="nav-btn" data-panel="staffPanel"><i class="fas fa-users"></i> Staff & Data Collectors</button>
            <button class="nav-btn" data-panel="templatesPanel"><i class="fas fa-file-alt"></i> Planning Templates</button>
            <button class="nav-btn" data-panel="adminPanel"><i class="fas fa-user-shield"></i> Admin</button>
        </div>
    </div>

    <!-- ACTIVITIES PLAN PANEL -->
    <div id="planPanel" class="panel active-panel"><div class="card"><h2><i class="fas fa-plus-circle"></i> Activities Plan <button id="addNewActivityBtn" class="btn-outline btn-small" style="margin-left:10px;"><i class="fas fa-plus"></i> New Activity</button></h2><div style="overflow-x:auto;"><table id="activitiesTable"><thead><tr><th>Code</th><th>Activity</th><th>Type</th><th>Unit</th><th>Target</th><th>Achieved</th><th>Progress</th><th>Status</th><th>Update</th><th>Action</th></tr></thead><tbody id="activitiesBody"></tbody></table></div><button id="resetProgressBtn" class="btn-outline">Reset All Progress</button></div></div>
    <div id="woredaPanel" class="panel"><div class="card"><h2>Woreda Summary</h2><div class="form-row"><div class="input-group"><label>Select Woreda</label><select id="woredaSelect"></select></div><div class="input-group"><label>Activity Code</label><select id="woredaActivitySelect"></select></div><div><button id="addWoredaDataBtn">Add/Update</button></div></div><div id="genderFieldsContainer" class="form-row"></div><table id="woredaTable"><thead><tr><th>Woreda</th><th>Activity</th><th>Type</th><th>Girls</th><th>Boys</th><th>Total</th><th>Date</th><th>Action</th></tr></thead><tbody id="woredaBody"></tbody></table></div></div>
    <div id="zonalPanel" class="panel"><div class="card"><h2>Zonal Summary</h2><div class="form-row"><div class="input-group"><label>Select Zone</label><select id="zoneSelect"><option value="East Hararghe">East Hararghe</option><option value="Borana">Borana</option></select></div><div><button id="refreshZonalBtn">Refresh</button></div></div><div id="zonalSummaryContainer"></div><canvas id="zonalChart" height="200"></canvas></div></div>
    <div id="narrativePanel" class="panel"><div class="card"><h2>Narrative Reports</h2><div class="form-row"><div class="input-group"><label>Activity</label><select id="narrativeActivitySelect"></select></div><div class="input-group"><label>Date</label><input type="date" id="narrativeDate"></div><div class="input-group"><label>Woreda</label><input type="text" id="narrativeWoreda" list="woredaList"></div></div><div class="input-group"><label>Report</label><textarea id="narrativeText" rows="4"></textarea></div><button id="saveNarrativeBtn">Save</button><button id="clearNarrativeBtn" class="btn-outline">Clear</button></div><div class="card"><h2>Reports List</h2><div id="reportsList"></div></div></div>
    <div id="progressPanel" class="panel"><div class="card"><h2>Dashboard</h2><div class="stats-grid" id="progressStats"></div><canvas id="overallProgressChart" height="150"></canvas><canvas id="topActivitiesChart" height="200"></canvas></div></div>

    <!-- STUDENT REGISTRATION PANEL with FULL ANALYTICS -->
    <div id="studentPanel" class="panel">
        <div class="card"><div class="phase-selector"><button class="phase-btn active-phase" data-phase="baseline">Baseline</button><button class="phase-btn" data-phase="midline">Midline</button><button class="phase-btn" data-phase="endline">Endline</button></div>
            <h2>Student Registration & Assessment</h2><div class="form-row"><div class="input-group"><label>Zone</label><input type="text" id="regZone"></div><div class="input-group"><label>Woreda</label><input type="text" id="regWoreda"></div><div class="input-group"><label>School</label><input type="text" id="regSchoolName"></div><div class="input-group"><label>Name</label><input type="text" id="regFullName"></div><div class="input-group"><label>Grade</label><select id="regGrade"><option>3</option><option>4</option><option>5</option><option>6</option></select></div><div class="input-group"><label>Gender</label><select id="regGender"><option value="Dhi">Boy</option><option value="Dub">Girl</option></select></div></div>
            <div class="level-section"><div class="level-title">Numeracy Levels 0-4</div><div class="binary-group" id="mathGroup"></div></div>
            <div class="level-section"><div class="level-title">Afaan Oromoo (0=Jalqabdoota,4=Seeneessaa)</div><div class="binary-group" id="oromoGroup"></div></div>
            <div class="level-section"><div class="level-title">English (0=Beginner,4=Story)</div><div class="binary-group" id="engGroup"></div></div>
            <button id="addStudentBtn">Register</button><button id="clearStudentBtn" class="btn-outline">Clear</button></div>
        <div class="card"><h2>Student Analytics: By Grade & Level (Separate Counts for All Subjects)</h2>
            <div class="summary-tabs"><div class="summary-tab active-tab" data-summary="gradeLevels">📊 By Grade & Level</div><div class="summary-tab" data-summary="sexLevels">⚧ By Sex</div><div class="summary-tab" data-summary="woredaSummary">🏘️ By Woreda</div><div class="summary-tab" data-summary="schoolSummary">🏫 By School</div><div class="summary-tab" data-summary="zoneSummary">🌍 By Zone</div><div class="summary-tab" data-summary="progressTracking">📈 Progress (B→M→E)</div></div>
            <div id="summary-gradeLevels" class="summary-content active-summary"><div id="gradeLevelsContainer"></div></div>
            <div id="summary-sexLevels" class="summary-content"><div id="sexLevelsContainer"></div></div>
            <div id="summary-woredaSummary" class="summary-content"><div id="woredaSummaryContainer"></div></div>
            <div id="summary-schoolSummary" class="summary-content"><div id="schoolSummaryContainer"></div></div>
            <div id="summary-zoneSummary" class="summary-content"><div id="zoneSummaryContainer"></div></div>
            <div id="summary-progressTracking" class="summary-content"><div id="progressTrackingContainer"></div></div>
        </div>
        <div class="card"><h2>Student List</h2><table id="studentTable"><thead><tr><th>Name</th><th>School</th><th>Grade</th><th>Gender</th><th>Baseline Oromo</th><th>Midline</th><th>Endline</th><th>Actions</th></tr></thead><tbody id="studentBody"></tbody></td><button id="exportStudentBtn" class="btn-outline">Export CSV</button></div>
    </div>

    <div id="cwdPanel" class="panel"><div class="card"><h2>CWD Registration</h2><div class="form-row"><div class="input-group"><label>Name</label><input type="text" id="cwdName"></div><div class="input-group"><label>Age</label><input type="number" id="cwdAge"></div><div class="input-group"><label>Sex</label><select id="cwdSex"><option>Male</option><option>Female</option></select></div><div class="input-group"><label>Zone</label><input type="text" id="cwdZone"></div><div class="input-group"><label>Woreda</label><input type="text" id="cwdWoreda"></div><div class="input-group"><label>School</label><input type="text" id="cwdSchool"></div><div class="input-group"><label>Disability</label><select id="cwdDisability"><option>Hearing</option><option>Visual</option><option>Physical</option></select></div><div class="input-group"><label>Service Stage</label><select id="cwdServiceStage"><option>Mapped</option><option>Diagnosed</option><option>Devices Provision</option><option>Medical Services</option></select></div><div class="input-group"><label>Device</label><select id="cwdDevice"><option>None</option><option>Eye Glasses</option><option>Hearing Aid</option></select></div><div class="input-group"><label>Caregiver</label><input type="text" id="cwdCaregiver"></div><div class="input-group"><label>Phone</label><input type="text" id="cwdPhone"></div></div><button id="addCwdBtn">Register</button><button id="clearCwdBtn" class="btn-outline">Clear</button></div><div class="card"><div id="cwdSummaryStats" class="stats-grid"></div><div id="cwdTablesContainer"></div><button id="exportCwdBtn" class="btn-outline">Export CWD CSV</button></div></div>

    <!-- PROJECT PROFILE PANEL -->
    <div id="projectPanel" class="panel"><div class="card"><h2><i class="fas fa-smile-wink"></i> SMILE Project Profile <i class="fas fa-edit edit-icon" onclick="editProjectProfile()"></i> <button id="saveProjectProfileBtn" class="btn-small btn-outline"><i class="fas fa-save"></i> Save</button></h2>
        <div class="admin-grid"><div class="admin-card"><strong>Project Name:</strong> <span id="projName" class="editable-field" onclick="editField('projName')">SMILE (Strengthening Minds through Innovative Learning & Equity)</span><br><strong>Code:</strong> <span id="projCode" class="editable-field" onclick="editField('projCode')">SMILE-TaRL-OR-2026</span><br><strong>Donor:</strong> <span id="projDonor" class="editable-field" onclick="editField('projDonor')">USAID/REA</span></div>
        <div class="admin-card"><strong>Start:</strong> <span id="projStart" class="editable-field" onclick="editField('projStart')">Jan 2024</span><br><strong>End:</strong> <span id="projEnd" class="editable-field" onclick="editField('projEnd')">Dec 2027</span><br><strong>Phase:</strong> <span id="projPhase" class="editable-field" onclick="editField('projPhase')">Year 2 (2026)</span></div>
        <div class="admin-card"><strong>Regions:</strong> <span id="projRegions" class="editable-field" onclick="editField('projRegions')">East Hararghe, Borana</span><br><strong>Schools:</strong> <span id="projSchools" class="editable-field" onclick="editField('projSchools')">157</span><br><strong>Beneficiaries:</strong> <span id="projBeneficiaries" class="editable-field" onclick="editField('projBeneficiaries')">864 Teachers, 314 Mentors</span></div></div></div>
        <div class="card"><h2>Results Framework <i class="fas fa-edit edit-icon" onclick="editResultsFramework()"></i> <button id="saveRFBtn" class="btn-small btn-outline">Save Framework</button></h2>
            <table class="level-breakdown-table" id="resultsFrameworkTable"><thead><tr><th>Level</th><th>Description</th><th>Indicators</th><th>Target</th><th>Achieved</th></tr></thead><tbody>
                <tr><td><strong>Goal</strong></td><td id="rf_goal_desc">Improved literacy & numeracy grades 3-6</td><td id="rf_goal_ind">% students at proficiency</td><td id="rf_goal_target">65%</td><td id="rf_goal_achieved">--</td></tr>
                <tr><td><strong>Outcome</strong></td><td id="rf_out_desc">Enhanced teacher capacity & materials</td><td id="rf_out_ind">Teachers trained / SRMs</td><td id="rf_out_target">864 teachers, 31,400 SRMs</td><td id="rf_out_achieved">--</td></tr>
                <tr><td><strong>Output 1</strong></td><td id="rf_out1_desc">Teacher training & mentoring</td><td id="rf_out1_ind"># teachers trained</td><td id="rf_out1_target">864 teachers</td><td id="rf_out1_achieved">--</td></tr>
                <tr><td><strong>Output 2</strong></td><td id="rf_out2_desc">Learning materials distributed</td><td id="rf_out2_ind"># SRMs distributed</td><td id="rf_out2_target">31,400</td><td id="rf_out2_achieved">--</td></tr>
                <tr><td><strong>Output 3</strong></td><td id="rf_out3_desc">CWD inclusion services</td><td id="rf_out3_ind"># CWDs supported</td><td id="rf_out3_target">124</td><td id="rf_out3_achieved">--</td></tr>
            </tbody></table></div>
        <div class="card"><h2>Performance Dashboard</h2><div class="stats-grid" id="projectKpiGrid"></div></div>
    </div>

    <!-- STAFF & DATA COLLECTORS PANEL -->
    <div id="staffPanel" class="panel"><div class="card"><h2>Staff Profiles</h2><div class="form-row"><div class="input-group"><label>Name</label><input type="text" id="staffName"></div><div class="input-group"><label>Role</label><select id="staffRole"><option>Program Manager</option><option>M&E Officer</option><option>Field Coordinator</option></select></div><div class="input-group"><label>Dept</label><select id="staffDept"><option>Management</option><option>M&E</option><option>Field Ops</option></select></div><div class="input-group"><label>Phone</label><input type="text" id="staffPhone"></div><div><button id="addStaffBtn">Add Staff</button></div></div><table id="staffTable"><thead><tr><th>Name</th><th>Role</th><th>Dept</th><th>Phone</th><th>Actions</th></tr></thead><tbody id="staffBody"></tbody></table></div>
        <div class="card"><h2>Data Collectors <span id="adminLoginStatus" class="badge badge-danger">Admin offline</span></h2><div class="form-row"><div class="input-group"><label>Name</label><input type="text" id="dcName"></div><div class="input-group"><label>Zone</label><select id="dcZone"><option>East Hararghe</option><option>Borana</option></select></div><div class="input-group"><label>Woreda</label><input type="text" id="dcWoreda"></div><div class="input-group"><label>Phone</label><input type="text" id="dcPhone"></div><div><button id="addDcBtn">Register (Pending Admin)</button></div></div>
            <h3>Pending Approval</h3><table id="dcPendingTable"><thead><tr><th>Name</th><th>Zone</th><th>Woreda</th><th>Phone</th><th>Status</th><th>Admin Action</th></tr></thead><tbody id="dcPendingBody"></tbody></table>
            <h3>Approved Data Collectors</h3><table id="dcApprovedTable"><thead><tr><th>Name</th><th>Zone</th><th>Woreda</th><th>Phone</th><th>Status</th></tr></thead><tbody id="dcApprovedBody"></tbody></td></div>
        <div class="card"><div class="stats-grid" id="hrStats"></div></div>
    </div>

    <!-- PLANNING TEMPLATES PANEL -->
    <div id="templatesPanel" class="panel">
        <div class="card"><h2><i class="fas fa-file-alt"></i> Planning Templates <button id="addTemplateBtn" class="btn-outline btn-small"><i class="fas fa-plus"></i> New Template</button></h2>
            <div class="form-row">
                <div class="input-group"><label>Template Name</label><input type="text" id="templateName" placeholder="e.g., Q2 Training Plan"></div>
                <div class="input-group"><label>Activity Code</label><select id="templateActivityCode"><option value="">-- Select Activity --</option></select></div>
                <div class="input-group"><label>Target Quantity</label><input type="number" id="templateTarget"></div>
                <div class="input-group"><label>Start Date</label><input type="date" id="templateStart"></div>
                <div class="input-group"><label>End Date</label><input type="date" id="templateEnd"></div>
                <div class="input-group"><label>Responsible Person</label><input type="text" id="templateResponsible"></div>
                <div class="input-group"><label>Status</label><select id="templateStatus"><option>Not Started</option><option>In Progress</option><option>Completed</option><option>Delayed</option></select></div>
                <div class="input-group"><label>Notes / Remarks</label><textarea id="templateNotes" rows="2"></textarea></div>
            </div>
            <button id="saveTemplateBtn">Save Template</button>
            <hr style="margin: 20px 0;">
            <h3>Existing Planning Templates</h3>
            <div class="table-responsive"><table class="level-breakdown-table" id="templatesTable"><thead><tr><th>Name</th><th>Activity</th><th>Target</th><th>Start</th><th>End</th><th>Responsible</th><th>Status</th><th>Notes</th><th>Actions</th></tr></thead><tbody id="templatesBody"></tbody></table></div>
        </div>
    </div>

    <!-- ADMIN PANEL (includes the requested Firebase test) -->
    <div id="adminPanel" class="panel">
        <div class="card"><h2>Admin Authorization</h2><div id="adminLoginSection"><div class="form-row"><div class="input-group"><label>Username</label><input type="text" id="adminUsername" placeholder="mohammedscho2023"></div><div class="input-group"><label>Password</label><input type="password" id="adminPassword" placeholder="Tuba5402!!"></div><div><button id="adminLoginBtn">Login</button></div></div><div id="loginMessage" style="color:#c23b22;"></div></div>
        <div id="adminContentSection" style="display:none;"><div class="badge badge-success">Admin Mode Active</div><div class="form-row" style="margin-top:15px;"><div class="input-group"><label>New Password</label><input type="password" id="newAdminPassword"></div><div><button id="changeAdminPasswordBtn">Change Password</button></div><div><button id="adminLogoutBtn" class="btn-outline">Logout</button></div></div></div></div>
        <div class="card"><h2>Data Management</h2><div class="admin-grid"><div class="admin-card">Students: <span id="adminStudentCount">0</span> | CWDs: <span id="adminCwdCount">0</span> | Staff: <span id="adminStaffCount">0</span></div><div class="admin-card"><button id="exportAllDataBtn" class="btn-outline">Export All</button><button id="clearAllDataBtn" class="btn-outline" style="background:#c23b22;color:white;">Reset All</button></div></div></div>
        
        <!-- NEW: Quick Cloud Test (exact code as requested) -->
        <div class="card"><h3><i class="fas fa-cloud-upload-alt"></i> Quick Cloud Test (your Firestore pattern)</h3>
            <div class="form-row">
                <div class="input-group"><label>School</label><input type="text" id="testSchool" placeholder="School name"></div>
                <div class="input-group"><label>Grade</label><input type="text" id="testGrade" placeholder="Grade (e.g., 3)"></div>
                <div><button id="testSaveBtn">Save Data (syncs to cloud)</button></div>
            </div>
            <h4>Live updates from "students" collection:</h4>
            <div id="testOutput" style="background:#f9fefb; padding:10px; border-radius:12px; max-height:200px; overflow-y:auto;"></div>
            <p class="badge badge-info">This demonstrates real‑time sync using <code>db.collection("students").add()</code> and <code>.onSnapshot()</code> exactly as you provided.</p>
        </div>
    </div>

    <div id="editModal" class="edit-modal"><div class="edit-modal-content"><h3>Edit Field</h3><input type="hidden" id="editFieldId"><input type="text" id="editFieldValue"><button id="saveEditFieldBtn">Save</button><button id="closeEditModalBtn" class="btn-outline">Cancel</button></div></div>
    <div id="addActivityModal" class="edit-modal"><div class="edit-modal-content"><h3>Add Activity</h3><div class="form-row"><div class="input-group"><label>Code</label><input type="text" id="newActivityCode"></div><div class="input-group"><label>Name</label><input type="text" id="newActivityName"></div><div class="input-group"><label>Type</label><select id="newActivityType"><option>training</option><option>non-training</option></select></div><div class="input-group"><label>Unit</label><input type="text" id="newActivityUnit"></div><div class="input-group"><label>Target</label><input type="number" id="newActivityTarget"></div></div><button id="confirmAddActivityBtn">Add</button><button id="closeActivityModalBtn" class="btn-outline">Cancel</button></div></div>
    <div id="saveIndicator" class="save-indicator">Saved!</div>
    <div class="sync-status" id="syncStatus">🔄 Local mode</div>

<script>
    // ==================== FIREBASE CONFIGURATION (Replace with your own) ====================
    const firebaseConfig = {
        apiKey: "YOUR_API_KEY",
        authDomain: "YOUR_PROJECT.firebaseapp.com",
        projectId: "YOUR_PROJECT_ID",
        storageBucket: "YOUR_PROJECT.appspot.com",
        messagingSenderId: "XXXX",
        appId: "XXXX"
    };

    let db = null;
    let isFirebaseReady = false;
    let firestoreListeners = {};

    try {
        if (firebaseConfig.apiKey && firebaseConfig.apiKey !== "YOUR_API_KEY") {
            firebase.initializeApp(firebaseConfig);
            db = firebase.firestore();
            isFirebaseReady = true;
            document.getElementById("syncStatus").innerHTML = "☁️ Cloud sync active";
        } else {
            console.log("Firebase not configured, using local storage only.");
        }
    } catch(e) {
        console.warn("Firebase init error:", e);
    }

    // ==================== YOUR ADDED FUNCTIONS (saveData + listener) ====================
    function saveData() {
        if (!isFirebaseReady) {
            alert("Firebase not configured. Please add your credentials.");
            return;
        }
        const school = document.getElementById("testSchool").value;
        const grade = document.getElementById("testGrade").value;
        if (!school || !grade) {
            alert("Please enter both school and grade.");
            return;
        }
        db.collection("students").add({
            school: school,
            grade: grade,
            timestamp: firebase.firestore.FieldValue.serverTimestamp()
        })
        .then(() => {
            alert("Data saved & synced");
            document.getElementById("testSchool").value = "";
            document.getElementById("testGrade").value = "";
        })
        .catch(err => alert(err.message));
    }

    // Real-time listener for the "students" collection
    if (isFirebaseReady) {
        db.collection("students")
          .orderBy("timestamp", "desc")
          .onSnapshot(snapshot => {
            let html = "";
            snapshot.forEach(doc => {
                const d = doc.data();
                html += `<p><strong>${d.school}</strong> – Grade ${d.grade} <small style="color:#548b74">(${d.timestamp?.toDate?.().toLocaleString() || 'just now'})</small></p>`;
            });
            if (!html) html = "<p>No records yet. Add some using the form above.</p>";
            document.getElementById("testOutput").innerHTML = html;
          });
    }

    // ==================== EXISTING DATA STRUCTURES (unchanged) ====================
    let activities = [
        { code: "4.1.6", name: "Print TaRL teachers guideline", unit: "Manuals", target: 864, achieved: 0, type: "non-training" },
        { code: "4.2.6", name: "Print SRMs", unit: "SRMs", target: 31400, achieved: 0, type: "non-training" },
        { code: "4.4.3", name: "Assistive devices for CWDs", unit: "CWDs", target: 124, achieved: 0, type: "non-training" },
        { code: "4.5.2", name: "Train teachers", unit: "Teachers", target: 864, achieved: 0, type: "training" },
        { code: "4.5.3", name: "Train mentors", unit: "Mentors", target: 314, achieved: 0, type: "training" }
    ];
    let woredaData = [], reports = [], students = [], cwdRecords = [], staffList = [], dataCollectors = [];
    let planningTemplates = [];
    let adminSession = false, currentAdminPassword = "Tuba5402!!", currentPhase = "baseline";
    let projectProfile = { projName: "SMILE (Strengthening Minds through Innovative Learning & Equity)", projCode: "SMILE-TaRL-OR-2026", donor: "USAID/REA", startDate: "Jan 2024", endDate: "Dec 2027", phase: "Year 2 (2026)", regions: "East Hararghe, Borana", schools: "157", beneficiaries: "864 Teachers, 314 Mentors" };
    let resultsFramework = { goal_desc: "Improved literacy & numeracy grades 3-6", goal_ind: "% students at proficiency", goal_target: "65%", out_desc: "Enhanced teacher capacity & materials", out_ind: "Teachers trained / SRMs", out_target: "864 teachers, 31,400 SRMs", out1_desc: "Teacher training & mentoring", out1_ind: "# teachers trained", out1_target: "864 teachers", out2_desc: "Learning materials distributed", out2_ind: "# SRMs distributed", out2_target: "31,400", out3_desc: "CWD inclusion services", out3_ind: "# CWDs supported", out3_target: "124" };
    const woredaList = ["East Hararghe Zone: Babile","East Hararghe Zone: Chiro","East Hararghe Zone: Chinaksan","East Hararghe Zone: Kersa","Borana Zone: Mio","Borana Zone: Yabelo","Borana Tatale"];
    const levelNames = { math: ['L0','L1','L2','L3','L4'], oromo: ['Jalqabdoota','Qubee','Jecha','Keey.Salphaa','Seeneessaa'], eng: ['Beginner','Letter','Word','Paragraph','Story'] };

    // ==================== CLOUD SYNC HELPERS (existing comprehensive sync) ====================
    async function pushToFirestore(collectionName, dataArray) {
        if (!isFirebaseReady) return;
        const colRef = db.collection(collectionName);
        const snapshot = await colRef.get();
        const batch = db.batch();
        snapshot.docs.forEach(doc => batch.delete(doc.ref));
        dataArray.forEach(item => {
            const docRef = colRef.doc(item.id ? item.id.toString() : null);
            if (docRef.id) batch.set(docRef, item);
            else batch.set(colRef.doc(), item);
        });
        await batch.commit();
    }

    function setupFirestoreListener(collectionName, targetArray) {
        if (!isFirebaseReady) return;
        if (firestoreListeners[collectionName]) firestoreListeners[collectionName]();
        firestoreListeners[collectionName] = db.collection(collectionName).onSnapshot(snapshot => {
            const updated = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
            targetArray.length = 0;
            targetArray.push(...updated);
            renderAll();
        });
    }

    function saveAll() {
        localStorage.setItem("smile_activities", JSON.stringify(activities));
        localStorage.setItem("smile_woreda", JSON.stringify(woredaData));
        localStorage.setItem("smile_reports", JSON.stringify(reports));
        localStorage.setItem("smile_students", JSON.stringify(students));
        localStorage.setItem("smile_cwd", JSON.stringify(cwdRecords));
        localStorage.setItem("smile_staff", JSON.stringify(staffList));
        localStorage.setItem("smile_datacollectors", JSON.stringify(dataCollectors));
        localStorage.setItem("smile_project_profile", JSON.stringify(projectProfile));
        localStorage.setItem("smile_results_framework", JSON.stringify(resultsFramework));
        localStorage.setItem("smile_planning_templates", JSON.stringify(planningTemplates));
        if (isFirebaseReady) {
            pushToFirestore("activities", activities);
            pushToFirestore("woredaData", woredaData);
            pushToFirestore("reports", reports);
            pushToFirestore("students", students);
            pushToFirestore("cwdRecords", cwdRecords);
            pushToFirestore("staffList", staffList);
            pushToFirestore("dataCollectors", dataCollectors);
            pushToFirestore("projectProfile", [projectProfile]);
            pushToFirestore("resultsFramework", [resultsFramework]);
            pushToFirestore("planningTemplates", planningTemplates);
        }
        document.getElementById("saveIndicator").style.display = "block";
        setTimeout(()=>document.getElementById("saveIndicator").style.display="none", 1000);
        renderAll();
    }
    window.saveAll = saveAll;

    function loadData() {
        let a = localStorage.getItem("smile_activities"); if(a) activities = JSON.parse(a);
        woredaData = JSON.parse(localStorage.getItem("smile_woreda")||"[]");
        reports = JSON.parse(localStorage.getItem("smile_reports")||"[]");
        students = JSON.parse(localStorage.getItem("smile_students")||"[]");
        cwdRecords = JSON.parse(localStorage.getItem("smile_cwd")||"[]");
        staffList = JSON.parse(localStorage.getItem("smile_staff")||"[]");
        dataCollectors = JSON.parse(localStorage.getItem("smile_datacollectors")||"[]");
        let p = localStorage.getItem("smile_admin_pass"); if(p) currentAdminPassword = p;
        let pp = localStorage.getItem("smile_project_profile"); if(pp) projectProfile = JSON.parse(pp);
        let rf = localStorage.getItem("smile_results_framework"); if(rf) resultsFramework = JSON.parse(rf);
        let tmpl = localStorage.getItem("smile_planning_templates"); if(tmpl) planningTemplates = JSON.parse(tmpl);
        
        if (isFirebaseReady) {
            setupFirestoreListener("activities", activities);
            setupFirestoreListener("woredaData", woredaData);
            setupFirestoreListener("reports", reports);
            setupFirestoreListener("students", students);
            setupFirestoreListener("cwdRecords", cwdRecords);
            setupFirestoreListener("staffList", staffList);
            setupFirestoreListener("dataCollectors", dataCollectors);
            setupFirestoreListener("planningTemplates", planningTemplates);
            db.collection("projectProfile").onSnapshot(snap => {
                if (!snap.empty) projectProfile = { ...projectProfile, ...snap.docs[0].data() };
                renderAll();
            });
            db.collection("resultsFramework").onSnapshot(snap => {
                if (!snap.empty) resultsFramework = { ...resultsFramework, ...snap.docs[0].data() };
                renderAll();
            });
        }
        renderAll();
    }

    // ==================== ALL ORIGINAL FUNCTIONS (unchanged, kept for compatibility) ====================
    function addNewActivity() { document.getElementById("addActivityModal").style.display = "flex"; }
    function confirmAddActivity() { let c=document.getElementById("newActivityCode").value.trim(), n=document.getElementById("newActivityName").value.trim(), t=parseInt(document.getElementById("newActivityTarget").value); if(!c||!n||!t) return; activities.push({code:c, name:n, unit:document.getElementById("newActivityUnit").value, target:t, achieved:0, type:document.getElementById("newActivityType").value}); saveAll(); document.getElementById("addActivityModal").style.display="none"; }
    function deleteActivity(i){ if(confirm("Delete?")){ activities.splice(i,1); saveAll(); } }
    function updateAchievement(i,v){ let n=parseInt(v); if(!isNaN(n)){ activities[i].achieved=Math.min(activities[i].target,Math.max(0,n)); saveAll(); } }
    function renderActivitiesTable(){ let t=document.getElementById("activitiesBody"); if(!t) return; t.innerHTML=""; activities.forEach((a,i)=>{ let p=(a.achieved/a.target)*100; let r=t.insertRow(); r.insertCell(0).innerHTML=a.code; r.insertCell(1).innerHTML=a.name; r.insertCell(2).innerHTML=a.type==="training"?`<span class="training-badge">Training</span>`:`<span>Non-Training</span>`; r.insertCell(3).innerHTML=a.unit; r.insertCell(4).innerHTML=a.target; r.insertCell(5).innerHTML=`<input type="number" value="${a.achieved}" style="width:70px;" onchange="updateAchievement(${i}, this.value)">`; r.insertCell(6).innerHTML=`<div class="progress-bar"><div class="progress-fill" style="width:${p}%"></div></div><span>${p.toFixed(0)}%</span>`; r.insertCell(7).innerHTML=`<span class="badge ${p>=100?'badge-success':'badge-warning'}">${p>=100?'Completed':'In Progress'}</span>`; let btn=document.createElement("button"); btn.innerHTML='<i class="fas fa-save"></i>'; btn.onclick=()=>updateAchievement(i, document.querySelector(`#achieve${i}`)?.value||a.achieved); r.insertCell(8).appendChild(btn); let db=document.createElement("button"); db.innerHTML='<i class="fas fa-trash"></i>'; db.className="btn-small"; db.style.background="#c23b22"; db.onclick=()=>deleteActivity(i); r.insertCell(9).appendChild(db); }); }
    function addWoredaData(){ let w=document.getElementById("woredaSelect").value, ac=document.getElementById("woredaActivitySelect").value; if(!w||!ac) return; let tot=parseInt(prompt("Total achieved:")); if(!isNaN(tot)) woredaData.push({woreda:w, activityCode:ac, total:tot, date:new Date().toLocaleDateString()}); saveAll(); }
    function populateWoredaSelects(){ let s=document.getElementById("woredaSelect"); if(s) s.innerHTML=woredaList.map(w=>`<option>${w}</option>`).join(''); let as=document.getElementById("woredaActivitySelect"); if(as) as.innerHTML=activities.map(a=>`<option value="${a.code}">${a.code}</option>`).join(''); let ns=document.getElementById("narrativeActivitySelect"); if(ns) ns.innerHTML='<option value="">--Select--</option>'+activities.map(a=>`<option value="${a.code}">${a.code}</option>`).join(''); let tas=document.getElementById("templateActivityCode"); if(tas) tas.innerHTML='<option value="">-- Select Activity --</option>'+activities.map(a=>`<option value="${a.code}">${a.code} - ${a.name}</option>`).join(''); }
    function renderWoredaTable(){ document.getElementById("woredaBody").innerHTML=woredaData.map((w,i)=>`<tr><td>${w.woreda}</td><td>${w.activityCode}</td><td colspan="3">Total: ${w.total}</td><td colspan="2">${w.date}</td><td><button onclick="woredaData.splice(${i},1);saveAll()">Del</button></td></tr>`).join(''); }
    function renderZonalSummary(){ document.getElementById("zonalSummaryContainer").innerHTML="<p>Zonal data loaded</p>"; }
    function saveNarrative(){ let code=document.getElementById("narrativeActivitySelect").value; if(!code) return; reports.push({id:Date.now(), activityCode:code, narrative:document.getElementById("narrativeText").value, date:new Date().toLocaleDateString()}); saveAll(); document.getElementById("narrativeText").value=""; alert("Saved"); }
    function renderReports(){ document.getElementById("reportsList").innerHTML=reports.map(r=>`<div class="report-card"><strong>${r.activityCode}</strong><div>${r.narrative}</div><div>${r.date}</div><button onclick="deleteReport(${r.id})">Delete</button></div>`).join('')||"No reports"; }
    function deleteReport(id){ reports=reports.filter(r=>r.id!==id); saveAll(); }
    function renderProgressDashboard(){ let tt=activities.reduce((s,a)=>s+a.target,0); let ta=activities.reduce((s,a)=>s+a.achieved,0); document.getElementById("progressStats").innerHTML=`<div class="stat-card"><strong>Progress</strong><div class="stat-number">${((ta/tt)*100).toFixed(1)}%</div></div>`; }

    function getHighestLevel(arr){ for(let i=4;i>=0;i--) if(arr[i]===1) return i; return 0; }
    function getBinaryArray(p){ let a=[]; for(let i=0;i<=4;i++) a.push(document.getElementById(p+i)?.checked?1:0); return a; }
    function addStudent(){ let n=document.getElementById("regFullName").value.trim(); if(!n) return; let s={id:Date.now(), name:n, school:document.getElementById("regSchoolName").value, grade:parseInt(document.getElementById("regGrade").value), gender:document.getElementById("regGender").value, zone:document.getElementById("regZone").value, woreda:document.getElementById("regWoreda").value, baseline:null, midline:null, endline:null}; let data={math:getBinaryArray("regMath"), oromo:getBinaryArray("regOromo"), eng:getBinaryArray("regEng")}; if(currentPhase==='baseline') s.baseline=data; else if(currentPhase==='midline') s.midline=data; else s.endline=data; students.push(s); saveAll(); document.getElementById("regFullName").value=""; for(let i=0;i<=4;i++){ document.getElementById("regMath"+i)&&(document.getElementById("regMath"+i).checked=false); document.getElementById("regOromo"+i)&&(document.getElementById("regOromo"+i).checked=false); document.getElementById("regEng"+i)&&(document.getElementById("regEng"+i).checked=false); } alert("Registered!"); }
    function deleteStudent(id){ students=students.filter(s=>s.id!==id); saveAll(); }
    function renderStudentTable(){ document.getElementById("studentBody").innerHTML=students.map(s=>`<tr><td>${s.name}</td><td>${s.school}</td><td>K${s.grade}</td><td>${s.gender==='Dhi'?'Boy':'Girl'}</td><td>${s.baseline?levelNames.oromo[getHighestLevel(s.baseline.oromo)]:'-'}</td><td>${s.midline?levelNames.oromo[getHighestLevel(s.midline.oromo)]:'-'}</td><td>${s.endline?levelNames.oromo[getHighestLevel(s.endline.oromo)]:'-'}</td><td><button onclick="deleteStudent(${s.id})">Del</button></td></tr>`).join('')||'<tr><td colspan="8">No students</td></tr>'; }
    function exportStudentCSV(){ let csv="Name,School,Grade,Gender,BaselineOromo\n"+students.map(s=>`"${s.name}","${s.school}",${s.grade},"${s.gender}","${s.baseline?levelNames.oromo[getHighestLevel(s.baseline.oromo)]:'-'}"`).join("\n"); let blob=new Blob([csv]); let a=document.createElement("a"); a.href=URL.createObjectURL(blob); a.download="students.csv"; a.click(); }
    function renderGradeLevels(){ let grades=[3,4,5,6], subjects=['oromo','eng','math'], subNames=['Oromo','English','Math'], levels=['L0','L1','L2','L3','L4']; let html=`<table class="level-breakdown-table"><thead><tr><th>Grade</th><th>Subject</th>${levels.map(l=>`<th>${l}</th>`).join('')}<th>Total</th></tr></thead><tbody>`; for(let g of grades){ let grp=students.filter(s=>s.grade===g&&s.baseline); for(let si=0;si<subjects.length;si++){ let counts=[0,0,0,0,0]; for(let s of grp) counts[getHighestLevel(s.baseline[subjects[si]])]++; html+=`<tr><td rowspan="${subjects.length}" style="${si===0?'':'display:none'}">K${g}</td><td><strong>${subNames[si]}</strong></td>${counts.map(c=>`<td>${c}</td>`).join('')}<td><strong>${grp.length}</strong></td></tr>`; } } html+=`</tbody></table>`; document.getElementById("gradeLevelsContainer").innerHTML=html; }
    function renderSexLevels(){ let sexes=[{code:'Dhi',name:'Boys'},{code:'Dub',name:'Girls'}], subjects=['oromo','eng','math'], subNames=['Oromo','English','Math'], levels=['L0','L1','L2','L3','L4']; let html=`<table class="level-breakdown-table"><thead><tr><th>Sex</th><th>Subject</th>${levels.map(l=>`<th>${l}</th>`).join('')}<th>Total</th></tr></thead><tbody>`; for(let sex of sexes){ let grp=students.filter(s=>s.gender===sex.code&&s.baseline); for(let si=0;si<subjects.length;si++){ let counts=[0,0,0,0,0]; for(let s of grp) counts[getHighestLevel(s.baseline[subjects[si]])]++; html+=`<tr><td rowspan="${subjects.length}" style="${si===0?'':'display:none'}">${sex.name}</td><td><strong>${subNames[si]}</strong></td>${counts.map(c=>`<td>${c}</td>`).join('')}<td><strong>${grp.length}</strong></td></tr>`; } } html+=`</tbody></table>`; document.getElementById("sexLevelsContainer").innerHTML=html; }
    function renderWoredaSummary(){ let map=new Map(); for(let s of students.filter(s=>s.baseline)){ let w=s.woreda||"Unknown"; if(!map.has(w)) map.set(w,[]); map.get(w).push(s); } let html=`<table class="level-breakdown-table"><thead><tr><th>Woreda</th><th>Students</th><th>Oromo L0-4</th></tr></thead><tbody>`; for(let [w,list] of map){ let cnt=[0,0,0,0,0]; for(let s of list) cnt[getHighestLevel(s.baseline.oromo)]++; html+=`<tr><td>${w}</td><td>${list.length}</td><td>${cnt.join('/')}</td></tr>`; } html+=`</tbody></table>`; document.getElementById("woredaSummaryContainer").innerHTML=html; }
    function renderSchoolSummary(){ let map=new Map(); for(let s of students.filter(s=>s.baseline)){ if(!map.has(s.school)) map.set(s.school,[]); map.get(s.school).push(s); } let html=`<table class="level-breakdown-table"><thead><tr><th>School</th><th>Students</th><th>Oromo L0-4</th></tr></thead><tbody>`; for(let [sch,list] of [...map.entries()].slice(0,15)){ let cnt=[0,0,0,0,0]; for(let s of list) cnt[getHighestLevel(s.baseline.oromo)]++; html+=`<tr><td>${sch.substring(0,25)}</td><td>${list.length}</td><td>${cnt.join('/')}</td></tr>`; } html+=`</tbody></table>`; document.getElementById("schoolSummaryContainer").innerHTML=html; }
    function renderZoneSummary(){ let map=new Map(); for(let s of students.filter(s=>s.baseline&&s.zone)){ if(!map.has(s.zone)) map.set(s.zone,[]); map.get(s.zone).push(s); } let html=`<table class="level-breakdown-table"><thead><tr><th>Zone</th><th>Students</th><th>Oromo L0-4</th></tr></thead><tbody>`; for(let [z,list] of map){ let cnt=[0,0,0,0,0]; for(let s of list) cnt[getHighestLevel(s.baseline.oromo)]++; html+=`<tr><td>${z}</td><td>${list.length}</td><td>${cnt.join('/')}</td></tr>`; } html+=`</tbody></table>`; document.getElementById("zoneSummaryContainer").innerHTML=html; }
    function renderProgressTracking(){ let withAll=students.filter(s=>s.baseline&&s.midline&&s.endline); let subjects=['oromo','eng','math'], subNames=['Oromo','English','Math']; let html=`<div class="progress-track">`; for(let si=0;si<subjects.length;si++){ let baseC=[0,0,0,0,0], midC=[0,0,0,0,0], endC=[0,0,0,0,0]; for(let s of withAll){ baseC[getHighestLevel(s.baseline[subjects[si]])]++; midC[getHighestLevel(s.midline[subjects[si]])]++; endC[getHighestLevel(s.endline[subjects[si]])]++; } html+=`<div style="background:#f9fefb;border-radius:12px;padding:12px;margin-bottom:10px;"><h4>${subNames[si]}</h4><table class="level-breakdown-table"><thead><tr><th>Phase</th><th>L0</th><th>L1</th><th>L2</th><th>L3</th><th>L4</th></tr></thead><tbody>
        <tr><td>Baseline</td>${baseC.map(c=>`<td>${c}</td>`).join('')}</tr>
        <tr><td>Midline</td>${midC.map(c=>`<td>${c}</td>`).join('')}</tr>
        <tr><td>Endline</td>${endC.map(c=>`<td>${c}</td>`).join('')}</tr>
        </tbody></table></div>`; } html+=`</div><p>Students with all three phases: ${withAll.length}</p>`; document.getElementById("progressTrackingContainer").innerHTML=html; }

    function addCwd(){ let n=document.getElementById("cwdName").value.trim(); if(!n) return; cwdRecords.push({id:Date.now(), name:n, school:document.getElementById("cwdSchool").value, serviceStage:document.getElementById("cwdServiceStage").value}); saveAll(); document.getElementById("cwdName").value=""; }
    function deleteCwd(id){ cwdRecords=cwdRecords.filter(c=>c.id!==id); saveAll(); }
    function renderCwdSummary(){ document.getElementById("cwdSummaryStats").innerHTML=`<div class="stat-card">Total CWDs: ${cwdRecords.length}</div>`; document.getElementById("cwdTablesContainer").innerHTML=`<table class="level-breakdown-table"><thead><tr><th>Name</th><th>School</th><th>Stage</th><th>Action</th></tr></thead><tbody>${cwdRecords.map(c=>`<tr><td>${c.name}</td><td>${c.school}</td><td>${c.serviceStage}</td><td><button onclick="deleteCwd(${c.id})">Del</button></td></tr>`).join('')}</tbody></table>`; }
    function exportCwdCSV(){ let csv="Name,School,Stage\n"+cwdRecords.map(c=>`"${c.name}","${c.school}","${c.serviceStage}"`).join("\n"); let blob=new Blob([csv]); let a=document.createElement("a"); a.href=URL.createObjectURL(blob); a.download="cwd.csv"; a.click(); }

    function addStaff(){ let n=document.getElementById("staffName").value.trim(); if(!n) return; staffList.push({id:Date.now(), name:n, role:document.getElementById("staffRole").value, dept:document.getElementById("staffDept").value, phone:document.getElementById("staffPhone").value}); saveAll(); document.getElementById("staffName").value=""; }
    function deleteStaff(id){ staffList=staffList.filter(s=>s.id!==id); saveAll(); }
    function renderStaff(){ document.getElementById("staffBody").innerHTML=staffList.map(s=>`<tr><td>${s.name}</td><td>${s.role}</td><td>${s.dept}</td><td>${s.phone}</td><td><button onclick="deleteStaff(${s.id})">Del</button></td></tr>`).join('')||'<tr><td colspan="5">No staff</td></tr>'; }
    function addDataCollector(){ let n=document.getElementById("dcName").value.trim(); if(!n) return; dataCollectors.push({id:Date.now(), name:n, zone:document.getElementById("dcZone").value, woreda:document.getElementById("dcWoreda").value, phone:document.getElementById("dcPhone").value, status:"pending"}); saveAll(); document.getElementById("dcName").value=""; alert("Registered. Waiting admin approval."); }
    function approveDC(id){ if(!adminSession){ alert("Admin login required!"); return; } let dc=dataCollectors.find(d=>d.id===id); if(dc) dc.status="approved"; saveAll(); alert("Approved"); }
    function rejectDC(id){ if(!adminSession) return; dataCollectors=dataCollectors.filter(d=>d.id!==id); saveAll(); }
    function renderDataCollectors(){ let pending=dataCollectors.filter(d=>d.status==="pending"); let approved=dataCollectors.filter(d=>d.status==="approved"); document.getElementById("dcPendingBody").innerHTML=pending.map(d=>`<tr><td>${d.name}</td><td>${d.zone}</td><td>${d.woreda}</td><td>${d.phone}</td><td><span class="badge badge-warning">Pending</span></td><td>${adminSession?`<button class="approve-btn btn-small" onclick="approveDC(${d.id})">Approve</button> <button class="reject-btn btn-small" onclick="rejectDC(${d.id})">Reject</button>`:`<span class="badge badge-danger">Login required</span>`}</td></tr>`).join('')||'<tr><td colspan="6">No pending</td></tr>';
        document.getElementById("dcApprovedBody").innerHTML=approved.map(d=>`<tr><td>${d.name}</td><td>${d.zone}</td><td>${d.woreda}</td><td>${d.phone}</td><td><span class="badge badge-success">Approved</span></td></tr>`).join('')||'<tr><td colspan="5">No approved</td></tr>';
        document.getElementById("adminLoginStatus").innerHTML=adminSession?'<span class="badge badge-success">Admin Online - Approval Active</span>':'<span class="badge badge-danger">Admin offline - Login to approve</span>';
    }
    function renderHRStats(){ document.getElementById("hrStats").innerHTML=`<div class="stat-card">Staff: ${staffList.length}</div><div class="stat-card">Data Collectors: ${dataCollectors.length}</div><div class="stat-card">Approved: ${dataCollectors.filter(d=>d.status==='approved').length}</div>`; }

    function adminLogin(){ let u=document.getElementById("adminUsername").value, p=document.getElementById("adminPassword").value; if(u==="mohammedscho2023" && p===currentAdminPassword){ adminSession=true; document.getElementById("adminLoginSection").style.display="none"; document.getElementById("adminContentSection").style.display="block"; document.getElementById("loginMessage").innerHTML=""; alert("Admin login successful!"); renderAll(); } else { document.getElementById("loginMessage").innerHTML="Invalid credentials!"; } }
    function changeAdminPassword(){ let np=document.getElementById("newAdminPassword").value; if(np.length<4) return; currentAdminPassword=np; localStorage.setItem("smile_admin_pass", np); alert("Password changed!"); }
    function adminLogout(){ adminSession=false; document.getElementById("adminLoginSection").style.display="block"; document.getElementById("adminContentSection").style.display="none"; renderAll(); }
    function renderAdminStats(){ document.getElementById("adminStudentCount").innerText=students.length; document.getElementById("adminCwdCount").innerText=cwdRecords.length; document.getElementById("adminStaffCount").innerText=staffList.length; }
    function exportAllData(){ let all={activities,woredaData,reports,students,cwdRecords,staffList,dataCollectors,projectProfile,resultsFramework,planningTemplates}; let blob=new Blob([JSON.stringify(all,null,2)],{type:"application/json"}); let a=document.createElement("a"); a.href=URL.createObjectURL(blob); a.download="smile_full_export.json"; a.click(); }
    function clearAllData(){ if(confirm("WARNING: Delete ALL data?")){ localStorage.clear(); location.reload(); } }

    function editField(id){ let val=document.getElementById(id).innerText; document.getElementById("editFieldId").value=id; document.getElementById("editFieldValue").value=val; document.getElementById("editModal").style.display="flex"; }
    function saveFieldEdit(){ let id=document.getElementById("editFieldId").value, val=document.getElementById("editFieldValue").value; if(projectProfile.hasOwnProperty(id)) projectProfile[id]=val; document.getElementById(id).innerText=val; if(id==="projName") document.getElementById("projectNameHeader").innerText=val; document.getElementById("editModal").style.display="none"; saveAll(); }
    function editProjectNameHeader(){ editField("projName"); }
    function editProjectProfile(){ alert("Click on any field to edit, then click Save."); }
    function editResultsFramework(){ alert("Click on table cells to edit directly, then click Save Framework."); }
    function saveResultsFramework(){ for(let key in resultsFramework){ let el=document.getElementById(`rf_${key}`); if(el) resultsFramework[key]=el.innerText; } saveAll(); alert("Framework saved!"); }
    function updateResultsFrameworkAchieved(){ let trained=activities.find(a=>a.code==="4.5.2")?.achieved||0; let srms=activities.find(a=>a.code==="4.2.6")?.achieved||0; let cwds=cwdRecords.length; document.getElementById("rf_out_achieved").innerText=`${trained} teachers, ${srms} SRMs`; document.getElementById("rf_out1_achieved").innerText=`${trained} teachers`; document.getElementById("rf_out2_achieved").innerText=`${srms} SRMs`; document.getElementById("rf_out3_achieved").innerText=`${cwds} CWDs`; document.getElementById("rf_goal_achieved").innerText=`${students.length} students assessed`; }
    function renderProjectKPI(){ let totalAchieved=activities.reduce((s,a)=>s+a.achieved,0); let totalTarget=activities.reduce((s,a)=>s+a.target,0); document.getElementById("projectKpiGrid").innerHTML=`<div class="stat-card">Overall: ${((totalAchieved/totalTarget)*100).toFixed(1)}%</div><div class="stat-card">Teachers: ${activities.find(a=>a.code==="4.5.2")?.achieved||0}/864</div><div class="stat-card">SRMs: ${activities.find(a=>a.code==="4.2.6")?.achieved||0}/31400</div><div class="stat-card">CWDs: ${cwdRecords.length}/124</div>`; updateResultsFrameworkAchieved(); }
    function renderProjectProfileUI(){ for(let k in projectProfile) if(document.getElementById(k)) document.getElementById(k).innerText=projectProfile[k]; document.getElementById("projectNameHeader").innerText=projectProfile.projName; for(let k in resultsFramework) if(document.getElementById(`rf_${k}`)) document.getElementById(`rf_${k}`).innerText=resultsFramework[k]; }

    function addTemplate() {
        const name = document.getElementById("templateName").value.trim();
        const activityCode = document.getElementById("templateActivityCode").value;
        const target = parseInt(document.getElementById("templateTarget").value);
        const startDate = document.getElementById("templateStart").value;
        const endDate = document.getElementById("templateEnd").value;
        const responsible = document.getElementById("templateResponsible").value.trim();
        const status = document.getElementById("templateStatus").value;
        const notes = document.getElementById("templateNotes").value.trim();
        if (!name || !activityCode || isNaN(target)) { alert("Please fill Template Name, Activity Code, and Target."); return; }
        const activity = activities.find(a => a.code === activityCode);
        planningTemplates.push({ id: Date.now(), name: name, activityCode: activityCode, activityName: activity ? activity.name : "", target: target, startDate: startDate, endDate: endDate, responsible: responsible, status: status, notes: notes });
        saveAll();
        document.getElementById("templateName").value = ""; document.getElementById("templateTarget").value = ""; document.getElementById("templateStart").value = ""; document.getElementById("templateEnd").value = ""; document.getElementById("templateResponsible").value = ""; document.getElementById("templateNotes").value = "";
        alert("Template saved.");
    }
    function deleteTemplate(id) { if (confirm("Delete this planning template?")) { planningTemplates = planningTemplates.filter(t => t.id !== id); saveAll(); } }
    function renderTemplates() { const tbody = document.getElementById("templatesBody"); if (!tbody) return; tbody.innerHTML = planningTemplates.map(t => `<tr><td>${escapeHtml(t.name)}</td><td>${escapeHtml(t.activityCode)} - ${escapeHtml(t.activityName || '')}</td><td>${t.target}</td><td>${t.startDate || '-'}</td><td>${t.endDate || '-'}</td><td>${escapeHtml(t.responsible || '-')}</td><td><span class="badge ${t.status === 'Completed' ? 'badge-success' : (t.status === 'In Progress' ? 'badge-info' : 'badge-warning')}">${t.status}</span></td><td>${escapeHtml(t.notes || '-')}</td><td><button class="btn-small" onclick="deleteTemplate(${t.id})"><i class="fas fa-trash"></i></button></td></tr>`).join(''); }
    function escapeHtml(str) { if (!str) return ''; return str.replace(/[&<>]/g, function(m) { if (m === '&') return '&amp;'; if (m === '<') return '&lt;'; if (m === '>') return '&gt;'; return m; }); }

    function createCheckboxes(cid,prefix){ let c=document.getElementById(cid); if(!c) return; let labs=cid==='mathGroup'?['L0','L1','L2','L3','L4']:(cid==='oromoGroup'?levelNames.oromo:levelNames.eng); c.innerHTML=''; for(let i=0;i<=4;i++) c.innerHTML+=`<label class="binary-option"><input type="checkbox" id="${prefix}${i}"> <span>${labs[i]}</span></label>`; }
    function renderAll(){ renderActivitiesTable(); renderWoredaTable(); renderZonalSummary(); renderReports(); renderProgressDashboard(); renderStudentTable(); renderGradeLevels(); renderSexLevels(); renderWoredaSummary(); renderSchoolSummary(); renderZoneSummary(); renderProgressTracking(); renderCwdSummary(); renderStaff(); renderDataCollectors(); renderHRStats(); renderProjectKPI(); renderAdminStats(); renderProjectProfileUI(); renderTemplates(); populateWoredaSelects(); }

    // Event Listeners
    document.getElementById("resetProgressBtn")?.addEventListener("click", ()=>{ activities.forEach(a=>a.achieved=0); saveAll(); });
    document.getElementById("addWoredaDataBtn")?.addEventListener("click", addWoredaData);
    document.getElementById("refreshZonalBtn")?.addEventListener("click", renderZonalSummary);
    document.getElementById("saveNarrativeBtn")?.addEventListener("click", saveNarrative);
    document.getElementById("clearNarrativeBtn")?.addEventListener("click", ()=>document.getElementById("narrativeText").value="");
    document.getElementById("addStudentBtn")?.addEventListener("click", addStudent);
    document.getElementById("clearStudentBtn")?.addEventListener("click", ()=>{ document.getElementById("regFullName").value=""; for(let i=0;i<=4;i++){ document.getElementById("regMath"+i)&&(document.getElementById("regMath"+i).checked=false); document.getElementById("regOromo"+i)&&(document.getElementById("regOromo"+i).checked=false); document.getElementById("regEng"+i)&&(document.getElementById("regEng"+i).checked=false); } });
    document.getElementById("exportStudentBtn")?.addEventListener("click", exportStudentCSV);
    document.getElementById("addCwdBtn")?.addEventListener("click", addCwd);
    document.getElementById("clearCwdBtn")?.addEventListener("click", ()=>{ document.getElementById("cwdName").value=""; document.getElementById("cwdSchool").value=""; });
    document.getElementById("exportCwdBtn")?.addEventListener("click", exportCwdCSV);
    document.getElementById("addStaffBtn")?.addEventListener("click", addStaff);
    document.getElementById("addDcBtn")?.addEventListener("click", addDataCollector);
    document.getElementById("addNewActivityBtn")?.addEventListener("click", addNewActivity);
    document.getElementById("confirmAddActivityBtn")?.addEventListener("click", confirmAddActivity);
    document.getElementById("closeActivityModalBtn")?.addEventListener("click", ()=>document.getElementById("addActivityModal").style.display="none");
    document.getElementById("adminLoginBtn")?.addEventListener("click", adminLogin);
    document.getElementById("changeAdminPasswordBtn")?.addEventListener("click", changeAdminPassword);
    document.getElementById("adminLogoutBtn")?.addEventListener("click", adminLogout);
    document.getElementById("exportAllDataBtn")?.addEventListener("click", exportAllData);
    document.getElementById("clearAllDataBtn")?.addEventListener("click", clearAllData);
    document.getElementById("saveProjectProfileBtn")?.addEventListener("click", ()=>{ saveAll(); alert("Saved"); });
    document.getElementById("saveRFBtn")?.addEventListener("click", saveResultsFramework);
    document.getElementById("saveEditFieldBtn")?.addEventListener("click", saveFieldEdit);
    document.getElementById("closeEditModalBtn")?.addEventListener("click", ()=>document.getElementById("editModal").style.display="none");
    document.getElementById("addTemplateBtn")?.addEventListener("click", addTemplate);
    document.getElementById("saveTemplateBtn")?.addEventListener("click", addTemplate);
    document.getElementById("testSaveBtn")?.addEventListener("click", saveData);   // your added button

    document.querySelectorAll('.phase-btn').forEach(btn=>btn.addEventListener('click',function(){ document.querySelectorAll('.phase-btn').forEach(b=>b.classList.remove('active-phase')); this.classList.add('active-phase'); currentPhase=this.getAttribute('data-phase'); renderStudentTable(); }));
    document.querySelectorAll('.summary-tab').forEach(tab=>{ tab.addEventListener('click',function(){ let t=this.getAttribute('data-summary'); document.querySelectorAll('.summary-tab').forEach(s=>s.classList.remove('active-tab')); this.classList.add('active-tab'); document.querySelectorAll('.summary-content').forEach(c=>c.classList.remove('active-summary')); document.getElementById(`summary-${t}`).classList.add('active-summary'); }); });
    document.querySelectorAll('.nav-btn').forEach(btn=>{ btn.addEventListener('click',function(){ let pid=this.getAttribute('data-panel'); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active')); this.classList.add('active'); document.querySelectorAll('.panel').forEach(p=>p.classList.remove('active-panel')); document.getElementById(pid).classList.add('active-panel'); renderAll(); }); });
    createCheckboxes('mathGroup','regMath'); createCheckboxes('oromoGroup','regOromo'); createCheckboxes('engGroup','regEng');
    populateWoredaSelects();
    loadData();
    window.deleteStaff=deleteStaff; window.deleteStudent=deleteStudent; window.deleteCwd=deleteCwd; window.deleteReport=deleteReport; window.updateAchievement=updateAchievement; window.deleteActivity=deleteActivity; window.approveDC=approveDC; window.rejectDC=rejectDC; window.deleteTemplate=deleteTemplate;
</script>
</body>
</html>
