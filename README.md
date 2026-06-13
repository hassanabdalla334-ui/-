<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>ميزانيتي V4 - صوت وإشعارات</title>
    <meta name="theme-color" content="#0a0a0a">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: #0a0a0a;
            color: #e0d4b0;
            padding-bottom: 80px;
            min-height: 100vh;
            position: relative;
        }
        .glass {
            background: rgba(20, 20, 20, 0.75);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(212, 175, 55, 0.2);
            border-radius: 20px;
        }
        header { 
            padding: 10px 16px 6px; 
            background: rgba(10,10,10,0.9); 
            position: sticky; 
            top: 0; 
            z-index: 500; 
            backdrop-filter: blur(10px);
        }
        .header-top {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .header-top h1 { font-size: 20px; color: #d4af37; }
        .clock-section {
            display: flex; align-items: center; gap: 10px;
            background: rgba(0,0,0,0.4); padding: 4px 12px; border-radius: 30px; border: 1px solid rgba(212, 175, 55, 0.15);
        }
        .analog-clock {
            width: 48px; height: 48px; border-radius: 50%; background: #1a1a1a; border: 2px solid #d4af37; position: relative;
        }
        .analog-clock .hand {
            position: absolute; bottom: 50%; left: 50%; transform-origin: bottom; border-radius: 4px;
        }
        .analog-clock .hour-hand { width: 3px; height: 13px; background: #f0e6c8; margin-left: -1.5px; }
        .analog-clock .minute-hand { width: 2px; height: 18px; background: #d4af37; margin-left: -1px; }
        .analog-clock .second-hand { width: 1px; height: 20px; background: #e74c3c; margin-left: -0.5px; }
        .analog-clock .center { position: absolute; width: 4px; height: 4px; background: #d4af37; border-radius: 50%; top: 50%; left: 50%; transform: translate(-50%, -50%); }
        .digital-clock { display: flex; flex-direction: column; text-align: right; line-height: 1.2; }
        .digital-clock .time { font-size: 16px; font-weight: 600; color: #d4af37; }
        .digital-clock .day { font-size: 11px; color: #d4af37; }
        .digital-clock .date { font-size: 10px; color: #aaa; }
        .tab-bar {
            position: fixed; bottom: 0; left: 0; right: 0;
            background: rgba(10,10,10,0.95); backdrop-filter: blur(16px);
            border-top: 1px solid rgba(212,175,55,0.15);
            display: flex; justify-content: space-around; padding: 6px 0; z-index: 1000;
        }
        .tab-btn {
            background: none; border: none; color: #888; font-size: 10px;
            display: flex; flex-direction: column; align-items: center; gap: 2px; cursor: pointer;
            transition: color 0.3s; font-family: inherit; flex: 1;
        }
        .tab-btn .tab-icon { font-size: 18px; }
        .tab-btn.active { color: #d4af37; }
        .tab-btn.active .tab-icon { text-shadow: 0 0 12px rgba(212, 175, 55, 0.5); }

        .page { display: none; padding: 10px 14px 18px; max-width: 480px; margin: 0 auto; }
        .page.active { display: block; }

        .dashboard-grid {
            display: grid; grid-template-columns: 1fr 1fr; gap: 8px; margin-top: 8px;
        }
        .stat-card {
            padding: 12px 8px; text-align: center;
            background: rgba(20,20,20,0.6); border: 1px solid rgba(212, 175, 55, 0.15); border-radius: 14px;
        }
        .stat-card .label { font-size: 10px; color: #aaa; }
        .stat-card .value { font-size: 18px; font-weight: 700; color: #f0e6c8; }
        .stat-card .value.gold { color: #d4af37; }

        .full-width-card {
            grid-column: 1 / -1; padding: 10px 14px;
            background: rgba(20,20,20,0.6); border: 1px solid rgba(212, 175, 55, 0.15); border-radius: 14px; margin-top: 4px;
        }
        .full-width-card .title { font-size: 12px; font-weight: 600; color: #d4af37; display: flex; align-items: center; gap: 6px; }

        .form-group { margin-bottom: 8px; }
        .form-group label { display: block; font-size: 11px; color: #bbb; margin-bottom: 2px; }
        .form-group input, .form-group select {
            width: 100%; padding: 8px 10px; background: rgba(30,30,30,0.8);
            border: 1px solid rgba(212, 175, 55, 0.25); border-radius: 10px; color: #f0e6c8; font-size: 13px;
            font-family: inherit;
        }
        .btn {
            padding: 6px 16px; border: none; border-radius: 10px; font-size: 13px; font-weight: 600; cursor: pointer;
            transition: all 0.3s; font-family: inherit;
        }
        .btn-primary { background: #d4af37; color: #0a0a0a; }
        .btn-danger { background: #c0392b; color: #fff; }
        .btn-secondary { background: #2c2c2c; color: #ddd; }
        .btn-sm { padding: 3px 10px; font-size: 11px; border-radius: 6px; }
        .btn-group { display: flex; gap: 6px; flex-wrap: wrap; margin-top: 4px; }

        .list-item {
            display: flex; justify-content: space-between; align-items: center;
            padding: 8px 10px; background: rgba(30,30,30,0.5); border-radius: 10px; margin-bottom: 4px;
        }
        .list-item .info .main { font-size: 13px; color: #f0e6c8; }
        .list-item .info .sub { font-size: 10px; color: #888; }
        .list-item .actions { display: flex; gap: 4px; flex-wrap: wrap; }

        .treasury-balance {
            text-align: center; padding: 16px; font-size: 28px; font-weight: 700; color: #d4af37;
            background: rgba(20,20,20,0.4); border-radius: 18px; border: 1px solid rgba(212, 175, 55, 0.2); margin-bottom: 12px;
        }
        .report-card {
            background: rgba(20,20,20,0.5); border-radius: 14px; padding: 10px 12px; margin-bottom: 8px;
        }
        .report-card .row { display: flex; justify-content: space-between; padding: 2px 0; font-size: 12px; }
        .report-card .row .label { color: #aaa; }
        .report-card .row .value { color: #f0e6c8; }

        .switch {
            position: relative; width: 40px; height: 22px; background: #333; border-radius: 11px; cursor: pointer;
            display: inline-block;
        }
        .switch.active { background: #d4af37; }
        .switch .slider { position: absolute; top: 2px; left: 2px; width: 18px; height: 18px; background: #fff; border-radius: 50%; transition: transform 0.3s; }
        .switch.active .slider { transform: translateX(18px); }

        .modal-overlay {
            position: fixed; top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0,0,0,0.7); backdrop-filter: blur(6px);
            display: none; justify-content: center; align-items: center; padding: 14px; z-index: 2000;
        }
        .modal-overlay.show { display: flex; }
        .modal-box {
            background: #1a1a1a; border-radius: 20px; padding: 16px 14px; max-width: 380px; width: 100%;
            border: 1px solid rgba(212, 175, 55, 0.2); max-height: 85vh; overflow-y: auto;
        }
        .modal-box h2 { color: #d4af37; margin-bottom: 10px; font-size: 16px; }
        .scrollable-list { max-height: 260px; overflow-y: auto; padding-right: 4px; }
        .empty-state { text-align: center; padding: 20px 10px; color: #666; font-size: 12px; }
        .empty-state .icon { font-size: 32px; }
        .backup-row { display: flex; gap: 6px; flex-wrap: wrap; margin-top: 4px; }
        .backup-row .btn { flex: 1; min-width: 60px; }
        
        /* Toast Notification System */
        #toast-container {
            position: fixed; top: 60px; right: 10px; left: 10px; z-index: 9999;
            display: flex; flex-direction: column; gap: 6px; pointer-events: none;
            max-width: 400px; margin: 0 auto;
        }
        .toast {
            background: #1a1a1a; border: 1px solid #d4af37; border-radius: 12px; padding: 10px 14px;
            color: #f0e6c8; font-size: 12px; box-shadow: 0 4px 20px rgba(0,0,0,0.6);
            animation: slideDown 0.3s ease;
            pointer-events: auto;
            backdrop-filter: blur(10px);
            display: flex; align-items: center; gap: 8px;
        }
        .toast.warning { border-color: #f39c12; }
        .toast.success { border-color: #27ae60; }
        .toast.info { border-color: #3498db; }
        @keyframes slideDown { from { opacity: 0; transform: translateY(-20px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body>

<header>
    <div class="header-top">
        <h1>👑 ميزانيتي</h1>
        <div class="clock-section">
            <div class="analog-clock" id="analogClock">
                <div class="hand hour-hand" id="hourHand"></div>
                <div class="hand minute-hand" id="minuteHand"></div>
                <div class="hand second-hand" id="secondHand"></div>
                <div class="center"></div>
            </div>
            <div class="digital-clock">
                <div class="time" id="digitalTime">00:00:00</div>
                <div class="day" id="digitalDay">الاثنين</div>
                <div class="date" id="digitalDate">2026-06-13</div>
            </div>
        </div>
    </div>
</header>

<!-- Container for Toast Notifications -->
<div id="toast-container"></div>

<!-- 6 صفحات -->
<section id="page-home" class="page active">
    <div class="dashboard-grid">
        <div class="stat-card"><div class="label">💰 الراتب</div><div class="value gold" id="dashSalary">0</div></div>
        <div class="stat-card"><div class="label">📋 الالتزامات</div><div class="value" id="dashCommitments">0</div></div>
        <div class="stat-card"><div class="label">💸 المصروفات</div><div class="value" id="dashExpenses">0</div></div>
        <div class="stat-card"><div class="label">🏦 الخزنة</div><div class="value gold" id="dashTreasury">0</div></div>
        <div class="stat-card"><div class="label">💵 المتبقي</div><div class="value" id="dashRemaining">0</div></div>
        <div class="stat-card"><div class="label">📈 نسبة الصرف</div><div class="value" id="dashSpentPercent">0%</div></div>
        <div class="stat-card"><div class="label">📉 نسبة التوفير</div><div class="value" id="dashSavedPercent">0%</div></div>
        <div class="stat-card"><div class="label">🔮 توقع نهاية الشهر</div><div class="value" id="dashForecast">0</div></div>
    </div>
    <div class="full-width-card"><div class="title">🤖 الذكاء المالي</div><div id="insightsContainer">جاري تحليل البيانات...</div></div>
    <div class="full-width-card"><div class="title">🔄 التحويل التلقائي</div><div id="autoTransferInfo">يتم تحويل المتبقي في نهاية كل شهر</div></div>
</section>

<section id="page-expenses" class="page">
    <h2 style="font-size:16px; color:#d4af37; margin-bottom:8px;">💸 المصروفات</h2>
    <div style="background:rgba(20,20,20,0.6); padding:12px; border-radius:14px; margin-bottom:12px;">
        <div style="display:flex; gap:6px; flex-wrap:wrap;">
            <input type="number" id="expAmount" placeholder="المبلغ" style="flex:2; min-width:70px; padding:8px; background:rgba(30,30,30,0.8); border:1px solid #d4af37; border-radius:10px; color:#f0e6c8;">
            <select id="expCategory" style="flex:1; min-width:60px; padding:8px; background:rgba(30,30,30,0.8); border:1px solid #d4af37; border-radius:10px; color:#f0e6c8;">
                <option value="🍔 أكل">🍔 أكل</option>
                <option value="⛽ بنزين">⛽ بنزين</option>
                <option value="🛒 تسوق">🛒 تسوق</option>
                <option value="📦 أخرى">📦 أخرى</option>
            </select>
        </div>
        <div style="display:flex; gap:6px; margin-top:6px;">
            <input type="text" id="expNote" placeholder="ملاحظة" style="flex:2; padding:8px; background:rgba(30,30,30,0.8); border:1px solid #d4af37; border-radius:10px; color:#f0e6c8;">
            <input type="date" id="expDate" style="flex:1; padding:8px; background:rgba(30,30,30,0.8); border:1px solid #d4af37; border-radius:10px; color:#f0e6c8;">
        </div>
        <button class="btn btn-primary" onclick="addExpense()" style="width:100%; margin-top:6px;">➕ إضافة</button>
    </div>
    <div id="expenseList" class="scrollable-list"></div>
</section>

<section id="page-loans" class="page">
    <h2 style="font-size:16px; color:#d4af37; margin-bottom:8px;">💰 السلف</h2>
    <div style="background:rgba(20,20,20,0.6); padding:12px; border-radius:14px; margin-bottom:12px;">
        <div style="display:flex; gap:6px; flex-wrap:wrap;">
            <input type="text" id="loanName" placeholder="الاسم" style="flex:1; min-width:60px; padding:8px; background:rgba(30,30,30,0.8); border:1px solid #d4af37; border-radius:10px; color:#f0e6c8;">
            <input type="number" id="loanAmount" placeholder="المبلغ" style="flex:1; min-width:60px; padding:8px; background:rgba(30,30,30,0.8); border:1px solid #d4af37; border-radius:10px; color:#f0e6c8;">
        </div>
        <button class="btn btn-primary" onclick="addLoan()" style="width:100%; margin-top:6px;">➕ إضافة</button>
    </div>
    <div id="loanList" class="scrollable-list"></div>
</section>

<section id="page-treasury" class="page">
    <h2 style="font-size:16px; color:#d4af37; margin-bottom:8px;">🏦 الخزنة</h2>
    <div class="treasury-balance" id="treasuryBalanceDisplay">0 ريال</div>
    <div style="background:rgba(20,20,20,0.6); padding:12px; border-radius:14px; margin-bottom:12px;">
        <div style="display:flex; gap:6px; flex-wrap:wrap;">
            <input type="number" id="treasuryAmount" placeholder="المبلغ" style="flex:2; min-width:70px; padding:8px; background:rgba(30,30,30,0.8); border:1px solid #d4af37; border-radius:10px; color:#f0e6c8;">
            <button class="btn btn-primary" onclick="treasuryDeposit()" style="flex:1;">💰 إيداع</button>
            <button class="btn btn-danger" onclick="treasuryWithdraw()" style="flex:1;">💸 سحب</button>
        </div>
    </div>
    <div id="treasuryLog" class="scrollable-list"></div>
</section>

<section id="page-reports" class="page">
    <h2 style="font-size:16px; color:#d4af37; margin-bottom:8px;">📊 التقارير</h2>
    <div class="report-card"><div class="title">📅 يومي</div><div id="dailyReport">جاري التحديث...</div></div>
    <div class="report-card"><div class="title">📆 أسبوعي</div><div id="weeklyReport">جاري التحديث...</div></div>
    <div class="report-card"><div class="title">📊 شهري</div><div id="monthlyReport">جاري التحديث...</div></div>
</section>

<section id="page-settings" class="page">
    <h2 style="font-size:16px; color:#d4af37; margin-bottom:8px;">⚙️ الإعدادات</h2>
    <div style="background:rgba(20,20,20,0.6); padding:12px; border-radius:14px;">
        <h3 style="color:#d4af37; font-size:14px; margin-bottom:6px; border-bottom:1px solid rgba(212,175,55,0.15); padding-bottom:4px;">💵 البيانات المالية</h3>
        <div class="form-group"><label>الراتب الشهري</label><input type="number" id="setSalary" step="0.01"></div>
        <div class="form-group"><label>رسوم الإقامة</label><input type="number" id="setIqama" step="0.01"></div>
        <div class="form-group"><label>النت</label><input type="number" id="setNet" step="0.01"></div>
        <div class="form-group"><label>بيت 1</label><input type="number" id="setHouse1" step="0.01"></div>
        <div class="form-group"><label>بيت 2</label><input type="number" id="setHouse2" step="0.01"></div>
        <div id="otherCommitmentsContainer"></div>
        <div class="btn-group"><button class="btn btn-secondary btn-sm" onclick="addOtherCommitmentField()">➕ إضافة التزام آخر</button></div>
        <button class="btn btn-primary mt-8" onclick="saveSettings()" style="width:100%;">💾 حفظ الإعدادات</button>

        <h3 style="color:#d4af37; font-size:14px; margin-top:12px; margin-bottom:6px; border-bottom:1px solid rgba(212,175,55,0.15); padding-bottom:4px;">🔧 إعدادات النظام</h3>
        <div class="form-group" style="display:flex; justify-content:space-between; align-items:center; padding:6px 0;">
            <span style="font-size:12px; color:#ccc;">🔊 الأصوات</span>
            <div class="switch active" id="toggleSound" onclick="toggleSetting('sound')"><div class="slider"></div></div>
        </div>
        <div class="form-group" style="display:flex; justify-content:space-between; align-items:center; padding:6px 0;">
            <span style="font-size:12px; color:#ccc;">🔔 الإشعارات</span>
            <div class="switch active" id="toggleNotifications" onclick="toggleSetting('notifications')"><div class="slider"></div></div>
        </div>
        <div class="form-group" style="display:flex; justify-content:space-between; align-items:center; padding:6px 0;">
            <span style="font-size:12px; color:#ccc;">⚠️ التنبيهات</span>
            <div class="switch active" id="toggleAlerts" onclick="toggleSetting('alerts')"><div class="slider"></div></div>
        </div>
        <div class="form-group" style="display:flex; justify-content:space-between; align-items:center; padding:6px 0;">
            <span style="font-size:12px; color:#ccc;">💡 النصائح الذكية</span>
            <div class="switch active" id="toggleTips" onclick="toggleSetting('tips')"><div class="slider"></div></div>
        </div>

        <h3 style="color:#d4af37; font-size:14px; margin-top:12px; margin-bottom:6px; border-bottom:1px solid rgba(212,175,55,0.15); padding-bottom:4px;">💾 النسخ الاحتياطي</h3>
        <div class="backup-row">
            <button class="btn btn-secondary" onclick="exportData()">📤 تصدير JSON</button>
            <button class="btn btn-secondary" onclick="document.getElementById('importFileInput').click()">📥 استيراد JSON</button>
            <input type="file" id="importFileInput" accept=".json" style="display:none;" onchange="importData(event)">
        </div>
        <button class="btn btn-danger mt-8" onclick="resetAllData()" style="width:100%;">🗑️ إعادة ضبط (مسح جميع البيانات)</button>
    </div>
</section>

<!-- === الشريط السفلي === -->
<nav class="tab-bar">
    <button class="tab-btn active" data-page="page-home"><span class="tab-icon">🏠</span>الرئيسية</button>
    <button class="tab-btn" data-page="page-expenses"><span class="tab-icon">💸</span>المصروفات</button>
    <button class="tab-btn" data-page="page-loans"><span class="tab-icon">💰</span>السلف</button>
    <button class="tab-btn" data-page="page-treasury"><span class="tab-icon">🏦</span>الخزنة</button>
    <button class="tab-btn" data-page="page-reports"><span class="tab-icon">📊</span>التقارير</button>
    <button class="tab-btn" data-page="page-settings"><span class="tab-icon">⚙️</span>الإعدادات</button>
</nav>

<!-- === MODAL === -->
<div class="modal-overlay" id="modalOverlay" onclick="if(event.target===this)closeModal()">
    <div class="modal-box">
        <h2 id="modalTitle">تعديل</h2>
        <div id="modalBody"></div>
        <div style="display:flex; gap:6px; margin-top:12px;">
            <button class="btn btn-secondary" onclick="closeModal()" style="flex:1;">إلغاء</button>
            <button class="btn btn-primary" id="modalSaveBtn" onclick="modalSave()" style="flex:1;">حفظ</button>
        </div>
    </div>
</div>

<script>
    // ============================================================
    // DATA LAYER
    // ============================================================
    const STORAGE_KEY = 'mizanyati_erp_mini_v4';

    function defaultData() {
        return {
            settings: {
                salary: 8000,
                iqama: 150,
                net: 300,
                house1: 1200,
                house2: 800,
                otherCommitments: [],
                sound: true,
                notifications: true,
                alerts: true,
                tips: true
            },
            expenses: [
                { id: 'd1', amount: 100, category: '🍔 أكل', note: 'غداء', date: new Date().toISOString().slice(0,10) }
            ],
            loans: [],
            treasury: [],
            treasuryBalance: 0,
            lastAutoTransferMonth: ''
        };
    }

    let data = JSON.parse(localStorage.getItem(STORAGE_KEY)) || defaultData();
    if (!data.settings.otherCommitments) data.settings.otherCommitments = [];

    function saveData() {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(data));
        renderAll();
    }

    // ============================================================
    // SOUND SYSTEM
    // ============================================================
    let audioCtx = null;

    function playSound() {
        if (data.settings.sound === false) return;
        try {
            if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            const oscillator = audioCtx.createOscillator();
            const gainNode = audioCtx.createGain();
            oscillator.connect(gainNode);
            gainNode.connect(audioCtx.destination);
            oscillator.frequency.value = 600;
            oscillator.type = 'sine';
            gainNode.gain.setValueAtTime(0.1, audioCtx.currentTime);
            gainNode.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.2);
            oscillator.start(audioCtx.currentTime);
            oscillator.stop(audioCtx.currentTime + 0.2);
        } catch(e) { /* Silent fail */ }
    }

    // ============================================================
    // TOAST NOTIFICATION SYSTEM
    // ============================================================
    function showToast(message, type = 'info', duration = 3000) {
        if (data.settings.notifications === false) return;
        const container = document.getElementById('toast-container');
        const toast = document.createElement('div');
        toast.className = `toast ${type}`;
        toast.innerHTML = message;
        container.appendChild(toast);
        playSound();
        setTimeout(() => {
            if (toast.parentNode) toast.remove();
        }, duration);
    }

    // ============================================================
    // HELPERS
    // ============================================================
    function formatCurrency(num) { return Number(num).toFixed(2); }
    function getToday() { return new Date().toISOString().split('T')[0]; }
    function getCurrentMonth() { return new Date().toISOString().slice(0,7); }
    function totalCommitments() {
        const s = data.settings;
        let total = (s.iqama || 0) + (s.net || 0) + (s.house1 || 0) + (s.house2 || 0);
        (s.otherCommitments || []).forEach(c => total += Number(c.amount) || 0);
        return total;
    }
    function totalExpensesForMonth(monthStr) {
        return data.expenses.filter(e => e.date && e.date.startsWith(monthStr)).reduce((sum, e) => sum + Number(e.amount), 0);
    }
    function getWeekStart() {
        const d = new Date();
        const day = d.getDay();
        const diff = d.getDate() - day + (day === 0 ? -6 : 1);
        return new Date(d.setDate(diff)).toISOString().split('T')[0];
    }

    // ============================================================
    // NAVIGATION
    // ============================================================
    function switchPage(pageId) {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        const target = document.getElementById(pageId);
        if (target) target.classList.add('active');
        document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
        const btn = document.querySelector(`.tab-btn[data-page="${pageId}"]`);
        if (btn) btn.classList.add('active');
    }

    document.addEventListener('DOMContentLoaded', function() {
        document.querySelectorAll('.tab-btn').forEach(btn => {
            btn.addEventListener('click', function() {
                switchPage(this.getAttribute('data-page'));
            });
        });
        document.getElementById('expDate').value = getToday();
        renderAll();
    });

    // ============================================================
    // RENDER ALL
    // ============================================================
    function renderAll() {
        // Home
        const salary = data.settings.salary || 0;
        const commitments = totalCommitments();
        const currentMonth = getCurrentMonth();
        const expenses = totalExpensesForMonth(currentMonth);
        const treasury = data.treasuryBalance || 0;
        const remaining = salary - commitments - expenses;
        const spentPercent = salary > 0 ? (expenses / salary) * 100 : 0;
        const savedPercent = salary > 0 ? (remaining / salary) * 100 : 0;
        document.getElementById('dashSalary').textContent = formatCurrency(salary) + ' ريال';
        document.getElementById('dashCommitments').textContent = formatCurrency(commitments) + ' ريال';
        document.getElementById('dashExpenses').textContent = formatCurrency(expenses) + ' ريال';
        document.getElementById('dashTreasury').textContent = formatCurrency(treasury) + ' ريال';
        document.getElementById('dashRemaining').textContent = formatCurrency(remaining) + ' ريال';
        document.getElementById('dashSpentPercent').textContent = spentPercent.toFixed(0) + '%';
        document.getElementById('dashSavedPercent').textContent = savedPercent.toFixed(0) + '%';
        document.getElementById('dashForecast').textContent = formatCurrency(salary - commitments - (expenses * 1.0)) + ' ريال';

        // Insights & Alerts
        let insightsHTML = '';
        if (data.settings.tips !== false) {
            insightsHTML += `<div>💡 أجمالي المصروفات لهذا الشهر: ${formatCurrency(expenses)} ريال</div>`;
            if (spentPercent > 80) {
                insightsHTML += `<div style="color:#f39c12;">⚠️ تحذير: لقد صرفت ${spentPercent.toFixed(0)}% من ميزانيتك!</div>`;
                if (data.settings.alerts !== false) {
                    showToast(`⚠️ تحذير: صرفت ${spentPercent.toFixed(0)}% من ميزانيتك!`, 'warning', 5000);
                }
            }
        } else {
            insightsHTML = '<div>💡 النصائح معطلة</div>';
        }
        document.getElementById('insightsContainer').innerHTML = insightsHTML;
        document.getElementById('autoTransferInfo').innerHTML = 'يتم تحويل المتبقي في نهاية كل شهر';

        // Expenses
        const list = document.getElementById('expenseList');
        if (data.expenses.length === 0) {
            list.innerHTML = '<div class="empty-state"><div class="icon">📭</div>لا توجد مصروفات</div>';
        } else {
            let html = '';
            data.expenses.slice().reverse().forEach(e => {
                html += `<div class="list-item"><div class="info"><div class="main">${e.category || '📦 أخرى'} - ${formatCurrency(e.amount)} ريال</div><div class="sub">${e.date || ''} ${e.note ? '• ' + e.note : ''}</div></div><div class="actions"><button class="btn btn-secondary btn-sm" onclick="editExpense('${e.id}')">✏️</button><button class="btn btn-danger btn-sm" onclick="deleteExpense('${e.id}')">🗑️</button></div></div>`;
            });
            list.innerHTML = html;
        }

        // Loans
        const loanList = document.getElementById('loanList');
        if (data.loans.length === 0) {
            loanList.innerHTML = '<div class="empty-state"><div class="icon">💰</div>لا توجد سلف</div>';
        } else {
            let html = '';
            data.loans.slice().reverse().forEach(l => {
                const badge = l.status === 'pending' ? '🟡 معلقة' : '🟢 مسددة';
                html += `<div class="list-item"><div class="info"><div class="main">${l.name} - ${formatCurrency(l.amount)} ريال ${badge}</div><div class="sub">${l.date || ''}</div></div><div class="actions"><button class="btn btn-primary btn-sm" onclick="settleLoan('${l.id}')">✅ تسديد</button><button class="btn btn-danger btn-sm" onclick="deleteLoan('${l.id}')">🗑️</button></div></div>`;
            });
            loanList.innerHTML = html;
        }

        // Treasury
        document.getElementById('treasuryBalanceDisplay').innerHTML = `${formatCurrency(data.treasuryBalance)} ريال`;
        const log = document.getElementById('treasuryLog');
        if (data.treasury.length === 0) {
            log.innerHTML = '<div class="empty-state"><div class="icon">🏦</div>لا توجد عمليات</div>';
        } else {
            let html = '';
            data.treasury.slice().reverse().forEach(t => {
                const label = t.type === 'deposit' ? '💰 إيداع' : t.type === 'withdraw' ? '💸 سحب' : '🔄 تحويل';
                html += `<div class="list-item"><div class="info"><div class="main">${label} - ${formatCurrency(t.amount)} ريال</div><div class="sub">${t.date || ''} | الرصيد: ${formatCurrency(t.balanceAfter)}</div></div></div>`;
            });
            log.innerHTML = html;
        }

        // Reports
        const dailySpent = data.expenses.filter(e => e.date === getToday()).reduce((s, e) => s + e.amount, 0);
        document.getElementById('dailyReport').innerHTML = `صرف اليوم: ${formatCurrency(dailySpent)} ريال`;
        const weekThis = data.expenses.filter(e => e.date >= getWeekStart()).reduce((s, e) => s + e.amount, 0);
        document.getElementById('weeklyReport').innerHTML = `هذا الأسبوع: ${formatCurrency(weekThis)} ريال`;
        document.getElementById('monthlyReport').innerHTML = `هذا الشهر: ${formatCurrency(expenses)} ريال`;

        // Settings UI
        document.getElementById('setSalary').value = data.settings.salary || '';
        document.getElementById('setIqama').value = data.settings.iqama || '';
        document.getElementById('setNet').value = data.settings.net || '';
        document.getElementById('setHouse1').value = data.settings.house1 || '';
        document.getElementById('setHouse2').value = data.settings.house2 || '';
        const container = document.getElementById('otherCommitmentsContainer');
        let htmlC = '';
        (data.settings.otherCommitments || []).forEach((c, idx) => {
            htmlC += `<div class="form-group" style="display:flex; gap:6px; align-items:center;"><input type="text" placeholder="اسم الالتزام" value="${c.name || ''}" style="flex:1;" id="otherName_${idx}"><input type="number" placeholder="المبلغ" step="0.01" value="${c.amount || ''}" style="flex:1;" id="otherAmount_${idx}"><button class="btn btn-danger btn-sm" onclick="removeOtherCommitment(${idx})">✕</button></div>`;
        });
        container.innerHTML = htmlC;
        setToggle('sound', data.settings.sound !== false);
        setToggle('notifications', data.settings.notifications !== false);
        setToggle('alerts', data.settings.alerts !== false);
        setToggle('tips', data.settings.tips !== false);
    }

    // ============================================================
    // TOGGLES
    // ============================================================
    function setToggle(key, active) {
        const el = document.getElementById('toggle' + key.charAt(0).toUpperCase() + key.slice(1));
        if (el) {
            if (active) el.classList.add('active');
            else el.classList.remove('active');
        }
    }

    function toggleSetting(key) {
        data.settings[key] = !data.settings[key];
        saveData();
        showToast(`تم تغيير إعداد ${key}`, 'info');
        playSound();
    }

    // ============================================================
    // CRUD
    // ============================================================
    function addExpense() {
        const amount = parseFloat(document.getElementById('expAmount').value);
        if (isNaN(amount) || amount <= 0) { 
            showToast('الرجاء إدخال مبلغ صحيح', 'warning');
            return; 
        }
        data.expenses.push({
            id: Date.now().toString(),
            amount: amount,
            category: document.getElementById('expCategory').value,
            note: document.getElementById('expNote').value.trim(),
            date: document.getElementById('expDate').value || getToday()
        });
        document.getElementById('expAmount').value = '';
        document.getElementById('expNote').value = '';
        saveData();
        showToast(`✅ تم إضافة مصروف بقيمة ${formatCurrency(amount)} ريال`, 'success');
        playSound();
    }

    function deleteExpense(id) {
        if (confirm('حذف هذه المصروف؟')) {
            data.expenses = data.expenses.filter(e => e.id !== id);
            saveData();
            showToast('تم حذف المصروف', 'info');
            playSound();
        }
    }

    function editExpense(id) {
        const expense = data.expenses.find(e => e.id === id);
        if (!expense) return;
        document.getElementById('modalTitle').textContent = 'تعديل المصروف';
        document.getElementById('modalBody').innerHTML = `
            <div class="form-group"><label>المبلغ</label><input type="number" id="modalAmount" value="${expense.amount}" style="width:100%; padding:8px; background:rgba(30,30,30,0.8); border:1px solid #d4af37; border-radius:10px; color:#f0e6c8;"></div>
            <div class="form-group"><label>التصنيف</label><select id="modalCategory" style="width:100%; padding:8px; background:rgba(30,30,30,0.8); border:1px solid #d4af37; border-radius:10px; color:#f0e6c8;">${['🍔 أكل','⛽ بنزين','🛒 تسوق','📦 أخرى'].map(c => `<option value="${c}" ${c===expense.category?'selected':''}>${c}</option>`).join('')}</select></div>`;
        document.getElementById('modalOverlay').classList.add('show');
        window.currentEditId = id;
        window.modalCallback = function() {
            const amt = parseFloat(document.getElementById('modalAmount').value);
            if (isNaN(amt) || amt <= 0) { 
                showToast('مبلغ غير صحيح', 'warning');
                return; 
            }
            const exp = data.expenses.find(e => e.id === window.currentEditId);
            if (exp) {
                exp.amount = amt;
                exp.category = document.getElementById('modalCategory').value;
                saveData();
                showToast('تم تعديل المصروف', 'info');
                playSound();
            }
            closeModal();
        };
    }

    function addLoan() {
        const name = document.getElementById('loanName').value.trim();
        const amount = parseFloat(document.getElementById('loanAmount').value);
        if (!name || isNaN(amount) || amount <= 0) { 
            showToast('أدخل اسم ومبلغ صحيح', 'warning');
            return; 
        }
        data.loans.push({
            id: Date.now().toString(),
            name: name,
            amount: amount,
            date: getToday(),
            status: 'pending'
        });
        document.getElementById('loanName').value = '';
        document.getElementById('loanAmount').value = '';
        saveData();
        showToast(`✅ تم إضافة سلفة بقيمة ${formatCurrency(amount)} ريال لـ ${name}`, 'success');
        playSound();
    }

    function deleteLoan(id) {
        if (confirm('حذف هذه السلفة؟')) {
            data.loans = data.loans.filter(l => l.id !== id);
            saveData();
            showToast('تم حذف السلفة', 'info');
            playSound();
        }
    }

    function settleLoan(id) {
        const loan = data.loans.find(l => l.id === id);
        if (loan) {
            loan.status = 'paid';
            saveData();
            showToast(`✅ تم تسديد سلفة ${loan.name}`, 'success');
            playSound();
        }
    }

    function treasuryDeposit() {
        const amount = parseFloat(document.getElementById('treasuryAmount').value);
        if (isNaN(amount) || amount <= 0) { 
            showToast('أدخل مبلغ صحيح', 'warning');
            return; 
        }
        data.treasuryBalance += amount;
        data.treasury.push({
            id: Date.now(),
            type: 'deposit',
            amount: amount,
            date: getToday(),
            balanceAfter: data.treasuryBalance
        });
        document.getElementById('treasuryAmount').value = '';
        saveData();
        showToast(`💰 تم إيداع ${formatCurrency(amount)} ريال في الخزنة`, 'success');
        playSound();
    }

    function treasuryWithdraw() {
        const amount = parseFloat(document.getElementById('treasuryAmount').value);
        if (isNaN(amount) || amount <= 0) { 
            showToast('أدخل مبلغ صحيح', 'warning');
            return; 
        }
        if (amount > data.treasuryBalance) { 
            showToast('الرصيد غير كافي', 'warning');
            return; 
        }
        data.treasuryBalance -= amount;
        data.treasury.push({
            id: Date.now(),
            type: 'withdraw',
            amount: amount,
            date: getToday(),
            balanceAfter: data.treasuryBalance
        });
        document.getElementById('treasuryAmount').value = '';
        saveData();
        showToast(`💸 تم سحب ${formatCurrency(amount)} ريال من الخزنة`, 'info');
        playSound();
    }

    function closeModal() { document.getElementById('modalOverlay').classList.remove('show'); }
    function modalSave() { if (window.modalCallback) window.modalCallback(); }

    // ============================================================
    // SETTINGS ACTIONS
    // ============================================================
    function saveSettings() {
        data.settings.salary = parseFloat(document.getElementById('setSalary').value) || 0;
        data.settings.iqama = parseFloat(document.getElementById('setIqama').value) || 0;
        data.settings.net = parseFloat(document.getElementById('setNet').value) || 0;
        data.settings.house1 = parseFloat(document.getElementById('setHouse1').value) || 0;
        data.settings.house2 = parseFloat(document.getElementById('setHouse2').value) || 0;

        const other = [];
        const container = document.getElementById('otherCommitmentsContainer');
        const groups = container.querySelectorAll('.form-group');
        groups.forEach((g, idx) => {
            const nameEl = g.querySelector('input[type="text"]');
            const amtEl = g.querySelector('input[type="number"]');
            if (nameEl && amtEl) {
                const name = nameEl.value.trim();
                const amount = parseFloat(amtEl.value) || 0;
                if (name || amount > 0) {
                    other.push({ name: name || 'التزام ' + (idx+1), amount: amount });
                }
            }
        });
        data.settings.otherCommitments = other;

        saveData();
        showToast('✅ تم حفظ الإعدادات بنجاح', 'success');
        playSound();
    }

    function addOtherCommitmentField() {
        const container = document.getElementById('otherCommitmentsContainer');
        const idx = container.children.length;
        const div = document.createElement('div');
        div.className = 'form-group';
        div.style.display = 'flex';
        div.style.gap = '6px';
        div.style.alignItems = 'center';
        div.innerHTML = `<input type="text" placeholder="اسم الالتزام" style="flex:1;" id="otherName_${idx}"><input type="number" placeholder="المبلغ" step="0.01" style="flex:1;" id="otherAmount_${idx}"><button class="btn btn-danger btn-sm" onclick="removeOtherCommitmentField(this)">✕</button>`;
        container.appendChild(div);
    }

    function removeOtherCommitmentField(btn) {
        btn.parentElement.remove();
    }

    function removeOtherCommitment(index) {
        data.settings.otherCommitments.splice(index, 1);
        saveData();
    }

    // ============================================================
    // BACKUP
    // ============================================================
    function exportData() {
        const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = 'mizanyati_backup.json';
        a.click();
        URL.revokeObjectURL(url);
        showToast('📤 تم تصدير البيانات', 'info');
        playSound();
    }

    function importData(event) {
        const file = event.target.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = function(e) {
            try {
                const imported = JSON.parse(e.target.result);
                if (typeof imported === 'object' && imported.settings) {
                    data = imported;
                    saveData();
                    showToast('📥 تم استيراد البيانات بنجاح', 'success');
                } else {
                    showToast('ملف غير صالح', 'warning');
                }
            } catch(err) {
                showToast('خطأ في قراءة الملف', 'warning');
            }
        };
        reader.readAsText(file);
        event.target.value = '';
    }

    function resetAllData() {
        if (!confirm('⚠️ هل أنت متأكد من مسح جميع البيانات؟ لا يمكن التراجع.')) return;
        if (!confirm('تأكيد آخر: سيتم حذف كل المصروفات والسلف والخزنة والإعدادات.')) return;
        data = defaultData();
        saveData();
        showToast('🗑️ تم مسح جميع البيانات', 'info');
        playSound();
    }

    // ============================================================
    // CLOCK
    // ============================================================
    function updateClock() {
        const now = new Date();
        const h = now.getHours(), m = now.getMinutes(), s = now.getSeconds();
        document.getElementById('digitalTime').textContent = h.toString().padStart(2,'0') + ':' + m.toString().padStart(2,'0') + ':' + s.toString().padStart(2,'0');
        document.getElementById('digitalDay').textContent = ['الأحد','الاثنين','الثلاثاء','الأربعاء','الخميس','الجمعة','السبت'][now.getDay()];
        document.getElementById('digitalDate').textContent = now.getFullYear() + '-' + (now.getMonth()+1).toString().padStart(2,'0') + '-' + now.getDate().toString().padStart(2,'0');
        const hourDeg = (h % 12) * 30 + m * 0.5;
        const minuteDeg = m * 6 + s * 0.1;
        const secondDeg = s * 6;
        document.getElementById('hourHand').style.transform = `rotate(${hourDeg}deg)`;
        document.getElementById('minuteHand').style.transform = `rotate(${minuteDeg}deg)`;
        document.getElementById('secondHand').style.transform = `rotate(${secondDeg}deg)`;
    }
    setInterval(updateClock, 1000);
    updateClock();

    // ============================================================
    // PWA / SW
    // ============================================================
    if ('serviceWorker' in navigator) {
        navigator.serviceWorker.register('sw.js').catch(() => {});
    }

    console.log('✅ تطبيق ميزانيتي V4 مع الصوت والإشعارات يعمل!');
</script>
</body>
</html>
