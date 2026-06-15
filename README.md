<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Testimony Tutors - Full Stack (Mobile Ready)</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jsbarcode/3.11.6/JsBarcode.all.min.js"></script>
    <script src="https://unpkg.com/@zxing/library@0.19.1/umd/index.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js"></script>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        :root {
            --red: #D62828; --blue: #2563EB; --gold: #F5A623; --dark: #1a1a2e; --surface: #f8f9fc; --card: #ffffff;
            --text: #1e293b; --muted: #64748b; --border: #e2e8f0; --success: #059669; --warning: #D97706; --danger: #DC2626;
            --alert-bg: #FEF3C7; --alert-border: #F59E0B;
        }
        body { font-family: 'Segoe UI', system-ui, sans-serif; background: var(--surface); color: var(--text); min-height: 100vh; }
        @keyframes scanAnim { 0%{top:0} 50%{top:calc(100% - 3px)} 100%{top:0} }
        @keyframes pulse { 0%,100%{transform:scale(1)} 50%{transform:scale(1.04)} }
        @keyframes fadeIn { from{opacity:0;transform:translateY(8px)} to{opacity:1;transform:translateY(0)} }

        .login-container { min-height:100vh; display:flex; align-items:center; justify-content:center; background:linear-gradient(135deg,#1a1a2e 0%,#16213e 50%,#0f3460 100%); padding:20px; }
        .login-box { background:white; border-radius:24px; box-shadow:0 25px 80px rgba(0,0,0,0.4); width:100%; max-width:460px; padding:48px 40px; }
        .login-logo { text-align:center; margin-bottom:32px; }
        .login-logo .brand { font-family:'Arial Black',sans-serif; font-size:38px; font-weight:900; color:var(--red); text-transform:uppercase; letter-spacing:3px; line-height:1; }
        .login-logo .brand span { display:block; font-size:18px; color:var(--blue); letter-spacing:10px; margin-top:2px; }
        .brand-bar { width:80px; height:4px; background:linear-gradient(90deg,var(--red),var(--blue)); margin:14px auto 0; border-radius:4px; }
        .login-tabs { display:flex; border-bottom:2px solid var(--border); margin-bottom:28px; }
        .login-tab { flex:1; text-align:center; padding:12px; cursor:pointer; color:var(--muted); font-weight:600; font-size:15px; transition:all 0.25s; }
        .login-tab.active { color:var(--red); border-bottom:3px solid var(--red); margin-bottom:-2px; }
        .login-form { display:none; }
        .login-form.active { display:block; }
        .form-group { margin-bottom:20px; }
        .form-group label { display:block; margin-bottom:6px; color:var(--text); font-weight:600; font-size:14px; }
        .form-group input, .form-group select, .form-group textarea { width:100%; padding:13px 16px; border:1.5px solid var(--border); border-radius:12px; font-size:15px; transition:border-color 0.2s; background:var(--surface); color:var(--text); }
        .form-group input:focus, .form-group select:focus, .form-group textarea:focus { outline:none; border-color:var(--red); background:white; }
        .login-btn { width:100%; padding:15px; background:linear-gradient(135deg,var(--red),var(--blue)); color:white; border:none; border-radius:12px; font-size:16px; font-weight:700; cursor:pointer; transition:all 0.3s; letter-spacing:0.5px; }
        .login-btn:hover { transform:translateY(-2px); box-shadow:0 8px 25px rgba(214,40,40,0.35); }
        .dashboard { display:none; min-height:100vh; }
        .dashboard.active { display:block; }
        .sidebar {
            position:fixed; left:0; top:0; bottom:0; width:270px;
            background:linear-gradient(180deg,#1a1a2e 0%,#16213e 100%);
            color:white; overflow-y:auto; z-index:50; transition: transform 0.3s;
        }
        .sidebar-header { padding:28px 20px 20px; text-align:center; border-bottom:1px solid rgba(255,255,255,0.08); }
        .sidebar-logo .brand { font-family:'Arial Black',sans-serif; font-size:26px; font-weight:900; color:var(--red); text-transform:uppercase; letter-spacing:2px; }
        .sidebar-logo .brand span { display:block; font-size:13px; color:var(--gold); letter-spacing:7px; }
        .sidebar-header p { font-size:12px; color:rgba(255,255,255,0.5); margin-top:8px; }
        .sidebar-menu { padding:16px 0; }
        .menu-section { padding:12px 20px 6px; font-size:10px; text-transform:uppercase; letter-spacing:2px; color:rgba(255,255,255,0.3); font-weight:700; }
        .menu-item { padding:13px 22px; display:flex; align-items:center; color:rgba(255,255,255,0.7); cursor:pointer; transition:all 0.25s; border-left:3px solid transparent; font-size:14px; font-weight:500; }
        .menu-item:hover, .menu-item.active { background:rgba(255,255,255,0.07); color:white; border-left-color:var(--red); }
        .menu-item .icon { margin-right:14px; font-size:18px; width:22px; text-align:center; }
        .menu-item .badge { margin-left:auto; background:var(--red); color:white; font-size:11px; font-weight:700; padding:2px 8px; border-radius:20px; }
        .main-content { margin-left:270px; padding:24px; min-height:100vh; transition: margin-left 0.3s; }
        .top-bar { background:white; padding:18px 28px; border-radius:16px; margin-bottom:24px; display:flex; justify-content:space-between; align-items:center; box-shadow:0 2px 12px rgba(0,0,0,0.06); }
        .welcome-text h3 { color:var(--text); font-size:19px; font-weight:700; }
        .welcome-text p { color:var(--muted); font-size:13px; margin-top:3px; }
        .top-right { display:flex; align-items:center; gap:16px; }
        .top-alert-badge { background:var(--alert-bg); border:1px solid var(--alert-border); color:var(--warning); padding:6px 14px; border-radius:20px; font-size:13px; font-weight:600; cursor:pointer; display:none; }
        .top-alert-badge.visible { display:flex; align-items:center; gap:6px; }
        .user-avatar { width:44px; height:44px; border-radius:50%; background:linear-gradient(135deg,var(--red),var(--blue)); color:white; display:flex; align-items:center; justify-content:center; font-size:16px; font-weight:700; }
        .logout-btn { padding:8px 18px; background:#fee2e2; color:var(--red); border:none; border-radius:8px; cursor:pointer; font-weight:600; font-size:13px; transition:all 0.2s; }
        .logout-btn:hover { background:var(--red); color:white; }
        .content-section { display:none; }
        .content-section.active { display:block; }
        .section-card { background:white; border-radius:16px; padding:28px; margin-bottom:24px; box-shadow:0 2px 12px rgba(0,0,0,0.06); }
        .section-header { display:flex; justify-content:space-between; align-items:center; margin-bottom:24px; }
        .section-header h2 { color:var(--text); font-size:22px; font-weight:700; }
        .section-header .actions { display:flex; gap:10px; flex-wrap:wrap; }
        .stats-grid { display:grid; grid-template-columns:repeat(auto-fit,minmax(220px,1fr)); gap:20px; margin-bottom:28px; }
        .stat-card { background:white; padding:24px; border-radius:16px; box-shadow:0 2px 12px rgba(0,0,0,0.06); cursor:pointer; transition:all 0.3s; border:1.5px solid var(--border); }
        .stat-card:hover { transform:translateY(-4px); box-shadow:0 8px 24px rgba(214,40,40,0.12); border-color:var(--red); }
        .stat-card .label { color:var(--muted); font-size:13px; font-weight:600; margin-bottom:10px; text-transform:uppercase; letter-spacing:0.5px; }
        .stat-card .number { font-size:34px; font-weight:800; color:var(--red); }
        .stat-card .sub { font-size:12px; color:var(--muted); margin-top:4px; }
        .alert-panel { background:var(--alert-bg); border:1.5px solid var(--alert-border); border-radius:12px; padding:18px 22px; margin-bottom:20px; }
        .alert-panel h4 { color:var(--warning); font-size:15px; margin-bottom:12px; display:flex; align-items:center; gap:8px; }
        .alert-item { display:flex; justify-content:space-between; align-items:center; padding:10px 14px; background:white; border-radius:8px; margin-bottom:8px; border-left:4px solid var(--warning); }
        .alert-item.critical { border-left-color:var(--danger); }
        .alert-item .ai { font-weight:600; font-size:14px; }
        .alert-item .ad { font-size:12px; color:var(--muted); }
        .alert-item .ab { padding:5px 12px; border:none; border-radius:6px; cursor:pointer; font-size:12px; font-weight:600; }
        .btn-warn { background:var(--warning); color:white; }
        .btn-danger { background:var(--danger); color:white; }
        .table-responsive { overflow-x:auto; }
        table { width:100%; border-collapse:collapse; }
        th { padding:14px 16px; text-align:left; background:var(--surface); font-weight:700; color:var(--red); font-size:13px; text-transform:uppercase; letter-spacing:0.5px; }
        td { padding:14px 16px; border-bottom:1px solid var(--border); font-size:14px; }
        tr:hover td { background:#fafbff; }
        .badge { padding:4px 12px; border-radius:20px; font-size:12px; font-weight:700; display:inline-block; }
        .badge-green { background:#d1fae5; color:#059669; }
        .badge-red { background:#fee2e2; color:var(--danger); }
        .badge-yellow { background:#fef3c7; color:var(--warning); }
        .badge-blue { background:#dbeafe; color:var(--blue); }
        .badge-gray { background:#f1f5f9; color:var(--muted); }
        .action-btns { display:flex; gap:6px; flex-wrap:wrap; }
        .action-btn { padding:6px 12px; border:none; border-radius:6px; cursor:pointer; font-size:12px; font-weight:600; transition:all 0.2s; }
        .edit-btn { background:#dbeafe; color:var(--blue); }
        .view-btn { background:#fce7f3; color:#be185d; }
        .delete-btn { background:#fee2e2; color:var(--danger); }
        .id-btn { background:#fef3c7; color:var(--warning); }
        .pay-btn { background:#d1fae5; color:var(--success); }
        .attend-btn { background:#ede9fe; color:#7c3aed; }
        .btn { padding:11px 22px; border:none; border-radius:10px; cursor:pointer; font-weight:600; font-size:14px; transition:all 0.25s; }
        .btn-primary { background:linear-gradient(135deg,var(--red),var(--blue)); color:white; }
        .btn-primary:hover { transform:translateY(-2px); box-shadow:0 5px 18px rgba(214,40,40,0.3); }
        .btn-success { background:var(--success); color:white; }
        .btn-success:hover { background:#047857; }
        .btn-outline { background:transparent; border:2px solid var(--red); color:var(--red); }
        .btn-outline:hover { background:var(--red); color:white; }
        .btn-warning { background:var(--warning); color:white; }
        .search-bar { display:flex; gap:10px; margin-bottom:20px; flex-wrap:wrap; }
        .search-bar input, .search-bar select { padding:11px 16px; border:1.5px solid var(--border); border-radius:10px; font-size:14px; transition:border-color 0.2s; }
        .search-bar input { flex:1; min-width:200px; }
        .search-bar input:focus, .search-bar select:focus { outline:none; border-color:var(--red); }
        .form-row { display:grid; grid-template-columns:1fr 1fr; gap:18px; }
        .form-full { grid-column:1/-1; }
        .modal { display:none; position:fixed; inset:0; background:rgba(0,0,0,0.55); z-index:200; align-items:center; justify-content:center; padding:20px; }
        .modal.active { display:flex; }
        .modal-content { background:white; border-radius:20px; padding:32px; width:100%; max-width:680px; max-height:92vh; overflow-y:auto; }
        .modal-header { display:flex; justify-content:space-between; align-items:center; margin-bottom:24px; }
        .modal-header h3 { color:var(--text); font-size:20px; font-weight:700; }
        .close-modal { font-size:26px; cursor:pointer; color:var(--muted); line-height:1; }
        .close-modal:hover { color:var(--danger); }
        .grid-3 { display:grid; grid-template-columns:repeat(auto-fit,minmax(280px,1fr)); gap:20px; }
        .branch-card { background:var(--surface); padding:24px; border-radius:14px; border:1.5px solid var(--border); }
        .branch-card h3 { color:var(--red); font-size:18px; margin-bottom:12px; }
        .branch-card p { color:var(--muted); font-size:14px; line-height:1.6; margin-bottom:8px; }
        .branch-stats { display:flex; justify-content:space-between; padding-top:14px; border-top:1px solid var(--border); margin-top:12px; font-size:14px; }
        .id-card-wrap { display:flex; justify-content:center; margin:20px 0; }
        .id-card { width:340px; background:white; border-radius:16px; box-shadow:0 12px 40px rgba(0,0,0,0.18); overflow:hidden; border:2px solid #e5e7eb; position:relative; }
        .id-card-top { height:12px; background:linear-gradient(90deg,var(--red) 0%,var(--blue) 100%); }
        .id-card-body { padding:20px; }
        .id-card-header { text-align:center; margin-bottom:16px; padding-bottom:14px; border-bottom:2px dashed var(--red); }
        .id-card-header h2 { color:var(--red); font-size:20px; font-weight:900; letter-spacing:1px; }
        .id-card-header p { color:var(--blue); font-size:11px; letter-spacing:4px; text-transform:uppercase; margin-top:2px; }
        .id-card-photo { width:90px; height:90px; background:linear-gradient(135deg,var(--red),var(--blue)); border-radius:50%; margin:0 auto 14px; display:flex; align-items:center; justify-content:center; color:white; font-size:36px; font-weight:800; border:3px solid var(--gold); }
        .id-card-detail { display:flex; margin-bottom:7px; font-size:13px; }
        .id-card-lbl { color:var(--blue); font-weight:700; width:90px; flex-shrink:0; }
        .id-card-val { color:var(--text); }
        .id-card-qr-section { background:white; padding:10px; margin:10px 0; text-align:center; }
        .barcode-strip-wrap { text-align:center; margin:20px 0; }
        .barcode-strip { display:inline-block; background:white; padding:10px 16px 6px; border-radius:8px; box-shadow:0 2px 10px rgba(0,0,0,0.12); border:1.5px solid var(--border); }
        .attendance-scanner { background:linear-gradient(135deg,#1a1a2e,#16213e); border-radius:20px; padding:36px; text-align:center; margin-bottom:24px; color:white; }
        .scanner-icon { font-size:56px; margin-bottom:12px; animation:pulse 2s infinite; }
        .scanner-input-row { display:flex; gap:12px; justify-content:center; flex-wrap:wrap; }
        .scanner-input { padding:14px 20px; border:2px solid rgba(255,255,255,0.2); border-radius:12px; background:rgba(255,255,255,0.1); color:white; font-size:16px; font-family:'Courier New',monospace; letter-spacing:2px; width:260px; text-align:center; outline:none; transition:border-color 0.2s; }
        .scanner-input::placeholder { color:rgba(255,255,255,0.35); }
        .scanner-input:focus { border-color:var(--gold); }
        .scan-btn { padding:14px 28px; background:linear-gradient(135deg,var(--red),var(--gold)); color:white; border:none; border-radius:12px; font-size:15px; font-weight:700; cursor:pointer; transition:all 0.25s; }
        .scan-btn:hover { transform:scale(1.04); }
        .scan-result { margin-top:20px; padding:18px 24px; border-radius:12px; font-weight:700; font-size:16px; display:none; }
        .scan-result.success { background:rgba(5,150,105,0.2); border:1.5px solid #059669; color:#6ee7b7; }
        .scan-result.error { background:rgba(220,38,38,0.2); border:1.5px solid #DC2626; color:#fca5a5; }
        .attendance-tabs { display:flex; gap:12px; margin-bottom:20px; }
        .att-tab { padding:10px 22px; border:2px solid var(--border); border-radius:10px; cursor:pointer; font-weight:600; font-size:14px; transition:all 0.2s; }
        .att-tab.active { background:var(--red); color:white; border-color:var(--red); }
        .cbt-option { padding:14px 18px; margin:10px 0; background:var(--surface); border:2px solid var(--border); border-radius:10px; cursor:pointer; transition:all 0.2s; display:flex; align-items:center; gap:12px; }
        .cbt-option:hover { background:#fff; border-color:var(--blue); }
        .cbt-option.selected { background:#eff6ff; border-color:var(--blue); }
        .cbt-option.correct { background:#f0fdf4; border-color:var(--success); }
        .cbt-option.wrong { background:#fee2e2; border-color:var(--danger); }
        .cbt-timer { font-size:20px; font-weight:800; color:var(--red); text-align:right; margin-bottom:12px; }
        .cbt-progress { font-size:16px; color:var(--muted); font-weight:600; margin-bottom:20px; text-align:center; }
        .subjects-grid { display:grid; grid-template-columns:repeat(auto-fill,minmax(180px,1fr)); gap:14px; }
        .subject-tag { background:var(--surface); padding:18px; border-radius:12px; border:1.5px solid var(--border); text-align:center; cursor:pointer; transition:all 0.25s; }
        .subject-tag:hover { transform:translateY(-3px); box-shadow:0 6px 20px rgba(214,40,40,0.1); border-color:var(--red); }
        .subject-tag h4 { color:var(--red); font-size:15px; margin-bottom:6px; }
        .subject-tag p { color:var(--muted); font-size:12px; }
        @media print { body * { visibility:hidden; } #printArea, #printArea * { visibility:visible; } #printArea { position:fixed; top:0; left:0; width:100%; } }

        .hamburger { display: none; flex-direction: column; gap: 5px; cursor: pointer; padding: 10px; z-index: 110; }
        .hamburger span { display: block; width: 25px; height: 3px; background: var(--text); border-radius: 3px; transition: 0.3s; }
        .sidebar-overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.5); z-index: 45; }

        @media (max-width: 900px) {
            .sidebar { transform: translateX(-100%); transition: transform 0.3s; z-index: 100; }
            .sidebar.open { transform: translateX(0); }
            .main-content { margin-left: 0; }
            .form-row { grid-template-columns: 1fr; }
            .hamburger { display: flex; }
            .sidebar-overlay.active { display: block; }
            .top-bar { padding: 14px 16px; }
        }
        .empty-state { text-align:center; padding:50px 20px; color:var(--muted); }
        .empty-state .icon { font-size:48px; margin-bottom:14px; }
        .note { background:#eff6ff; border-left:4px solid var(--blue); padding:12px 16px; border-radius:0 8px 8px 0; font-size:13px; color:var(--blue); margin-bottom:20px; }
        .divider { border:none; border-top:1px solid var(--border); margin:20px 0; }
        .tag-pill { display:inline-block; padding:3px 10px; border-radius:20px; font-size:11px; font-weight:700; margin:2px; background:#dbeafe; color:var(--blue); }
    </style>
</head>
<body>
<div id="loginContainer" class="login-container">
    <div class="login-box">
        <div class="login-logo">
            <div class="brand">TESTIMONY<span>TUTORS</span></div>
            <div class="brand-bar"></div>
        </div>
        <div class="login-tabs">
            <div class="login-tab active" onclick="switchTab('student')">🎓 Student</div>
            <div class="login-tab" onclick="switchTab('admin')">🔐 Admin</div>
        </div>
        <div id="studentLogin" class="login-form active">
            <form onsubmit="handleStudentLogin(event)">
                <div class="form-group"><label>Registration Number</label><input type="text" id="studentRegNo" placeholder="Enter your Reg No." required></div>
                <div class="form-group"><label>Password</label><input type="password" id="studentPassword" placeholder="Enter your password" required></div>
                <button type="submit" class="login-btn">Login to Student Portal</button>
            </form>
        </div>
        <div id="adminLogin" class="login-form">
            <form onsubmit="handleAdminLogin(event)">
                <div class="form-group"><label>Admin ID</label><input type="text" id="adminId" placeholder="Enter admin ID" required></div>
                <div class="form-group"><label>Password</label><input type="password" id="adminPassword" placeholder="Enter password" required></div>
                <button type="submit" class="login-btn">Login to Admin Dashboard</button>
            </form>
        </div>
    </div>
</div>

<!-- ADMIN DASHBOARD -->
<div id="adminDashboard" class="dashboard">
    <div class="sidebar-overlay" id="adminOverlay" onclick="closeSidebar('admin')"></div>
    <div class="sidebar" id="adminSidebar">
        <div class="sidebar-header">
            <div class="sidebar-logo"><div class="brand">TESTIMONY<span>TUTORS</span></div></div>
            <p>Admin Dashboard</p>
        </div>
        <div class="sidebar-menu">
            <div class="menu-section">Overview</div>
            <div class="menu-item active" onclick="showAdmin('dashboard'); closeSidebar('admin')"><span class="icon">📊</span> Dashboard</div>
            <div class="menu-section">Students</div>
            <div class="menu-item" onclick="showAdmin('students'); closeSidebar('admin')"><span class="icon">👥</span> Manage Students</div>
            <div class="menu-item" onclick="showAdmin('addStudent'); closeSidebar('admin')"><span class="icon">➕</span> Add New Student</div>
            <div class="menu-section">Monitoring</div>
            <div class="menu-item" onclick="showAdmin('attendance'); closeSidebar('admin')"><span class="icon">📋</span> Attendance <span class="badge" id="attendanceBadge">0</span></div>
            <div class="menu-item" onclick="showAdmin('payments'); closeSidebar('admin')"><span class="icon">💰</span> Payments <span class="badge" id="paymentAlertBadge" style="background:var(--warning)">0</span></div>
            <div class="menu-item" onclick="showAdmin('tracking'); closeSidebar('admin')"><span class="icon">📈</span> Student Tracking</div>
            <div class="menu-section">Academics</div>
            <div class="menu-item" onclick="showAdmin('subjects'); closeSidebar('admin')"><span class="icon">📚</span> Subjects</div>
            <div class="menu-item" onclick="showAdmin('manageSubjects'); closeSidebar('admin')"><span class="icon">🔧</span> Manage Subjects</div>
            <div class="menu-item" onclick="showAdmin('cbt'); closeSidebar('admin')"><span class="icon">💻</span> CBT Questions</div>
            <div class="menu-item" onclick="showAdmin('tests'); closeSidebar('admin')"><span class="icon">📝</span> Tests & Exams</div>
            <div class="menu-section">Settings</div>
            <div class="menu-item" onclick="showAdmin('branches'); closeSidebar('admin')"><span class="icon">🏢</span> Branches</div>
        </div>
    </div>
    <div class="main-content">
        <div class="top-bar">
            <div class="hamburger" onclick="toggleSidebar('admin')"><span></span><span></span><span></span></div>
            <div class="welcome-text"><h3>Welcome, Admin</h3><p id="adminDateLine">Loading date...</p></div>
            <div class="top-right">
                <div class="top-alert-badge" id="topAlertBadge" onclick="showAdmin('payments')">⚠️ <span id="topAlertCount">0</span> Payment Alert(s)</div>
                <div class="user-avatar">AD</div>
                <button class="logout-btn" onclick="logout()">Logout</button>
            </div>
        </div>

        <!-- Admin Dashboard View -->
        <div id="adminDashboardView" class="content-section active">
            <div class="stats-grid" id="adminStatsGrid"></div>
            <div class="section-card" id="paymentAlertsCard" style="display:none"><div class="alert-panel" id="paymentAlertsPanel"></div></div>
            <div class="section-card">
                <div class="section-header"><h2>Recent Students</h2><button class="btn btn-primary" onclick="showAdmin('addStudent')">+ Add Student</button></div>
                <div class="table-responsive"><table><thead><tr><th>Reg No.</th><th>Name</th><th>Dept</th><th>Branch</th><th>Payment</th><th>Status</th><th>Actions</th></tr></thead><tbody id="recentStudentsTable"></tbody></table></div>
            </div>
        </div>

        <!-- Manage Students -->
        <div id="adminStudentsView" class="content-section">
            <div class="section-card">
                <div class="section-header"><h2>Manage Students</h2><button class="btn btn-primary" onclick="showAdmin('addStudent')">+ Add Student</button></div>
                <div class="search-bar"><input type="text" id="searchStudent" placeholder="Search by name, reg no, branch..." onkeyup="searchStudents()"><select onchange="filterStudentsByBranch(this.value)"><option value="">All Branches</option><option value="Iyana Agbala">Iyana Agbala</option><option value="Omi Bridge">Omi Bridge</option><option value="Ajameta">Ajameta</option></select></div>
                <div class="table-responsive"><table><thead><tr><th>Reg No.</th><th>Name</th><th>Dept</th><th>Branch</th><th>Payment</th><th>Expiry</th><th>Status</th><th>Actions</th></tr></thead><tbody id="studentsTableBody"></tbody></table></div>
            </div>
        </div>

        <!-- Add Student -->
        <div id="adminAddStudentView" class="content-section">
            <div class="section-card">
                <div class="section-header"><h2>Register New Student</h2></div>
                <div class="note">📌 Subjects auto-assigned by department. A unique QR code will be generated for the student's ID card upon registration.</div>
                <form id="addStudentForm" onsubmit="addStudent(event)">
                    <div class="form-row"><div class="form-group"><label>First Name *</label><input type="text" id="firstName" required></div><div class="form-group"><label>Last Name *</label><input type="text" id="lastName" required></div></div>
                    <div class="form-row"><div class="form-group"><label>Date of Birth *</label><input type="date" id="dob" required></div><div class="form-group"><label>Gender *</label><select id="gender" required><option value="">Select</option><option>Male</option><option>Female</option></select></div></div>
                    <div class="form-row"><div class="form-group"><label>Email *</label><input type="email" id="email" required></div><div class="form-group"><label>Phone *</label><input type="tel" id="phone" required></div></div>
                    <div class="form-group"><label>Address *</label><textarea id="address" rows="2" required></textarea></div>
                    <div class="form-row"><div class="form-group"><label>Branch *</label><select id="branch" required><option value="">Select Branch</option><option>Iyana Agbala</option><option>Omi Bridge</option><option>Ajameta</option></select></div><div class="form-group"><label>Department *</label><select id="department" required><option value="">Select Dept</option><option>HUMANITIES</option><option>BUSINESS</option><option>SCIENCES</option></select></div></div>
                    <div class="form-row"><div class="form-group"><label>Registration Date *</label><input type="date" id="regDate" required></div><div class="form-group"><label>Payment Plan *</label><select id="paymentPlan" required onchange="updatePaymentFields()"><option value="">Select Plan</option><option value="full">Full Payment — ₦10,000 / 30 days</option><option value="half">Half Payment — ₦5,000 / 14 days</option></select></div></div>
                    <div class="form-row"><div class="form-group"><label>Amount Paid (₦)</label><input type="number" id="amountPaid" value="0" readonly></div><div class="form-group"><label>Payment Date *</label><input type="date" id="paymentDate" required></div></div>
                    <div id="paymentSummary" style="margin-bottom:16px;"></div>
                    <div class="form-row"><div class="form-group"><label>Guardian Name *</label><input type="text" id="guardianName" required></div><div class="form-group"><label>Guardian Phone *</label><input type="tel" id="guardianPhone" required></div></div>
                    <div class="form-row"><div class="form-group"><label>Login Username *</label><input type="text" id="username" required></div><div class="form-group"><label>Password *</label><input type="password" id="password" required></div></div>
                    <button type="submit" class="btn btn-primary" style="width:100%;padding:16px;font-size:16px">Register Student</button>
                </form>
            </div>
        </div>

        <!-- Attendance -->
        <div id="adminAttendanceView" class="content-section">
            <div class="section-card" style="background:linear-gradient(135deg,#1a1a2e,#16213e);color:white;margin-bottom:20px;">
                <div style="text-align:center;margin-bottom:20px;"><div style="font-size:48px;margin-bottom:8px;">📸</div><h2 style="color:white;">QR Attendance Scanner</h2><p style="color:rgba(255,255,255,0.55);">Point camera at student's ID QR code to clock in/out automatically</p></div>
                <div id="scannerViewport" style="display:none;position:relative;max-width:480px;margin:0 auto 16px;border-radius:16px;overflow:hidden;border:3px solid var(--gold);">
                    <video id="scannerVideo" style="width:100%;display:block;border-radius:14px;" autoplay muted playsinline></video>
                    <div style="position:absolute;inset:0;pointer-events:none;">
                        <div style="position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);width:70%;height:60px;border:3px solid var(--gold);border-radius:6px;box-shadow:0 0 0 9999px rgba(0,0,0,0.4);"><div id="scanLine" style="position:absolute;top:0;left:0;right:0;height:3px;background:linear-gradient(90deg,transparent,var(--gold),transparent);animation:scanAnim 1.5s linear infinite;"></div></div>
                    </div>
                </div>
                <div style="display:flex;gap:10px;justify-content:center;flex-wrap:wrap;margin-bottom:16px;">
                    <button id="startScanBtn" class="scan-btn" onclick="startCameraScanner()">📷 Start Camera Scanner</button>
                    <button id="stopScanBtn" class="scan-btn" onclick="stopCameraScanner()" style="display:none;background:linear-gradient(135deg,#555,#333);">⛔ Stop Camera</button>
                    <select id="cameraSelect" style="padding:10px 14px;border-radius:10px;border:none;background:rgba(255,255,255,0.12);color:white;font-size:13px;cursor:pointer;display:none;" onchange="switchCamera()"><option value="">Select Camera</option></select>
                </div>
                <div style="display:flex;align-items:center;gap:12px;margin:14px 0;max-width:480px;margin:14px auto;"><div style="flex:1;height:1px;background:rgba(255,255,255,0.15);"></div><span style="color:rgba(255,255,255,0.4);">or type manually</span><div style="flex:1;height:1px;background:rgba(255,255,255,0.15);"></div></div>
                <div class="scanner-input-row"><input type="text" class="scanner-input" id="barcodeInput" placeholder="Type Reg No. then Enter" onkeydown="if(event.key==='Enter') processBarcode()" oninput="filterAttendanceDropdown()"><button class="scan-btn" onclick="processBarcode()">⚡ Clock In/Out</button></div>
                <div class="scan-result" id="scanResult"></div>
                <div style="margin-top:20px;max-width:600px;margin-left:auto;margin-right:auto;"><p style="font-size:13px;color:rgba(255,255,255,0.4);">— Or tap a student button to clock in/out instantly —</p><div id="quickStudentPicker" style="display:flex;flex-wrap:wrap;gap:8px;max-height:220px;overflow-y:auto;"></div></div>
            </div>
            <div class="section-card">
                <div class="section-header"><h2>Attendance Records</h2><div class="actions"><div class="attendance-tabs"><div class="att-tab active" onclick="filterAttendance('all',this)">All</div><div class="att-tab" onclick="filterAttendance('clocked-in',this)">Present</div><div class="att-tab" onclick="filterAttendance('clocked-out',this)">Completed</div></div><button class="btn btn-outline" onclick="exportAttendance()">📥 Export CSV</button></div></div>
                <div class="search-bar"><input type="text" id="searchAttendance" placeholder="Search..." onkeyup="renderAttendanceTable()"><input type="date" id="attendanceDate" onchange="renderAttendanceTable()"></div>
                <div class="table-responsive"><table><thead><tr><th>Date</th><th>Reg No.</th><th>Name</th><th>Branch</th><th>Clock In</th><th>Clock Out</th><th>Duration</th><th>Status</th></tr></thead><tbody id="attendanceTableBody"></tbody></table></div>
            </div>
        </div>

        <!-- Payments -->
        <div id="adminPaymentsView" class="content-section">
            <div class="section-card">
                <div id="paymentAlertsFull"></div>
                <div class="section-header"><h2>Payment Records</h2><button class="btn btn-primary" onclick="showModal('recordPaymentModal')">+ Record Payment</button></div>
                <div class="stats-grid"><div class="stat-card"><div class="label">Total Collected</div><div class="number" id="totalCollected">₦0</div></div><div class="stat-card"><div class="label">Pending / Partial</div><div class="number" id="pendingTotal" style="color:var(--warning)">₦0</div></div><div class="stat-card"><div class="label">Expired Plans</div><div class="number" id="expiredCount" style="color:var(--danger)">0</div></div><div class="stat-card"><div class="label">Expiring Soon</div><div class="number" id="expiringSoonCount" style="color:var(--warning)">0</div></div></div>
                <h3>Payment Expiry Status</h3><div id="paymentExpiryList" style="margin-bottom:28px"></div>
                <div class="table-responsive"><table><thead><tr><th>Date</th><th>Student</th><th>Reg No.</th><th>Plan</th><th>Amount</th><th>Expiry</th><th>Status</th></tr></thead><tbody id="paymentsTableBody"></tbody></table></div>
            </div>
        </div>

        <!-- Student Tracking -->
        <div id="adminTrackingView" class="content-section">
            <div class="section-card">
                <div class="section-header"><h2>Student Activity Tracking</h2></div>
                <div class="search-bar"><input type="text" id="searchTracking" placeholder="Search student..." onkeyup="renderTracking()"></div>
                <div class="table-responsive"><table><thead><tr><th>Date</th><th>Student</th><th>Reg No.</th><th>Activity</th><th>Subject</th><th>Score</th><th>Status</th></tr></thead><tbody id="trackingTableBody"></tbody></table></div>
            </div>
        </div>

        <!-- Subjects -->
        <div id="adminSubjectsView" class="content-section">
            <div class="section-card"><div class="section-header"><h2>Department Subjects</h2></div><div class="grid-3" id="departmentSubjectsGrid"></div></div>
        </div>

        <!-- Manage Subjects -->
        <div id="adminManageSubjectsView" class="content-section">
            <div class="section-card"><div class="section-header"><h2>Subject Configuration</h2></div><div id="subjectConfigContainer"></div></div>
        </div>

        <!-- CBT -->
        <div id="adminCBTView" class="content-section">
            <div class="section-card"><div class="section-header"><h2>CBT Questions</h2><button class="btn btn-primary" onclick="showModal('cbtQuestionModal')">+ Add Question</button></div><div id="cbtQuestionsContainer"></div></div>
        </div>

        <!-- Tests -->
        <div id="adminTestsView" class="content-section">
            <div class="section-card">
                <div class="section-header"><h2>Tests & Exams</h2><button class="btn btn-primary" onclick="showModal('addTestModal')">+ Create Test</button></div>
                <div class="table-responsive"><table><thead><tr><th>Test Name</th><th>Subject</th><th>Dept</th><th>Date</th><th>Duration</th><th>Questions</th><th>Submissions</th><th>Status</th><th>Actions</th></tr></thead><tbody id="testsTableBody"></tbody></table></div>
            </div>
            <div class="section-card" id="testQuestionPanel" style="display:none">...</div>
            <div class="section-card" id="gradingPanel" style="display:none">...</div>
        </div>

        <!-- Branches -->
        <div id="adminBranchesView" class="content-section">
            <div class="section-card"><div class="section-header"><h2>Our Branches</h2></div>
            <div class="grid-3">
                <div class="branch-card"><h3>Iyana Agbala</h3><p>📍 Opposite Christ Power Junction, Beside Jaagee Building, Iyana Agbala, Ibadan</p><div class="branch-stats"><span><b>Students:</b> <span id="b1count">0</span></span><span>Active ✅</span></div></div>
                <div class="branch-card"><h3>Omi Bridge</h3><p>📍 Iyana Aba Otun Road, Omi Bridge, Alaro Junction, Ibadan</p><div class="branch-stats"><span><b>Students:</b> <span id="b2count">0</span></span><span>Active ✅</span></div></div>
                <div class="branch-card"><h3>Ajameta</h3><p>📍 Ajameta Junction, Aroye, Ibadan</p><div class="branch-stats"><span><b>Students:</b> <span id="b3count">0</span></span><span>Active ✅</span></div></div>
            </div>
        </div>
    </div>
</div>

<!-- STUDENT DASHBOARD -->
<div id="studentDashboard" class="dashboard">
    <div class="sidebar-overlay" id="studentOverlay" onclick="closeSidebar('student')"></div>
    <div class="sidebar" id="studentSidebar">
        <div class="sidebar-header"><div class="sidebar-logo"><div class="brand">TESTIMONY<span>TUTORS</span></div></div><p>Student Portal</p></div>
        <div class="sidebar-menu">
            <div class="menu-item active" onclick="showStudent('overview'); closeSidebar('student')"><span class="icon">📊</span> Overview</div>
            <div class="menu-item" onclick="showStudent('profile'); closeSidebar('student')"><span class="icon">👤</span> My Profile</div>
            <div class="menu-item" onclick="showStudent('idcard'); closeSidebar('student')"><span class="icon">🪪</span> My ID Card</div>
            <div class="menu-item" onclick="showStudent('subjects'); closeSidebar('student')"><span class="icon">📚</span> My Subjects</div>
            <div class="menu-item" onclick="showStudent('tests'); closeSidebar('student')"><span class="icon">📝</span> Tests</div>
            <div class="menu-item" onclick="showStudent('payments'); closeSidebar('student')"><span class="icon">💰</span> Payments</div>
            <div class="menu-item" onclick="showStudent('cbt'); closeSidebar('student')"><span class="icon">💻</span> CBT Practice</div>
        </div>
    </div>
    <div class="main-content">
        <div class="top-bar">
            <div class="hamburger" onclick="toggleSidebar('student')"><span></span><span></span><span></span></div>
            <div class="welcome-text"><h3>Welcome, <span id="studentNameDisplay">Student</span></h3><p id="studentInfoLine">Your learning portal</p></div>
            <div class="top-right"><div class="user-avatar" id="studentAvatarTop">ST</div><button class="logout-btn" onclick="logout()">Logout</button></div>
        </div>
        <div id="studentOverview" class="content-section active"><div class="stats-grid" id="studentStatsGrid"></div><div class="section-card"><h2>Recent Activities</h2><table><thead><tr><th>Date</th><th>Activity</th><th>Subject</th><th>Score</th><th>Status</th></tr></thead><tbody id="studentActivitiesTable"></tbody></table></div></div>
        <div id="studentProfile" class="content-section"><div class="section-card" id="studentProfileContent"></div></div>
        <div id="studentIDCardSection" class="content-section"><div class="section-card"><div class="section-header"><h2>My ID Card</h2></div><div id="idCardContainer"></div><button class="btn btn-primary" onclick="printIDCard()">🖨️ Print</button></div></div>
        <div id="studentSubjectsSection" class="content-section"><div class="section-card"><h2>My Subjects</h2><div class="subjects-grid" id="studentSubjectsGrid"></div></div></div>
        <div id="studentTestsSection" class="content-section"><div class="section-card"><h2>Tests & Examinations</h2><table><thead><tr><th>Test Name</th><th>Subject</th><th>Date</th><th>Duration</th><th>Questions</th><th>Status</th><th>Action</th></tr></thead><tbody id="studentTestsList"></tbody></table></div></div>
        <div id="studentTestInterface" class="content-section">...</div>
        <div id="studentTestResultInterface" class="content-section">...</div>
        <div id="studentPostedResults" class="content-section">...</div>
        <div id="studentPaymentsSection" class="content-section"><div class="section-card"><h2>Payment History</h2><div id="studentPaymentStatus"></div><table><thead><tr><th>Date</th><th>Plan</th><th>Amount</th><th>Expiry</th><th>Status</th></tr></thead><tbody id="studentPaymentsList"></tbody></table></div></div>
        <div id="studentCBTSection" class="content-section"><div class="section-card"><h2>CBT Practice Center</h2><div id="cbtSubjectsList"></div></div></div>
        <div id="cbtPracticeInterface" class="content-section">...</div>
        <div id="cbtResultsInterface" class="content-section">...</div>
    </div>
</div>

<!-- Modals (abbreviated) -->
<div id="viewStudentModal" class="modal"><div class="modal-content"><div class="modal-header"><h3>Student Details</h3><span class="close-modal" onclick="closeModal('viewStudentModal')">×</span></div><div id="viewStudentContent"></div></div></div>
<div id="editStudentModal" class="modal">...</div>
<div id="recordPaymentModal" class="modal">...</div>
<div id="cbtQuestionModal" class="modal">...</div>
<div id="addTestModal" class="modal">...</div>

<script>
// =================== API CONFIGURATION ===================
const BASE_URL = 'http://localhost:3000/api';
let AUTH_TOKEN = sessionStorage.getItem('token') || null;

const api = {
    headers() { const h = {'Content-Type':'application/json'}; if(AUTH_TOKEN) h['Authorization'] = `Bearer ${AUTH_TOKEN}`; return h; },
    async get(url) { const res = await fetch(BASE_URL+url,{headers:this.headers()}); if(!res.ok) throw new Error(await res.text()); return res.json(); },
    async post(url,data) { const res = await fetch(BASE_URL+url,{method:'POST',headers:this.headers(),body:JSON.stringify(data)}); if(!res.ok) throw new Error(await res.text()); return res.json(); },
    async put(url,data) { const res = await fetch(BASE_URL+url,{method:'PUT',headers:this.headers(),body:JSON.stringify(data)}); if(!res.ok) throw new Error(await res.text()); return res.json(); },
    async del(url) { const res = await fetch(BASE_URL+url,{method:'DELETE',headers:this.headers()}); if(!res.ok) throw new Error(await res.text()); return res.json(); }
};

// =================== HELPERS ===================
const PLANS = { full: { amount:10000, days:30, label:'Full — 30 days' }, half: { amount:5000, days:14, label:'Half — 14 days' } };
function fmtDate(d) { return d.toISOString().split('T')[0]; }
function getExpiryDate(s) { if(!s.paymentDate||!s.paymentPlan) return null; const p = PLANS[s.paymentPlan]; if(!p) return null; const d=new Date(s.paymentDate+'T00:00:00'); if(isNaN(d.getTime())) return null; d.setDate(d.getDate()+p.days); return d; }
function getDaysLeft(s) { const exp=getExpiryDate(s); if(!exp) return null; const n=new Date(); n.setHours(0,0,0,0); return Math.ceil((exp-n)/(1000*60*60*24)); }
function getPaymentStatusBadge(s) { const d=getDaysLeft(s); if(d===null) return '<span class="badge badge-gray">No Payment</span>'; if(d<0) return '<span class="badge badge-red">Expired '+Math.abs(d)+'d ago</span>'; if(d<=5) return '<span class="badge badge-yellow">Expiring in '+d+'d</span>'; return '<span class="badge badge-green">Active ('+d+'d left)</span>'; }

// =================== AUTH ===================
function switchTab(t) {
    document.querySelectorAll('.login-tab').forEach((tab,i)=>{ tab.classList.toggle('active', (t==='student'&&i===0)||(t==='admin'&&i===1)); });
    document.getElementById('studentLogin').classList.toggle('active', t==='student');
    document.getElementById('adminLogin').classList.toggle('active', t==='admin');
}
async function handleStudentLogin(e) {
    e.preventDefault();
    const regNo = document.getElementById('studentRegNo').value.trim();
    const password = document.getElementById('studentPassword').value;
    try {
        const data = await api.post('/auth/student/login', { regNo, password });
        AUTH_TOKEN = data.token; sessionStorage.setItem('token', data.token);
        window.currentStudent = data.student;
        document.getElementById('loginContainer').style.display = 'none';
        document.getElementById('studentDashboard').classList.add('active');
        loadStudentDashboard();
    } catch(err) { alert('Login failed: ' + err.message); }
}
async function handleAdminLogin(e) {
    e.preventDefault();
    const username = document.getElementById('adminId').value;
    const password = document.getElementById('adminPassword').value;
    try {
        const data = await api.post('/auth/admin/login', { username, password });
        AUTH_TOKEN = data.token; sessionStorage.setItem('token', data.token);
        document.getElementById('loginContainer').style.display = 'none';
        document.getElementById('adminDashboard').classList.add('active');
        loadAdminDashboard();
    } catch(err) { alert('Login failed: ' + err.message); }
}
function logout() {
    AUTH_TOKEN = null; sessionStorage.removeItem('token'); window.currentStudent = null;
    document.getElementById('loginContainer').style.display = 'flex';
    document.getElementById('studentDashboard').classList.remove('active');
    document.getElementById('adminDashboard').classList.remove('active');
}

// =================== ADMIN DASHBOARD ===================
async function loadAdminDashboard() {
    document.getElementById('adminDateLine').textContent = new Date().toDateString();
    await Promise.all([renderAdminStats(), renderRecentStudents(), renderBranchCounts()]);
    updateAlertBadges();
}
async function renderAdminStats() {
    const [students, payments, attendance] = await Promise.all([
        api.get('/students').catch(()=>[]), api.get('/payments').catch(()=>[]), api.get('/attendance').catch(()=>[])
    ]);
    const today = fmtDate(new Date());
    const present = attendance.filter(a=>a.date===today && (a.status==='clocked-in'||a.clockOut)).length;
    const collected = payments.reduce((s,p)=>s+(p.amount||0),0);
    document.getElementById('adminStatsGrid').innerHTML = `
        <div class="stat-card" onclick="showAdmin('students')"><div class="label">Total Students</div><div class="number">${students.length}</div></div>
        <div class="stat-card" onclick="showAdmin('attendance')"><div class="label">Present Today</div><div class="number" style="color:var(--success)">${present}</div></div>
        <div class="stat-card" onclick="showAdmin('payments')"><div class="label">Total Collected</div><div class="number">₦${(collected/1000).toFixed(0)}k</div></div>
        <div class="stat-card"><div class="label">Payment Alerts</div><div class="number" id="alertCount">0</div></div>`;
}
async function renderRecentStudents() {
    const students = await api.get('/students').catch(()=>[]);
    const recent = students.slice(-5).reverse();
    document.getElementById('recentStudentsTable').innerHTML = recent.map(s=>`<tr>
        <td><code>${s.regNo}</code></td><td><b>${s.fullName}</b></td><td>${s.department}</td><td>${s.branch}</td>
        <td>${getPaymentStatusBadge(s)}</td><td><span class="badge badge-green">${s.status}</span></td>
        <td><button class="action-btn view-btn" onclick="viewStudent('${s.id}')">View</button></td>
    </tr>`).join('');
}
async function renderBranchCounts() {
    const students = await api.get('/students').catch(()=>[]);
    document.getElementById('b1count').textContent = students.filter(s=>s.branch==='Iyana Agbala').length;
    document.getElementById('b2count').textContent = students.filter(s=>s.branch==='Omi Bridge').length;
    document.getElementById('b3count').textContent = students.filter(s=>s.branch==='Ajameta').length;
}
async function updateAlertBadges() {
    const students = await api.get('/students').catch(()=>[]);
    const alerts = students.filter(s=>{ const d=getDaysLeft(s); return d!==null && d<=5; });
    document.getElementById('paymentAlertBadge').textContent = alerts.length;
    document.getElementById('topAlertBadge').classList.toggle('visible', alerts.length>0);
    document.getElementById('topAlertCount').textContent = alerts.length;
    document.getElementById('alertCount').textContent = alerts.length;
}

// =================== STUDENTS TABLE ===================
async function renderStudentsTable() {
    const students = await api.get('/students').catch(()=>[]);
    const tbody = document.getElementById('studentsTableBody');
    tbody.innerHTML = students.map(s=>{
        const days = getDaysLeft(s);
        const daysDisplay = days===null?'—':days<0?`<span style="color:var(--danger)">Exp. ${Math.abs(days)}d ago</span>`:`<span style="color:var(--success)">${days}d</span>`;
        return `<tr>
            <td><code>${s.regNo}</code></td><td><b>${s.fullName}</b></td><td><span class="badge badge-blue">${s.department}</span></td><td>${s.branch}</td>
            <td>${getPaymentStatusBadge(s)}</td><td>${daysDisplay}</td><td><span class="badge ${s.status==='active'?'badge-green':'badge-red'}">${s.status}</span></td>
            <td class="action-btns">
                <button class="action-btn view-btn" onclick="viewStudent('${s.id}')">View</button>
                <button class="action-btn edit-btn" onclick="editStudent('${s.regNo}')">Edit</button>
                <button class="action-btn id-btn" onclick="adminViewIDCard('${s.regNo}')">ID</button>
                <button class="action-btn pay-btn" onclick="quickRecordPayment('${s.regNo}')">Pay</button>
                <button class="action-btn delete-btn" onclick="deleteStudent('${s.regNo}')">Del</button>
            </td></tr>`;
    }).join('');
}
async function searchStudents() {
    const q = document.getElementById('searchStudent').value.toLowerCase();
    const students = await api.get('/students').catch(()=>[]);
    const filtered = students.filter(s=>s.fullName.toLowerCase().includes(q)||s.regNo.toLowerCase().includes(q)||s.branch.toLowerCase().includes(q));
    renderStudentsTableFromData(filtered);
}
function renderStudentsTableFromData(data) {
    const tbody = document.getElementById('studentsTableBody');
    tbody.innerHTML = data.map(s=>`<tr><td><code>${s.regNo}</code></td><td><b>${s.fullName}</b></td><td>${s.department}</td><td>${s.branch}</td><td>${getPaymentStatusBadge(s)}</td><td>${getDaysLeft(s)}d</td><td><span class="badge badge-green">${s.status}</span></td></tr>`).join('');
}
function filterStudentsByBranch(branch) {
    api.get('/students').then(students=>{
        const filtered = branch ? students.filter(s=>s.branch===branch) : students;
        renderStudentsTableFromData(filtered);
    });
}

// =================== ADD/EDIT/DELETE STUDENT ===================
function updatePaymentFields() {
    const plan = document.getElementById('paymentPlan').value;
    document.getElementById('amountPaid').value = plan ? PLANS[plan].amount : 0;
    document.getElementById('paymentSummary').innerHTML = plan ? `<div class="note">${PLANS[plan].label}</div>` : '';
}
async function addStudent(e) {
    e.preventDefault();
    const form = document.getElementById('addStudentForm');
    const formData = new FormData(form);
    const data = Object.fromEntries(formData.entries());
    try {
        const res = await api.post('/students', data);
        alert('✅ Student registered! Reg No: ' + res.regNo);
        form.reset();
        document.getElementById('paymentSummary').innerHTML = '';
        showAdmin('students');
    } catch(err) { alert('Error: ' + err.message); }
}
async function viewStudent(id) {
    const students = await api.get('/students');
    const s = students.find(st=>st.id===id);
    if(!s) return;
    document.getElementById('viewStudentContent').innerHTML = `<div><b>Name:</b> ${s.fullName}</div><div><b>Reg No:</b> ${s.regNo}</div><div><b>Branch:</b> ${s.branch}</div><div><b>Dept:</b> ${s.department}</div><div><b>Phone:</b> ${s.phone}</div><div><b>Payment:</b> ${getPaymentStatusBadge(s)}</div>`;
    showModal('viewStudentModal');
}
async function editStudent(regNo) {
    const students = await api.get('/students');
    const s = students.find(st=>st.regNo===regNo);
    if(!s) return;
    document.getElementById('editStudentId').value = regNo;
    document.getElementById('editFirstName').value = s.firstName;
    document.getElementById('editLastName').value = s.lastName;
    document.getElementById('editEmail').value = s.email;
    document.getElementById('editPhone').value = s.phone;
    document.getElementById('editBranch').value = s.branch;
    document.getElementById('editDepartment').value = s.department;
    document.getElementById('editPaymentPlan').value = s.paymentPlan||'full';
    document.getElementById('editPaymentDate').value = s.paymentDate||'';
    showModal('editStudentModal');
}
async function updateStudent(e) {
    e.preventDefault();
    const regNo = document.getElementById('editStudentId').value;
    const data = {
        firstName: document.getElementById('editFirstName').value,
        lastName: document.getElementById('editLastName').value,
        email: document.getElementById('editEmail').value,
        phone: document.getElementById('editPhone').value,
        branch: document.getElementById('editBranch').value,
        department: document.getElementById('editDepartment').value,
        paymentPlan: document.getElementById('editPaymentPlan').value,
        paymentDate: document.getElementById('editPaymentDate').value
    };
    try {
        await api.put('/students/'+regNo, data);
        closeModal('editStudentModal');
        renderStudentsTable();
        alert('✅ Student updated');
    } catch(err) { alert('Error: '+err.message); }
}
async function deleteStudent(regNo) {
    if(!confirm('Delete this student?')) return;
    try {
        await api.del('/students/'+regNo);
        renderStudentsTable();
    } catch(err) { alert('Error: '+err.message); }
}

// =================== ID CARD ===================
function adminViewIDCard(regNo) {
    api.get('/students').then(students=>{
        const s = students.find(st=>st.regNo===regNo);
        if(s) {
            window.currentStudent = s;
            showStudent('idcard');
            document.getElementById('adminDashboard').classList.remove('active');
            document.getElementById('studentDashboard').classList.add('active');
            loadStudentDashboard();
            showStudent('idcard');
        }
    });
}
function renderIDCard(s) {
    const container = document.getElementById('idCardContainer');
    const initials = (s.firstName[0]+s.lastName[0]).toUpperCase();
    const qrValue = s.regNo.replace(/[^A-Z0-9]/g,'');
    const svgId = 'idCardQRCode';
    container.innerHTML = `<div class="id-card-wrap"><div class="id-card" id="printArea"><div class="id-card-top"></div><div class="id-card-body"><div class="id-card-header"><h2>TESTIMONY TUTORS</h2><p>Excellence in Learning</p></div><div class="id-card-photo">${initials}</div><div class="id-card-detail"><span>Name:</span> <b>${s.fullName}</b></div><div class="id-card-detail"><span>Reg No:</span> ${s.regNo}</div><div class="id-card-detail"><span>Dept:</span> ${s.department}</div><div class="id-card-detail"><span>Branch:</span> ${s.branch}</div></div><div id="${svgId}" style="padding:10px;background:white;text-align:center;"></div></div></div>`;
    setTimeout(()=>{
        const qr = document.getElementById(svgId);
        if(qr) { qr.innerHTML=''; new QRCode(qr,{text:qrValue,width:110,height:110,colorDark:"#1a1a2e",colorLight:"#ffffff",correctLevel:QRCode.CorrectLevel.H}); }
    },200);
}
function printIDCard() { window.print(); }

// =================== ATTENDANCE ===================
async function processBarcode(val) {
    const input = document.getElementById('barcodeInput');
    const regNo = val || input.value.trim().toUpperCase();
    const res = document.getElementById('scanResult');
    if(!regNo) return;
    try {
        const data = await api.post('/attendance/clock',{regNo});
        res.className = 'scan-result success'; res.style.display='block'; res.innerHTML = data.message;
        input.value='';
        renderAttendanceTable(); renderQuickStudentPicker(); updateAlertBadges();
        const todayAtt = await api.get('/attendance?date='+fmtDate(new Date()));
        document.getElementById('attendanceBadge').textContent = todayAtt.length;
    } catch(err) {
        res.className = 'scan-result error'; res.style.display='block'; res.innerHTML = '❌ '+err.message;
        input.value='';
    }
}
async function renderAttendanceTable() {
    const attendance = await api.get('/attendance').catch(()=>[]);
    document.getElementById('attendanceTableBody').innerHTML = attendance.map(a=>`<tr>
        <td>${a.date}</td><td>${a.regNo}</td><td>${a.name}</td><td>${a.branch}</td>
        <td>${a.clockIn}</td><td>${a.clockOut||'—'}</td><td>${a.duration||'—'}</td>
        <td><span class="badge ${a.status==='clocked-in'?'badge-yellow':'badge-green'}">${a.status==='clocked-in'?'Present':'Complete'}</span></td>
    </tr>`).join('');
}
function filterAttendance(type,el) { /* implement if needed */ }

// =================== PAYMENTS ===================
async function renderPaymentsSection() {
    const [payments, students] = await Promise.all([api.get('/payments').catch(()=>[]), api.get('/students').catch(()=>[])]);
    document.getElementById('totalCollected').textContent = '₦'+payments.reduce((s,p)=>s+(p.amount||0),0).toLocaleString();
    document.getElementById('paymentsTableBody').innerHTML = payments.map(p=>`<tr>
        <td>${p.date}</td><td>${p.student}</td><td>${p.regNo}</td><td>${p.plan}</td><td>₦${(p.amount||0).toLocaleString()}</td><td>${p.date?getExpiryDate({paymentDate:p.date,paymentPlan:p.plan})?.toDateString():'—'}</td><td><span class="badge badge-green">Paid</span></td>
    </tr>`).join('');
}
async function recordPayment(e) {
    e.preventDefault();
    const regNo = document.getElementById('paymentStudentId').value.trim();
    const plan = document.getElementById('paymentPlanModal').value;
    const date = document.getElementById('paymentRecordDate').value;
    try {
        await api.post('/payments',{regNo,plan,date});
        closeModal('recordPaymentModal');
        renderPaymentsSection();
        alert('✅ Payment recorded');
    } catch(err) { alert('Error: '+err.message); }
}
function setPaymentAmount() { document.getElementById('paymentAmountModal').value = document.getElementById('paymentPlanModal').value==='full'?10000:5000; }
function quickRecordPayment(regNo) { document.getElementById('paymentStudentId').value=regNo; showModal('recordPaymentModal'); }

// =================== TRACKING ===================
async function renderTracking() {
    const search = document.getElementById('searchTracking')?.value || '';
    const activities = await api.get('/tracking?search='+encodeURIComponent(search)).catch(()=>[]);
    document.getElementById('trackingTableBody').innerHTML = activities.map(a=>`<tr>
        <td>${a.date}</td><td><b>${a.student}</b></td><td><code>${a.regNo}</code></td>
        <td>${a.activity}</td><td>${a.subject}</td><td>${a.score}</td><td>${a.status}</td>
    </tr>`).join('');
}

// =================== SUBJECTS ===================
async function renderSubjectConfig() {
    const config = await api.get('/subjects/config').catch(()=>({}));
    let html = '';
    ['HUMANITIES','BUSINESS','SCIENCES'].forEach(dept=>{
        const s = config[dept]||{compulsory:[],electives:[]};
        html += `<div style="margin-bottom:20px"><h3>${dept}</h3>
        <input type="text" id="comp_${dept}" value="${s.compulsory.join(',')}" style="width:100%;padding:10px;border-radius:8px;margin-bottom:8px" placeholder="Compulsory (comma separated)">
        <input type="text" id="elect_${dept}" value="${s.electives.join(',')}" style="width:100%;padding:10px;border-radius:8px" placeholder="Electives (comma separated)"></div>`;
    });
    html += '<button class="btn btn-primary" onclick="saveSubjectConfig()">💾 Save Subjects</button>';
    document.getElementById('subjectConfigContainer').innerHTML = html;
}
async function saveSubjectConfig() {
    const config = {};
    ['HUMANITIES','BUSINESS','SCIENCES'].forEach(dept=>{
        config[dept] = {
            compulsory: document.getElementById('comp_'+dept).value.split(',').map(s=>s.trim()).filter(s=>s),
            electives: document.getElementById('elect_'+dept).value.split(',').map(s=>s.trim()).filter(s=>s)
        };
    });
    await api.put('/subjects/config', config);
    alert('✅ Subjects updated');
}

// =================== CBT ===================
async function renderCBTQuestions() {
    const grouped = await api.get('/cbt/questions').catch(()=>({}));
    let html = '';
    for(const subject in grouped) {
        html += `<h3>${subject} (${grouped[subject].length})</h3><table><tr><th>Question</th><th>Options</th><th></th></tr>`;
        grouped[subject].forEach(q=>{
            html += `<tr><td>${q.question}</td><td>${q.options.map((o,i)=>String.fromCharCode(65+i)+': '+o).join(' | ')}</td><td><button class="action-btn delete-btn" onclick="deleteCBTQuestion('${q.id}')">Del</button></td></tr>`;
        });
        html += '</table>';
    }
    document.getElementById('cbtQuestionsContainer').innerHTML = html || '<div class="empty-state">No questions</div>';
}
async function saveCBTQuestion(e) {
    e.preventDefault();
    const data = {
        subject: document.getElementById('cbtQSubject').value,
        question: document.getElementById('cbtQText').value,
        options: [document.getElementById('cbtQA').value, document.getElementById('cbtQB').value, document.getElementById('cbtQC').value, document.getElementById('cbtQD').value],
        correct: parseInt(document.getElementById('cbtQCorrect').value),
        explanation: document.getElementById('cbtQExplain').value
    };
    await api.post('/cbt/questions', data);
    closeModal('cbtQuestionModal');
    renderCBTQuestions();
}
async function deleteCBTQuestion(id) { if(confirm('Delete?')) { await api.del('/cbt/questions/'+id); renderCBTQuestions(); } }

// =================== TESTS (ADMIN) ===================
async function renderTestsTable() {
    const tests = await api.get('/tests').catch(()=>[]);
    const tq = await api.get('/tests/questions').catch(()=>({}));
    const tbody = document.getElementById('testsTableBody');
    tbody.innerHTML = tests.map(t=>`<tr><td>${t.name}</td><td>${t.subject}</td><td>${t.department}</td><td>${t.date}</td><td>${t.duration}min</td><td>?</td><td>?</td><td><span class="badge ${t.status==='Active'?'badge-green':'badge-yellow'}">${t.status}</span></td><td><button class="action-btn edit-btn" onclick="openTestQuestionPanel('${t.id}')">Questions</button></td></tr>`).join('');
}
async function addTest(e) { /* ... */ }

// =================== SECTION DISPATCH ===================
function showAdmin(section) {
    const map = { dashboard:'adminDashboardView', students:'adminStudentsView', addStudent:'adminAddStudentView', attendance:'adminAttendanceView', payments:'adminPaymentsView', tracking:'adminTrackingView', subjects:'adminSubjectsView', manageSubjects:'adminManageSubjectsView', cbt:'adminCBTView', tests:'adminTestsView', branches:'adminBranchesView' };
    document.querySelectorAll('#adminDashboard .content-section').forEach(s=>s.classList.remove('active'));
    document.getElementById(map[section]).classList.add('active');
    if(section==='students') renderStudentsTable();
    if(section==='attendance') { renderAttendanceTable(); renderQuickStudentPicker(); }
    if(section==='payments') renderPaymentsSection();
    if(section==='tracking') renderTracking();
    if(section==='cbt') renderCBTQuestions();
    if(section==='manageSubjects') renderSubjectConfig();
    if(section==='tests') renderTestsTable();
}

// =================== MOBILE SIDEBAR TOGGLE ===================
function toggleSidebar(role) {
    const sidebarId = role === 'admin' ? 'adminSidebar' : 'studentSidebar';
    const overlayId = role === 'admin' ? 'adminOverlay' : 'studentOverlay';
    const sidebar = document.getElementById(sidebarId);
    const overlay = document.getElementById(overlayId);
    sidebar.classList.toggle('open');
    overlay.classList.toggle('active');
}
function closeSidebar(role) {
    const sidebarId = role === 'admin' ? 'adminSidebar' : 'studentSidebar';
    const overlayId = role === 'admin' ? 'adminOverlay' : 'studentOverlay';
    const sidebar = document.getElementById(sidebarId);
    const overlay = document.getElementById(overlayId);
    sidebar.classList.remove('open');
    overlay.classList.remove('active');
}

// =================== INIT ===================
document.getElementById('adminDateLine').textContent = new Date().toDateString();
</script>
</body>
</html>
