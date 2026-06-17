# sarossss.github.io
[deepseek_html_20260616_cf3af3.html](https://github.com/user-attachments/files/29045398/deepseek_html_20260616_cf3af3.html)
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=no">
  <title>倒计时 · 定格时间</title>
  <!-- iOS 主屏幕支持 -->
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="default">
  <meta name="apple-mobile-web-app-title" content="倒计时">
  <link rel="apple-touch-icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='45' fill='none' stroke='%231a1817' stroke-width='6'/%3E%3Cpath d='M50 25v25l15 10' fill='none' stroke='%231a1817' stroke-width='6'/%3E%3C/svg%3E">
  <style>
    :root {
      --bg-normal: #faf7f4;
      --bg-flash-1: #e9e4de;
      --bg-flash-2: #d9d3cc;
      --text-primary: #1a1817;
      --text-separator: #b5b0a8;
      --text-separator-flash: #9e9890;
      --gear-color: #c4bfb8;
      --gear-hover: #3d3834;
      --panel-bg: #fefcf9;
      --panel-shadow: 0 2px 20px rgba(0, 0, 0, 0.07), 0 0 0 1px rgba(0, 0, 0, 0.04);
      --panel-shadow-strong: 0 4px 32px rgba(0, 0, 0, 0.10), 0 0 0 1px rgba(0, 0, 0, 0.05);
      --overlay-bg: rgba(0, 0, 0, 0.03);
      --btn-bg: #1a1817;
      --btn-text: #faf7f4;
      --btn-hover-bg: #3d3834;
      --input-border: #d5cfc8;
      --input-focus-border: #1a1817;
      --cell-today-bg: rgba(0,0,0,0.03);
      --cell-time-color: #6b6560;
      --transition-smooth: 0.35s cubic-bezier(0.25, 0.1, 0.25, 1);
      --transition-fast: 0.2s cubic-bezier(0.25, 0.1, 0.25, 1);
      --safe-area-inset-bottom: env(safe-area-inset-bottom, 0px);
    }

    * { margin: 0; padding: 0; box-sizing: border-box; }
    html { height: 100%; -webkit-tap-highlight-color: transparent; -webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale; text-rendering: optimizeLegibility; }
    body {
      height: 100%; display: flex; align-items: center; justify-content: center;
      background-color: var(--bg-normal);
      font-family: -apple-system, BlinkMacSystemFont, 'SF Pro Display', 'Segoe UI', 'Helvetica Neue', sans-serif;
      transition: background-color var(--transition-smooth);
      user-select: none; -webkit-user-select: none; -webkit-touch-callout: none;
      overflow: hidden; position: relative;
      padding-bottom: var(--safe-area-inset-bottom);
    }

    body.expired { animation: greyPulse 1.6s ease-in-out infinite; }
    @keyframes greyPulse {
      0%, 100% { background-color: var(--bg-flash-1); }
      38% { background-color: var(--bg-flash-2); }
      62% { background-color: var(--bg-flash-2); }
    }

    .countdown-container {
      display: flex; align-items: center; justify-content: center;
      gap: clamp(2px, 1vw, 8px); z-index: 1; position: relative; padding: 20px;
    }
    .time-part {
      font-size: clamp(3.2rem, 8.5vw, 7.5rem); font-weight: 230; letter-spacing: -0.02em;
      color: var(--text-primary); font-variant-numeric: tabular-nums;
      font-family: 'SF Mono', 'JetBrains Mono', 'Consolas', 'Menlo', 'Courier New', monospace;
      line-height: 1; transition: color var(--transition-fast);
      min-width: clamp(2.6rem, 7vw, 6rem); text-align: center; display: inline-block;
    }
    .time-separator {
      font-size: clamp(2.4rem, 6.5vw, 5.8rem); font-weight: 160; color: var(--text-separator);
      font-family: 'SF Mono', 'JetBrains Mono', 'Consolas', 'Menlo', 'Courier New', monospace;
      line-height: 1; transition: color var(--transition-fast);
      padding-bottom: clamp(0.1rem, 0.3vw, 0.4rem); align-self: center;
    }
    body.expired .time-separator { color: var(--text-separator-flash); }

    .countdown-container::after {
      content: ''; position: absolute; bottom: 8%; left: 5%; right: 5%; height: 4px;
      background: transparent; border-radius: 50%; box-shadow: 0 16px 40px rgba(0,0,0,0.03);
      z-index: -1; transition: box-shadow var(--transition-smooth);
    }
    body.expired .countdown-container::after { box-shadow: 0 16px 40px rgba(0,0,0,0.06); }

    .corner-btn {
      position: fixed; width: 42px; height: 42px; border-radius: 50%;
      border: none; background: transparent; cursor: pointer; display: flex;
      align-items: center; justify-content: center; z-index: 10;
      transition: all var(--transition-fast); -webkit-tap-highlight-color: transparent;
      outline: none; padding: 8px;
    }
    .corner-btn:hover { background: rgba(0,0,0,0.04); }
    .corner-btn:active { background: rgba(0,0,0,0.08); transform: scale(0.94); transition: transform 0.1s ease; }
    .corner-btn svg { width: 22px; height: 22px; stroke: var(--gear-color); fill: none; stroke-width: 1.6; stroke-linecap: round; stroke-linejoin: round; transition: stroke var(--transition-fast), transform 0.6s cubic-bezier(0.25,0.1,0.25,1); }
    .corner-btn:hover svg { stroke: var(--gear-hover); }
    .corner-btn:active svg { transform: scale(0.9); }

    .settings-btn { bottom: clamp(18px, 3.5vh, 28px); right: clamp(18px, 3.5vw, 28px); }
    .moment-btn { top: clamp(18px, 3.5vh, 28px); right: clamp(18px, 3.5vw, 28px); }
    .calendar-btn { bottom: clamp(18px, 3.5vh, 28px); left: clamp(18px, 3.5vw, 28px); }

    .settings-btn:hover svg { transform: rotate(30deg); }
    .settings-btn:active svg { transform: rotate(60deg); }

    .icon-hidden { display: none; }

    .overlay {
      position: fixed; inset: 0; background: var(--overlay-bg); z-index: 20;
      opacity: 0; pointer-events: none; transition: opacity var(--transition-smooth);
      backdrop-filter: blur(1px); -webkit-backdrop-filter: blur(1px);
    }
    .overlay.active { opacity: 1; pointer-events: auto; }

    .settings-panel, .calendar-panel {
      position: fixed; z-index: 30; background: var(--panel-bg); border-radius: 16px;
      padding: clamp(20px, 4vw, 28px); box-shadow: var(--panel-shadow);
      opacity: 0; pointer-events: none; transform: translateY(12px) scale(0.96);
      transition: all var(--transition-smooth);
      top: 50%; left: 50%; transform-origin: center center;
    }
    .settings-panel.active, .calendar-panel.active {
      opacity: 1; pointer-events: auto; transform: translateY(0) scale(1);
      box-shadow: var(--panel-shadow-strong);
    }
    .settings-panel {
      width: min(340px, 88vw);
      translate: -50% -50%;
    }
    .calendar-panel {
      width: min(380px, 92vw);
      max-height: 85vh;
      overflow-y: auto;
      translate: -50% -50%;
      padding: 20px 18px 18px;
    }

    .panel-header {
      display: flex; align-items: center; justify-content: space-between; margin-bottom: 18px;
    }
    .panel-title {
      font-size: 1rem; font-weight: 500; color: var(--text-primary); letter-spacing: 0.01em;
    }
    .panel-close {
      width: 30px; height: 30px; border-radius: 50%; border: none; background: transparent;
      cursor: pointer; display: flex; align-items: center; justify-content: center;
      transition: background var(--transition-fast); outline: none;
      color: #9e9890; font-size: 1.2rem; line-height: 1;
    }
    .panel-close:hover { background: rgba(0,0,0,0.05); color: #3d3834; }
    .panel-close:active { background: rgba(0,0,0,0.1); }

    .input-group { display: flex; align-items: center; gap: 10px; margin-bottom: 20px; }
    .time-input {
      flex: 1; padding: 12px 14px; border: 1.5px solid var(--input-border); border-radius: 10px;
      font-size: 1.05rem; font-family: 'SF Mono','JetBrains Mono','Consolas','Menlo',monospace;
      color: var(--text-primary); background: #fdfbf8; outline: none;
      transition: all var(--transition-fast); letter-spacing: 0.03em; text-align: center;
    }
    .time-input:focus { border-color: var(--input-focus-border); background: #fff; box-shadow: 0 0 0 3px rgba(26,24,23,0.06); }
    .btn-save {
      width: 100%; padding: 12px 20px; border: none; border-radius: 10px;
      background: var(--btn-bg); color: var(--btn-text); font-size: 0.95rem; font-weight: 500;
      letter-spacing: 0.03em; cursor: pointer; transition: all var(--transition-fast);
      outline: none; font-family: inherit;
    }
    .btn-save:hover { background: var(--btn-hover-bg); box-shadow: 0 2px 12px rgba(0,0,0,0.12); }
    .btn-save:active { background: #0d0c0b; transform: scale(0.97); }
    .btn-save.saved { background: #2e7d32; }
    .btn-save.saved::after { content: ' ✓'; font-weight: 500; }

    .cal-nav {
      display: flex; align-items: center; justify-content: space-between; margin-bottom: 14px;
    }
    .cal-nav-btn {
      width: 32px; height: 32px; border: none; background: transparent; cursor: pointer;
      border-radius: 50%; display: flex; align-items: center; justify-content: center;
      font-size: 1.2rem; color: var(--text-separator); transition: all var(--transition-fast);
    }
    .cal-nav-btn:hover { background: rgba(0,0,0,0.04); color: var(--text-primary); }
    .cal-month-title {
      font-weight: 500; font-size: 1rem; color: var(--text-primary); letter-spacing: 0.02em;
    }
    .cal-weekdays {
      display: grid; grid-template-columns: repeat(7, 1fr); text-align: center;
      font-size: 0.7rem; color: #9e9890; margin-bottom: 6px; font-weight: 500;
    }
    .cal-dates {
      display: grid; grid-template-columns: repeat(7, 1fr); gap: 4px;
    }
    .cal-cell {
      aspect-ratio: 1 / 1; display: flex; flex-direction: column; align-items: center;
      justify-content: center; border-radius: 8px; font-size: 0.85rem;
      color: var(--text-primary); position: relative; transition: background var(--transition-fast);
      padding: 2px;
    }
    .cal-cell.today { background: var(--cell-today-bg); font-weight: 600; }
    .cal-cell .date-num { line-height: 1.2; }
    .cal-cell .moment-time {
      font-size: 0.55rem; color: var(--cell-time-color); margin-top: 1px;
      font-variant-numeric: tabular-nums; letter-spacing: -0.01em;
      white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 100%;
    }
    .cal-stats {
      margin-top: 16px; padding-top: 14px; border-top: 1px solid #e9e4de;
      font-size: 0.8rem; color: #4a4642; display: flex; flex-direction: column; gap: 6px;
    }
    .cal-stats span { font-weight: 500; color: var(--text-primary); }

    .ambient-dot {
      position: fixed; border-radius: 50%; pointer-events: none; z-index: 0;
      opacity: 0.03; background: #1a1817; transition: opacity var(--transition-smooth);
    }
    .ambient-dot.dot-1 { width: 300px; height: 300px; top: -120px; left: -80px; }
    .ambient-dot.dot-2 { width: 200px; height: 200px; bottom: -60px; right: -40px; opacity: 0.025; }
    body.expired .ambient-dot { opacity: 0.06; background: #8a8278; }

    @media (max-width: 480px) {
      .time-part { font-weight: 250; min-width: clamp(2.2rem, 6.5vw, 3.5rem); }
      .time-separator { font-weight: 180; }
      .corner-btn { width: 38px; height: 38px; padding: 7px; }
      .corner-btn svg { width: 19px; height: 19px; }
      .settings-btn { bottom: 14px; right: 14px; }
      .moment-btn { top: 14px; right: 14px; }
      .calendar-btn { bottom: 14px; left: 14px; }
      .settings-panel { border-radius: 14px; padding: 18px 16px 20px; width: 90vw; max-width: 320px; }
      .calendar-panel { padding: 16px 12px; width: 94vw; max-height: 80vh; }
      .btn-save { padding: 11px 16px; font-size: 0.9rem; }
      .time-input { padding: 10px 12px; font-size: 1rem; }
      .panel-title { font-size: 0.95rem; }
    }

    @media (prefers-reduced-motion: reduce) {
      body { transition: none; }
      body.expired { animation: none; background-color: var(--bg-flash-1); }
      .corner-btn svg { transition: stroke 0.1s ease; }
      .settings-panel, .calendar-panel { transition: opacity 0.15s ease; }
      .overlay { transition: opacity 0.15s ease; }
    }
  </style>
</head>
<body>
  <div class="ambient-dot dot-1"></div>
  <div class="ambient-dot dot-2"></div>

  <div class="countdown-container" id="countdownDisplay" aria-live="polite" aria-label="倒计时">
    <span class="time-part" id="hours">00</span>
    <span class="time-separator">:</span>
    <span class="time-part" id="minutes">00</span>
    <span class="time-separator">:</span>
    <span class="time-part" id="seconds">00</span>
  </div>

  <button class="corner-btn moment-btn" id="momentBtn" aria-label="定格当前时间" title="定格时间">
    <svg class="icon-clock" viewBox="0 0 24 24"><circle cx="12" cy="12" r="10"/><path d="M12 7v5l3 3"/></svg>
    <svg class="icon-check icon-hidden" viewBox="0 0 24 24"><polyline points="5 13 9 17 19 7"/></svg>
  </button>

  <button class="corner-btn calendar-btn" id="calendarBtn" aria-label="打卡日历" title="日历">
    <svg viewBox="0 0 24 24"><rect x="4" y="5" width="16" height="16" rx="2"/><path d="M4 10h16M8 3v3M16 3v3"/></svg>
  </button>

  <button class="corner-btn settings-btn" id="settingsBtn" aria-label="设置目标时间" title="设置目标时间">
    <svg viewBox="0 0 24 24"><circle cx="12" cy="12" r="3.5"/><path d="M12 1.5v3M12 19.5v3M1.5 12h3M19.5 12h3M4.6 4.6l2.12 2.12M17.28 17.28l2.12 2.12M4.6 19.4l2.12-2.12M17.28 6.72l2.12-2.12" stroke-linecap="round"/></svg>
  </button>

  <div class="overlay" id="overlay"></div>

  <div class="settings-panel" id="settingsPanel" role="dialog" aria-modal="true" aria-labelledby="panelTitle">
    <div class="panel-header">
      <span class="panel-title" id="panelTitle">目标时间</span>
      <button class="panel-close" id="panelClose" aria-label="关闭">&times;</button>
    </div>
    <div class="input-group">
      <input type="time" class="time-input" id="timeInput" step="60" aria-label="选择目标时间">
    </div>
    <button class="btn-save" id="btnSave">保存</button>
  </div>

  <div class="calendar-panel" id="calendarPanel" role="dialog" aria-modal="true" aria-label="定格日历">
    <div class="panel-header">
      <span class="panel-title">定格日历</span>
      <button class="panel-close" id="calendarClose" aria-label="关闭">&times;</button>
    </div>
    <div class="cal-nav">
      <button class="cal-nav-btn" id="prevMonth" aria-label="上个月">←</button>
      <span class="cal-month-title" id="monthTitle">2026年6月</span>
      <button class="cal-nav-btn" id="nextMonth" aria-label="下个月">→</button>
    </div>
    <div class="cal-weekdays">
      <div>一</div><div>二</div><div>三</div><div>四</div><div>五</div><div>六</div><div>日</div>
    </div>
    <div class="cal-dates" id="datesGrid"></div>
    <div class="cal-stats">
      <div>打卡点：<span id="statsMedian">--:--:--</span></div>
      <div>本月打卡 <span id="statsCount">0</span> 次</div>
    </div>
  </div>

  <script>
    (function() {
      // ----- 动态注入 PWA Manifest -----
      const manifest = {
        name: '倒计时 · 定格时间',
        short_name: '倒计时',
        description: '极简黑白倒计时与时间定格工具',
        start_url: location.href,
        display: 'standalone',
        background_color: '#faf7f4',
        theme_color: '#1a1817',
        icons: [{
          src: 'data:image/svg+xml,' + encodeURIComponent('<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"><circle cx="50" cy="50" r="45" fill="none" stroke="%231a1817" stroke-width="6"/><path d="M50 25v25l15 10" fill="none" stroke="%231a1817" stroke-width="6"/></svg>'),
          sizes: '100x100',
          type: 'image/svg+xml',
          purpose: 'any'
        }]
      };
      const blob = new Blob([JSON.stringify(manifest)], {type: 'application/json'});
      const manifestURL = URL.createObjectURL(blob);
      const linkEl = document.createElement('link');
      linkEl.rel = 'manifest';
      linkEl.href = manifestURL;
      document.head.appendChild(linkEl);

      // ----- 原有逻辑 -----
      const hoursEl = document.getElementById('hours');
      const minutesEl = document.getElementById('minutes');
      const secondsEl = document.getElementById('seconds');
      const settingsBtn = document.getElementById('settingsBtn');
      const overlay = document.getElementById('overlay');
      const settingsPanel = document.getElementById('settingsPanel');
      const panelClose = document.getElementById('panelClose');
      const timeInput = document.getElementById('timeInput');
      const btnSave = document.getElementById('btnSave');
      const momentBtn = document.getElementById('momentBtn');
      const iconClock = momentBtn.querySelector('.icon-clock');
      const iconCheck = momentBtn.querySelector('.icon-check');
      const calendarBtn = document.getElementById('calendarBtn');
      const calendarPanel = document.getElementById('calendarPanel');
      const calendarClose = document.getElementById('calendarClose');
      const datesGrid = document.getElementById('datesGrid');
      const monthTitle = document.getElementById('monthTitle');
      const prevMonthBtn = document.getElementById('prevMonth');
      const nextMonthBtn = document.getElementById('nextMonth');
      const statsMedian = document.getElementById('statsMedian');
      const statsCount = document.getElementById('statsCount');
      const body = document.body;

      const DEFAULT_TARGET = '21:00';
      const STORAGE_TARGET_KEY = 'countdown_target_time';
      const STORAGE_MOMENT_KEY = 'moment_log';
      let targetHour = 21;
      let targetMinute = 0;
      let isExpired = false;
      let currentDateStr = '';
      let updateTimerId = null;
      let saveFeedbackTimer = null;
      let momentData = {};
      let calendarYear, calendarMonth;
      let calendarOpen = false;

      function pad(n) { return String(n).padStart(2, '0'); }
      function getDateStr(d = new Date()) {
        return `${d.getFullYear()}-${pad(d.getMonth()+1)}-${pad(d.getDate())}`;
      }
      function timeToSeconds(timeStr) {
        const [h,m,s] = timeStr.split(':').map(Number);
        return h*3600 + m*60 + s;
      }
      function secondsToTimeStr(totalSec) {
        const sec = Math.round(totalSec);
        const h = Math.floor(sec/3600);
        const m = Math.floor((sec%3600)/60);
        const s = sec%60;
        return `${pad(h)}:${pad(m)}:${pad(s)}`;
      }

      function loadTargetTime() {
        try {
          const stored = localStorage.getItem(STORAGE_TARGET_KEY);
          if (stored && /^\d{2}:\d{2}$/.test(stored)) {
            const [h,m] = stored.split(':').map(Number);
            if (h>=0 && h<=23 && m>=0 && m<=59) {
              targetHour = h; targetMinute = m; return stored;
            }
          }
        } catch(e) {}
        return DEFAULT_TARGET;
      }
      function saveTargetTime(timeStr) {
        try { localStorage.setItem(STORAGE_TARGET_KEY, timeStr); } catch(e) {}
      }

      function loadMomentData() {
        try {
          const raw = localStorage.getItem(STORAGE_MOMENT_KEY);
          if (raw) momentData = JSON.parse(raw);
        } catch(e) { momentData = {}; }
      }
      function saveMomentData() {
        try { localStorage.setItem(STORAGE_MOMENT_KEY, JSON.stringify(momentData)); } catch(e) {}
      }

      function getTodayTarget() {
        const now = new Date();
        return new Date(now.getFullYear(), now.getMonth(), now.getDate(), targetHour, targetMinute, 0, 0);
      }

      function updateDisplay(h,m,s) {
        const hStr = pad(h), mStr = pad(m), sStr = pad(s);
        if (hoursEl.textContent !== hStr) hoursEl.textContent = hStr;
        if (minutesEl.textContent !== mStr) minutesEl.textContent = mStr;
        if (secondsEl.textContent !== sStr) secondsEl.textContent = sStr;
      }

      function setExpiredState(expired) {
        if (isExpired === expired) return;
        isExpired = expired;
        if (expired) body.classList.add('expired');
        else {
          body.classList.remove('expired');
          body.style.backgroundColor = '';
        }
      }

      function updateMomentIcon() {
        const today = getDateStr();
        const hasRecord = !!momentData[today];
        if (hasRecord) {
          iconClock.classList.add('icon-hidden');
          iconCheck.classList.remove('icon-hidden');
        } else {
          iconClock.classList.remove('icon-hidden');
          iconCheck.classList.add('icon-hidden');
        }
      }

      function updateCountdown() {
        const now = new Date();
        const target = getTodayTarget();
        const diffMs = target - now;
        const newDateStr = getDateStr(now);

        if (currentDateStr && newDateStr !== currentDateStr) {
          setExpiredState(false);
          currentDateStr = newDateStr;
          updateMomentIcon();
          const newTarget = getTodayTarget();
          const newDiff = newTarget - now;
          if (newDiff > 0) {
            const totalSec = Math.floor(newDiff/1000);
            updateDisplay(Math.floor(totalSec/3600), Math.floor((totalSec%3600)/60), totalSec%60);
            return;
          }
        }
        if (!currentDateStr) {
          currentDateStr = newDateStr;
        }

        if (diffMs > 0) {
          setExpiredState(false);
          const totalSec = Math.floor(diffMs/1000);
          updateDisplay(Math.floor(totalSec/3600), Math.floor((totalSec%3600)/60), totalSec%60);
        } else {
          setExpiredState(true);
          updateDisplay(0,0,0);
        }
      }

      function recordMoment() {
        if (isExpired) return;
        const now = new Date();
        const today = getDateStr(now);
        const timeStr = `${pad(now.getHours())}:${pad(now.getMinutes())}:${pad(now.getSeconds())}`;
        momentData[today] = timeStr;
        saveMomentData();
        updateMomentIcon();
        if (calendarOpen) {
          const calYear = calendarYear, calMonth = calendarMonth;
          if (calYear === now.getFullYear() && calMonth === now.getMonth()) {
            renderCalendar(calYear, calMonth);
          }
        }
      }

      function getMonthStats(year, month) {
        const records = [];
        for (const [dateStr, timeStr] of Object.entries(momentData)) {
          const [y,m] = dateStr.split('-').map(Number);
          if (y === year && (m-1) === month) {
            records.push(timeToSeconds(timeStr));
          }
        }
        records.sort((a,b)=>a-b);
        const count = records.length;
        let medianStr = '--:--:--';
        if (count > 0) {
          let medianSec;
          if (count % 2 === 1) {
            medianSec = records[Math.floor(count/2)];
          } else {
            medianSec = Math.round((records[count/2-1] + records[count/2]) / 2);
          }
          medianStr = secondsToTimeStr(medianSec);
        }
        return { medianStr, count };
      }

      function renderCalendar(year, month) {
        calendarYear = year;
        calendarMonth = month;
        monthTitle.textContent = `${year}年${month+1}月`;
        const firstDay = new Date(year, month, 1);
        const lastDay = new Date(year, month+1, 0);
        const startDayOfWeek = (firstDay.getDay() + 6) % 7;
        const totalDays = lastDay.getDate();
        const todayStr = getDateStr();

        datesGrid.innerHTML = '';
        for (let i=0; i<startDayOfWeek; i++) {
          const empty = document.createElement('div');
          empty.className = 'cal-cell';
          datesGrid.appendChild(empty);
        }
        for (let d=1; d<=totalDays; d++) {
          const date = new Date(year, month, d);
          const dateStr = getDateStr(date);
          const cell = document.createElement('div');
          cell.className = 'cal-cell';
          if (dateStr === todayStr) cell.classList.add('today');
          const numSpan = document.createElement('span');
          numSpan.className = 'date-num';
          numSpan.textContent = d;
          cell.appendChild(numSpan);
          if (momentData[dateStr]) {
            const timeSpan = document.createElement('span');
            timeSpan.className = 'moment-time';
            timeSpan.textContent = momentData[dateStr];
            cell.appendChild(timeSpan);
          }
          datesGrid.appendChild(cell);
        }
        const { medianStr, count } = getMonthStats(year, month);
        statsMedian.textContent = medianStr;
        statsCount.textContent = count;
      }

      function openCalendar() {
        closeAllPanels();
        const now = new Date();
        calendarYear = now.getFullYear();
        calendarMonth = now.getMonth();
        renderCalendar(calendarYear, calendarMonth);
        overlay.classList.add('active');
        calendarPanel.classList.add('active');
        calendarOpen = true;
      }

      function closeCalendar() {
        calendarPanel.classList.remove('active');
        calendarOpen = false;
        if (!settingsPanel.classList.contains('active')) {
          overlay.classList.remove('active');
        }
      }

      function openSettings() {
        closeAllPanels();
        timeInput.value = `${pad(targetHour)}:${pad(targetMinute)}`;
        btnSave.classList.remove('saved');
        btnSave.textContent = '保存';
        if (saveFeedbackTimer) clearTimeout(saveFeedbackTimer);
        overlay.classList.add('active');
        settingsPanel.classList.add('active');
        setTimeout(() => timeInput.focus(), 150);
      }

      function closeSettings() {
        settingsPanel.classList.remove('active');
        if (!calendarOpen) overlay.classList.remove('active');
        timeInput.value = `${pad(targetHour)}:${pad(targetMinute)}`;
        if (saveFeedbackTimer) clearTimeout(saveFeedbackTimer);
        btnSave.classList.remove('saved');
        btnSave.textContent = '保存';
      }

      function saveSettings() {
        const value = timeInput.value;
        if (!value || !/^\d{2}:\d{2}$/.test(value)) {
          timeInput.value = `${pad(targetHour)}:${pad(targetMinute)}`;
          return;
        }
        const [h,m] = value.split(':').map(Number);
        if (isNaN(h) || isNaN(m) || h<0 || h>23 || m<0 || m>59) {
          timeInput.value = `${pad(targetHour)}:${pad(targetMinute)}`;
          return;
        }
        if (h === targetHour && m === targetMinute) {
          closeSettings();
          return;
        }
        targetHour = h; targetMinute = m;
        saveTargetTime(value);
        btnSave.classList.add('saved');
        btnSave.textContent = '已保存';
        if (saveFeedbackTimer) clearTimeout(saveFeedbackTimer);
        setExpiredState(false);
        currentDateStr = getDateStr();
        updateCountdown();
        saveFeedbackTimer = setTimeout(() => { closeSettings(); }, 500);
      }

      function closeAllPanels() {
        if (settingsPanel.classList.contains('active')) closeSettings();
        if (calendarOpen) closeCalendar();
        overlay.classList.remove('active');
      }

      settingsBtn.addEventListener('click', openSettings);
      overlay.addEventListener('click', closeAllPanels);
      panelClose.addEventListener('click', closeSettings);
      btnSave.addEventListener('click', saveSettings);
      momentBtn.addEventListener('click', recordMoment);
      calendarBtn.addEventListener('click', openCalendar);
      calendarClose.addEventListener('click', closeCalendar);

      prevMonthBtn.addEventListener('click', ()=>{
        if (calendarMonth === 0) { calendarYear--; calendarMonth = 11; }
        else calendarMonth--;
        renderCalendar(calendarYear, calendarMonth);
      });
      nextMonthBtn.addEventListener('click', ()=>{
        if (calendarMonth === 11) { calendarYear++; calendarMonth = 0; }
        else calendarMonth++;
        renderCalendar(calendarYear, calendarMonth);
      });

      document.addEventListener('keydown', (e) => {
        if (e.key === 'Escape') {
          if (settingsPanel.classList.contains('active') || calendarOpen) {
            closeAllPanels();
          }
        }
        if (e.key === 'Enter') {
          if (settingsPanel.classList.contains('active') && document.activeElement === timeInput) {
            e.preventDefault();
            saveSettings();
          }
        }
      });

      settingsPanel.addEventListener('click', (e) => e.stopPropagation());
      calendarPanel.addEventListener('click', (e) => e.stopPropagation());

      document.addEventListener('visibilitychange', () => {
        if (document.visibilityState === 'visible') {
          currentDateStr = getDateStr();
          setExpiredState(false);
          updateCountdown();
          updateMomentIcon();
        }
      });

      loadTargetTime();
      loadMomentData();
      timeInput.value = `${pad(targetHour)}:${pad(targetMinute)}`;
      updateMomentIcon();
      currentDateStr = getDateStr();
      updateCountdown();
      updateTimerId = setInterval(updateCountdown, 1000);

      window.addEventListener('beforeunload', () => {
        if (updateTimerId) clearInterval(updateTimerId);
        if (saveFeedbackTimer) clearTimeout(saveFeedbackTimer);
      });
    })();
  </script>
</body>
</html>
