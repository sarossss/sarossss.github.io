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
      transition: background var(--transition-fast); outline: 
