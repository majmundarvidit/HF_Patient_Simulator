# HF_Patient_Simulator
Simulator model for cardiology fellows
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>HF Patient Simulator — Cardiology Fellowship</title>
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;600;700&family=Source+Sans+3:wght@300;400;500;600&display=swap" rel="stylesheet" />
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg:        #080e1c;
    --bg2:       #0d1628;
    --bg3:       #111e35;
    --border:    rgba(255,255,255,0.09);
    --border2:   rgba(255,255,255,0.05);
    --blue:      #2d6cdf;
    --blue-glow: rgba(45,108,223,0.35);
    --green:     #16a34a;
    --red:       #dc2626;
    --text:      #e8eeff;
    --muted:     #6b7fa8;
    --muted2:    #3a4d6e;
    --patient-bubble: rgba(20,42,88,0.75);
    --fellow-bubble:  rgba(14,60,35,0.75);
  }

  html, body { height: 100%; overflow: hidden; }

  body {
    font-family: 'Source Sans 3', sans-serif;
    background: var(--bg);
    color: var(--text);
  }

  /* ── scrollbar ── */
  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: var(--muted2); border-radius: 2px; }

  /* ════════════════════════════════════════
     SCREEN SYSTEM
  ════════════════════════════════════════ */
  .screen { display: none; height: 100vh; }
  .screen.active { display: flex; }

  /* ════════════════════════════════════════
     SCREEN 1 — API KEY
  ════════════════════════════════════════ */
  #screen-key {
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background: radial-gradient(ellipse 80% 60% at 50% 0%, rgba(45,108,223,0.12) 0%, transparent 70%), var(--bg);
    padding: 24px;
  }

  .key-card {
    background: rgba(255,255,255,0.03);
    border: 1px solid var(--border);
    border-radius: 18px;
    padding: 44px 40px;
    max-width: 460px;
    width: 100%;
    backdrop-filter: blur(20px);
  }

  .wordmark {
    font-family: 'Playfair Display', serif;
    font-size: 13px;
    letter-spacing: 3px;
    text-transform: uppercase;
    color: var(--blue);
    margin-bottom: 20px;
    display: block;
  }

  .key-title {
    font-family: 'Playfair Display', serif;
    font-size: 30px;
    font-weight: 700;
    color: var(--text);
    line-height: 1.15;
    margin-bottom: 10px;
  }

  .key-sub {
    color: var(--muted);
    font-size: 14.5px;
    line-height: 1.65;
    margin-bottom: 30px;
  }

  .field-label {
    display: block;
    font-size: 11px;
    letter-spacing: 1.2px;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 7px;
  }

  .field-input {
    width: 100%;
    background: rgba(255,255,255,0.05);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 13px 15px;
    color: var(--text);
    font-family: 'Source Sans 3', sans-serif;
    font-size: 15px;
    outline: none;
    transition: border-color .2s;
    margin-bottom: 14px;
  }
  .field-input:focus { border-color: rgba(45,108,223,0.6); }
  .field-input::placeholder { color: var(--muted2); }

  .key-help {
    font-size: 12.5px;
    color: var(--muted);
    line-height: 1.6;
    margin-bottom: 24px;
    background: rgba(45,108,223,0.07);
    border: 1px solid rgba(45,108,223,0.18);
    border-radius: 9px;
    padding: 12px 14px;
  }
  .key-help a { color: #7baeff; text-decoration: none; }
  .key-help a:hover { text-decoration: underline; }

  .btn-primary {
    width: 100%;
    background: linear-gradient(135deg, var(--blue), #1a4fbd);
    color: #fff;
    border: none;
    border-radius: 10px;
    padding: 15px;
    font-family: 'Source Sans 3', sans-serif;
    font-size: 15.5px;
    font-weight: 600;
    cursor: pointer;
    transition: all .2s;
    letter-spacing: .3px;
  }
  .btn-primary:hover:not(:disabled) {
    background: linear-gradient(135deg, #3d7cf5, #2260d4);
    box-shadow: 0 8px 28px var(--blue-glow);
    transform: translateY(-1px);
  }
  .btn-primary:disabled { opacity: .4; cursor: not-allowed; transform: none; }

  .err-msg {
    background: rgba(220,38,38,0.1);
    border: 1px solid rgba(220,38,38,0.3);
    color: #fca5a5;
    border-radius: 8px;
    padding: 10px 13px;
    font-size: 13px;
    margin-top: 10px;
    display: none;
  }

  /* ════════════════════════════════════════
     SCREEN 2 — LOGIN
  ════════════════════════════════════════ */
  #screen-login {
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background: radial-gradient(ellipse 80% 60% at 50% 0%, rgba(45,108,223,0.12) 0%, transparent 70%), var(--bg);
    padding: 24px;
  }

  .login-card {
    background: rgba(255,255,255,0.03);
    border: 1px solid var(--border);
    border-radius: 18px;
    padding: 44px 40px;
    max-width: 460px;
    width: 100%;
  }

  .case-chip {
    display: inline-block;
    background: rgba(220,38,38,0.12);
    border: 1px solid rgba(220,38,38,0.28);
    color: #fca5a5;
    font-size: 11px;
    letter-spacing: 1.4px;
    text-transform: uppercase;
    padding: 4px 12px;
    border-radius: 20px;
    margin-bottom: 20px;
  }

  .login-title {
    font-family: 'Playfair Display', serif;
    font-size: 32px;
    font-weight: 700;
    color: var(--text);
    line-height: 1.15;
    margin-bottom: 8px;
  }

  .login-sub {
    color: var(--muted);
    font-size: 14.5px;
    line-height: 1.65;
    margin-bottom: 28px;
  }

  .case-preview {
    background: rgba(45,108,223,0.07);
    border: 1px solid rgba(45,108,223,0.2);
    border-radius: 10px;
    padding: 15px 17px;
    margin-bottom: 28px;
  }
  .case-preview-label {
    font-size: 10.5px;
    letter-spacing: 1.5px;
    text-transform: uppercase;
    color: #7baeff;
    margin-bottom: 6px;
  }
  .case-preview-text {
    color: #c8d8f8;
    font-size: 14px;
    line-height: 1.55;
  }

  .phase-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 8px;
    margin-top: 20px;
  }
  .phase-chip-sm {
    background: var(--bg3);
    border: 1px solid var(--border2);
    border-radius: 9px;
    padding: 9px 12px;
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 13px;
    color: var(--muted);
  }

  /* ════════════════════════════════════════
     SCREEN 3 — SIMULATOR
  ════════════════════════════════════════ */
  #screen-sim {
    flex-direction: column;
    height: 100vh;
    overflow: hidden;
  }

  /* top bar */
  .topbar {
    background: rgba(8,14,28,0.9);
    border-bottom: 1px solid var(--border);
    padding: 10px 18px;
    display: flex;
    align-items: center;
    gap: 14px;
    flex-shrink: 0;
    backdrop-filter: blur(12px);
    flex-wrap: wrap;
  }

  .patient-badge {
    display: flex;
    align-items: center;
    gap: 11px;
    flex-shrink: 0;
  }
  .avatar {
    width: 42px; height: 42px;
    background: linear-gradient(135deg, #1a3260, #2563eb);
    border-radius: 50%;
    display: flex; align-items: center; justify-content: center;
    font-size: 20px;
    flex-shrink: 0;
  }
  .patient-name-bar {
    font-family: 'Playfair Display', serif;
    font-size: 16px;
    color: var(--text);
    font-weight: 600;
  }
  .vitals-strip {
    font-size: 11px;
    color: #ef4444;
    letter-spacing: .3px;
    margin-top: 2px;
    display: flex;
    align-items: center;
    gap: 5px;
  }
  .pulse-dot {
    width: 6px; height: 6px;
    background: #ef4444;
    border-radius: 50%;
    animation: blink 1.4s ease-in-out infinite;
  }
  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:.25} }

  .phase-tabs {
    display: flex;
    gap: 5px;
    flex: 1;
    justify-content: center;
    flex-wrap: wrap;
  }
  .phase-tab {
    padding: 6px 15px;
    border-radius: 20px;
    font-size: 12.5px;
    font-weight: 500;
    cursor: pointer;
    border: none;
    transition: all .2s;
    white-space: nowrap;
    font-family: 'Source Sans 3', sans-serif;
  }
  .phase-tab.off {
    background: rgba(255,255,255,0.05);
    color: var(--muted);
  }
  .phase-tab.off:hover { background: rgba(255,255,255,0.09); color: #aabbdd; }

  .bar-actions {
    display: flex;
    gap: 7px;
    flex-shrink: 0;
  }
  .bar-btn {
    background: rgba(255,255,255,0.06);
    border: 1px solid var(--border);
    color: var(--muted);
    padding: 7px 13px;
    border-radius: 8px;
    font-size: 12px;
    cursor: pointer;
    font-family: 'Source Sans 3', sans-serif;
    transition: all .2s;
    white-space: nowrap;
  }
  .bar-btn:hover { background: rgba(255,255,255,0.1); color: var(--text); }
  .bar-btn.red { border-color: rgba(220,38,38,0.35); color: #fca5a5; }
  .bar-btn.red:hover { background: rgba(220,38,38,0.1); }

  /* chat */
  .chat-wrap {
    flex: 1;
    overflow-y: auto;
    padding: 22px 20px;
    display: flex;
    flex-direction: column;
    gap: 18px;
  }

  .msg-row {
    display: flex;
    gap: 10px;
    max-width: 76%;
    animation: rise .28s ease;
  }
  @keyframes rise { from{opacity:0;transform:translateY(10px)} to{opacity:1;transform:none} }
  .msg-row.fellow { align-self: flex-end; flex-direction: row-reverse; }
  .msg-row.patient { align-self: flex-start; }
  .msg-row.sys { align-self: center; max-width: 90%; }

  .msg-av {
    width: 34px; height: 34px;
    border-radius: 50%;
    display: flex; align-items: center; justify-content: center;
    font-size: 15px;
    flex-shrink: 0;
    margin-top: 18px;
  }
  .msg-av.p { background: linear-gradient(135deg, #1a3260, #1e40af); }
  .msg-av.f { background: linear-gradient(135deg, #14532d, #16a34a); }

  .bubble-wrap { display: flex; flex-direction: column; gap: 3px; }
  .bubble-name {
    font-size: 11.5px;
    color: var(--muted2);
    padding: 0 4px;
  }
  .msg-row.fellow .bubble-name { text-align: right; }

  .bubble {
    padding: 12px 17px;
    border-radius: 14px;
    font-size: 14.5px;
    line-height: 1.65;
    white-space: pre-wrap;
  }
  .bubble.p {
    background: var(--patient-bubble);
    border: 1px solid rgba(45,108,223,0.22);
    color: #d6e4ff;
    border-radius: 14px 14px 14px 4px;
  }
  .bubble.f {
    background: var(--fellow-bubble);
    border: 1px solid rgba(22,163,74,0.22);
    color: #d1fae5;
    border-radius: 14px 14px 4px 14px;
  }
  .bubble.sys {
    background: rgba(255,255,255,0.04);
    border: 1px solid var(--border2);
    color: var(--muted);
    font-style: italic;
    font-size: 13px;
    border-radius: 10px;
  }

  .bubble-phase {
    font-size: 10px;
    color: var(--muted2);
    padding: 0 4px;
  }
  .msg-row.fellow .bubble-phase { text-align: right; }

  /* typing indicator */
  .typing-row {
    display: flex;
    align-self: flex-start;
    gap: 10px;
  }
  .typing-dots {
    display: flex;
    gap: 5px;
    align-items: center;
    background: var(--patient-bubble);
    border: 1px solid rgba(45,108,223,0.2);
    padding: 14px 18px;
    border-radius: 14px 14px 14px 4px;
  }
  .typing-dots span {
    width: 7px; height: 7px;
    background: #3b82f6;
    border-radius: 50%;
    animation: dot 1.2s ease-in-out infinite;
  }
  .typing-dots span:nth-child(2) { animation-delay: .2s; }
  .typing-dots span:nth-child(3) { animation-delay: .4s; }
  @keyframes dot { 0%,60%,100%{transform:translateY(0);opacity:.4} 30%{transform:translateY(-6px);opacity:1} }

  /* input */
  .input-bar {
    padding: 14px 18px;
    border-top: 1px solid var(--border);
    background: rgba(0,0,0,0.3);
    display: flex;
    gap: 11px;
    align-items: flex-end;
    flex-shrink: 0;
  }
  .input-wrap { flex: 1; }
  .phase-hint {
    font-size: 11px;
    color: var(--muted2);
    margin-bottom: 5px;
    padding-left: 2px;
    letter-spacing: .3px;
  }
  .chat-input {
    width: 100%;
    background: rgba(255,255,255,0.05);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 12px 16px;
    color: var(--text);
    font-family: 'Source Sans 3', sans-serif;
    font-size: 14.5px;
    outline: none;
    resize: none;
    min-height: 48px;
    max-height: 120px;
    line-height: 1.55;
    transition: border-color .2s;
    overflow-y: auto;
  }
  .chat-input:focus { border-color: rgba(45,108,223,0.55); }
  .chat-input::placeholder { color: var(--muted2); }

  .send-btn {
    background: linear-gradient(135deg, var(--blue), #1a4fbd);
    color: #fff;
    border: none;
    border-radius: 10px;
    width: 48px; height: 48px;
    font-size: 20px;
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    flex-shrink: 0;
    transition: all .2s;
  }
  .send-btn:hover:not(:disabled) {
    background: linear-gradient(135deg, #3d7cf5, #2260d4);
    transform: scale(1.05);
  }
  .send-btn:disabled { opacity: .35; cursor: not-allowed; }

  /* ════════════════════════════════════════
     REVEAL PANEL
  ════════════════════════════════════════ */
  .reveal-overlay {
    display: none;
    position: fixed; inset: 0;
    background: rgba(0,0,0,0.6);
    z-index: 200;
    align-items: center;
    justify-content: center;
    padding: 20px;
  }
  .reveal-overlay.open { display: flex; }

  .reveal-card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: 18px;
    padding: 32px;
    max-width: 540px;
    width: 100%;
    max-height: 85vh;
    overflow-y: auto;
    position: relative;
  }

  .reveal-title {
    font-family: 'Playfair Display', serif;
    font-size: 22px;
    color: var(--text);
    margin-bottom: 6px;
  }
  .reveal-sub { color: var(--muted); font-size: 14px; line-height: 1.6; margin-bottom: 22px; }

  .dx-box {
    width: 100%;
    background: rgba(255,255,255,0.05);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 12px 14px;
    color: var(--text);
    font-family: 'Source Sans 3', sans-serif;
    font-size: 14px;
    outline: none;
    margin-bottom: 12px;
    resize: none;
    min-height: 70px;
    line-height: 1.55;
  }
  .dx-box:focus { border-color: rgba(220,38,38,0.5); }
  .dx-box::placeholder { color: var(--muted2); }

  .btn-reveal {
    width: 100%;
    background: linear-gradient(135deg, var(--red), #b91c1c);
    color: #fff;
    border: none;
    border-radius: 10px;
    padding: 13px;
    font-family: 'Source Sans 3', sans-serif;
    font-size: 14.5px;
    font-weight: 600;
    cursor: pointer;
    transition: opacity .2s;
  }
  .btn-reveal:hover { opacity: .88; }
  .btn-reveal:disabled { opacity: .4; cursor: not-allowed; }

  .your-dx-box {
    background: rgba(22,163,74,0.1);
    border: 1px solid rgba(22,163,74,0.3);
    border-radius: 10px;
    padding: 13px 16px;
    color: #6ee7b7;
    font-size: 14px;
    line-height: 1.6;
    margin-bottom: 16px;
  }
  .hidden-dx-box {
    background: rgba(220,38,38,0.1);
    border: 1px solid rgba(220,38,38,0.3);
    border-radius: 10px;
    padding: 13px 16px;
    color: #fca5a5;
    font-size: 14.5px;
    font-weight: 500;
    line-height: 1.6;
    margin-bottom: 20px;
  }
  .section-label {
    font-size: 11px;
    letter-spacing: 1.3px;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 10px;
    margin-top: 20px;
  }
  .tp-item {
    background: var(--bg3);
    border: 1px solid var(--border2);
    border-radius: 10px;
    padding: 13px 15px;
    color: #c8d8f0;
    font-size: 14px;
    line-height: 1.65;
    margin-bottom: 9px;
  }
  .tp-item::before { content: "📌 "; }

  .close-btn {
    position: absolute;
    top: 16px; right: 16px;
    background: rgba(255,255,255,0.08);
    border: none;
    color: var(--muted);
    width: 32px; height: 32px;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    display: flex; align-items: center; justify-content: center;
    transition: background .2s;
  }
  .close-btn:hover { background: rgba(255,255,255,0.14); color: var(--text); }

  /* ════════════════════════════════════════
     LOG MODAL
  ════════════════════════════════════════ */
  .log-overlay {
    display: none;
    position: fixed; inset: 0;
    background: rgba(0,0,0,0.65);
    z-index: 200;
    align-items: center;
    justify-content: center;
    padding: 20px;
  }
  .log-overlay.open { display: flex; }

  .log-card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: 18px;
    padding: 28px;
    max-width: 580px;
    width: 100%;
    max-height: 80vh;
    overflow-y: auto;
    position: relative;
  }
  .log-title {
    font-family: 'Playfair Display', serif;
    font-size: 20px;
    color: var(--text);
    margin-bottom: 20px;
  }
  .stats-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 11px;
    margin-bottom: 22px;
  }
  .stat-box {
    background: var(--bg3);
    border: 1px solid var(--border2);
    border-radius: 10px;
    padding: 13px 14px;
  }
  .stat-label { font-size: 10.5px; letter-spacing: 1px; text-transform: uppercase; color: var(--muted); margin-bottom: 4px; }
  .stat-val { font-size: 18px; font-weight: 600; color: var(--text); }

  .phase-bar-row {
    display: flex;
    align-items: center;
    gap: 11px;
    margin-bottom: 9px;
  }
  .phase-bar-label { width: 120px; color: var(--muted); font-size: 13px; flex-shrink: 0; }
  .phase-bar-track {
    flex: 1;
    background: rgba(255,255,255,0.06);
    border-radius: 4px;
    height: 8px;
    overflow: hidden;
  }
  .phase-bar-fill { height: 100%; border-radius: 4px; transition: width .4s ease; }
  .phase-bar-count { width: 24px; text-align: right; color: var(--text); font-size: 13px; }

  /* responsive */
  @media (max-width: 640px) {
    .phase-tabs { display: none; }
    .topbar { gap: 10px; }
    .bar-btn span { display: none; }
    .key-card, .login-card { padding: 28px 22px; }
    .msg-row { max-width: 90%; }
  }
</style>
</head>
<body>

<!-- ══════════════════════════════════════════
     SCREEN 1 — API KEY ENTRY
══════════════════════════════════════════ -->
<div id="screen-key" class="screen active">
  <div class="key-card">
    <span class="wordmark">Cardiology Fellowship</span>
    <h1 class="key-title">HF Patient<br>Simulator</h1>
    <p class="key-sub">AI-powered standardized patient grounded in ACC/AHA Heart Failure &amp; Valvular guidelines. No account required — just an Anthropic API key.</p>

    <div class="key-help">
      Get a free API key at <a href="https://console.anthropic.com" target="_blank">console.anthropic.com</a> → API Keys → Create Key.<br>
      Your key is used only in your browser and never stored on any server.
    </div>

    <label class="field-label">Anthropic API Key</label>
    <input class="field-input" id="apiKeyInput" type="password" placeholder="sk-ant-api03-..." autocomplete="off" />

    <div id="keyErr" class="err-msg">Invalid key format. Anthropic API keys start with <code>sk-ant-</code>.</div>

    <button class="btn-primary" id="keyNextBtn" onclick="submitKey()">Continue →</button>
  </div>
</div>

<!-- ══════════════════════════════════════════
     SCREEN 1 — FELLOW LOGIN
══════════════════════════════════════════ -->
<div id="screen-login" class="screen">
  <div class="login-card">
    <div class="case-chip">🚨 Active Case</div>
    <h1 class="login-title">Walter Simmons<br><span style="font-size:20px;color:var(--muted)">72M · ED Resuscitation Bay</span></h1>
    <p class="login-sub">Dyspnea and syncope. Wife called 911. A fully interactive AI patient awaits your clinical evaluation.</p>

    <div class="case-preview">
      <div class="case-preview-label">Case Overview</div>
      <div class="case-preview-text">Retired football coach. Chief complaint: <strong style="color:#e0eaff">progressive dyspnea and syncope</strong>. Known heart murmur, HTN, HLD. Grounded in ACC/AHA HF &amp; Valvular Disease Guidelines.</div>
    </div>

    <label class="field-label">Your Name</label>
    <input class="field-input" id="fellowInput" type="text" placeholder="e.g., Dr. Patel" />

    <button class="btn-primary" id="startBtn" onclick="startSession()" disabled>Begin Patient Encounter →</button>

    <div class="phase-grid">
      <div class="phase-chip-sm">📋 <span>History</span></div>
      <div class="phase-chip-sm">🩺 <span>Physical Exam</span></div>
      <div class="phase-chip-sm">🔬 <span>Diagnostics</span></div>
      <div class="phase-chip-sm">💊 <span>Management</span></div>
    </div>
  </div>
</div>

<!-- ══════════════════════════════════════════
     SCREEN 3 — SIMULATOR
══════════════════════════════════════════ -->
<div id="screen-sim" class="screen">

  <!-- Top bar -->
  <div class="topbar">
    <div class="patient-badge">
      <div class="avatar">👴</div>
      <div>
        <div class="patient-name-bar">Walter Simmons</div>
        <div class="vitals-strip">
          <div class="pulse-dot"></div>
          BP 88/62 · HR 104 · SpO₂ 88% · RR 26
        </div>
      </div>
    </div>

    <div class="phase-tabs" id="phaseTabs">
      <!-- injected by JS -->
    </div>

    <div class="bar-actions">
      <button class="bar-btn" onclick="openLog()">📊 <span>Log</span></button>
      <button class="bar-btn" onclick="exportLog()">⬇ <span>Export</span></button>
      <button class="bar-btn red" onclick="openReveal()">🔍 <span>Reveal Dx</span></button>
    </div>
  </div>

  <!-- Chat -->
  <div class="chat-wrap" id="chatWrap"></div>

  <!-- Input -->
  <div class="input-bar">
    <div class="input-wrap">
      <div class="phase-hint" id="phaseHint"></div>
      <textarea class="chat-input" id="chatInput" placeholder="Ask Walter something, examine him, or order a test…" rows="1"></textarea>
    </div>
    <button class="send-btn" id="sendBtn" onclick="sendMessage()" disabled>↑</button>
  </div>
</div>

<!-- ══════════════════════════════════════════
     REVEAL PANEL
══════════════════════════════════════════ -->
<div class="reveal-overlay" id="revealOverlay">
  <div class="reveal-card">
    <button class="close-btn" onclick="closeReveal()">✕</button>
    <div class="reveal-title">Case Debrief</div>
    <div class="reveal-sub">Enter your working diagnosis before revealing the hidden answer.</div>

    <div id="dxEntrySection">
      <label class="field-label">Your Working Diagnosis</label>
      <textarea class="dx-box" id="dxInput" placeholder="e.g., Decompensated HFrEF secondary to severe AS with cardiogenic shock…"></textarea>
      <button class="btn-reveal" id="revealBtn" onclick="submitDx()">Submit &amp; Reveal Answer</button>
    </div>

    <div id="dxResultSection" style="display:none">
      <div class="section-label">Your Diagnosis</div>
      <div class="your-dx-box" id="yourDxDisplay"></div>

      <div class="section-label">Hidden Diagnosis</div>
      <div class="hidden-dx-box">Decompensated heart failure (HFrEF, EF 25–30%) secondary to severe aortic stenosis (AVA 0.75 cm², low-flow low-gradient pattern) with early cardiogenic shock (SCAI Stage C)</div>

      <div class="section-label">Teaching Points</div>
      <div class="tp-item">Severe AS (AVA &lt;1.0 cm²) causes HFrEF through chronic pressure overload → LV dilation + systolic dysfunction — the <em>"burned-out AS"</em> phenotype.</div>
      <div class="tp-item">Classic SAD triad: <strong>S</strong>yncope → 3-yr survival, <strong>A</strong>ngina → 5-yr survival, <strong>D</strong>yspnea/HF → 2-yr survival without valve intervention.</div>
      <div class="tp-item">Low-flow low-gradient AS (AVA &lt;1.0, gradient &lt;40 mmHg, EF &lt;50%) requires <strong>dobutamine stress echo</strong> to distinguish true-severe from pseudo-severe AS.</div>
      <div class="tp-item">Vasodilators (nitrates, ACEi, ARBs, hydralazine) are <strong>CONTRAINDICATED</strong> in severe AS — the fixed obstruction prevents compensatory CO increase, precipitating cardiovascular collapse.</div>
      <div class="tp-item">Diuretics must be used cautiously — the hypertrophied, non-compliant LV is preload-dependent. Aggressive diuresis → underfilling → further CO drop.</div>
      <div class="tp-item">Mechanical support (Impella CP/5.5) as a bridge to definitive therapy: <strong>TAVR</strong> (high surgical risk) or <strong>SAVR</strong> (acceptable surgical risk). Emergent TAVR increasingly performed in AS-related cardiogenic shock.</div>
      <div class="tp-item">NAPROXEN in this patient was likely contributing to fluid retention, worsening renal function (cardiorenal), and masking early decompensation. NSAIDs are contraindicated in HF.</div>
    </div>
  </div>
</div>

<!-- ══════════════════════════════════════════
     LOG MODAL
══════════════════════════════════════════ -->
<div class="log-overlay" id="logOverlay">
  <div class="log-card">
    <button class="close-btn" onclick="closeLog()">✕</button>
    <div class="log-title">Interaction Log</div>
    <div class="stats-grid" id="logStats"></div>
    <div class="section-label">Phase Breakdown</div>
    <div id="logPhaseBars"></div>
    <br>
    <button class="btn-primary" onclick="exportLog(); closeLog()">⬇ Export Full Session (JSON)</button>
  </div>
</div>

<script>
// ════════════════════════════════════════
// STATE
// ════════════════════════════════════════
let API_KEY = '';
let FELLOW  = '';
let currentPhase = 'history';
let messages = [];      // {role, content, phase, ts}
let isLoading = false;
let sessionStart = null;

const PHASES = [
  { id:'history',     label:'History',       icon:'📋', color:'#2d6cdf', desc:'Take a full history from the patient' },
  { id:'exam',        label:'Physical Exam', icon:'🩺', color:'#7c3aed', desc:'Perform physical examination' },
  { id:'diagnostics', label:'Diagnostics',   icon:'🔬', color:'#0891b2', desc:'Order and interpret investigations' },
  { id:'management',  label:'Management',    icon:'💊', color:'#16a34a', desc:'Formulate and discuss a management plan' },
];

// ════════════════════════════════════════
// HF LITERATURE RAG
// ════════════════════════════════════════
const HF_RAG = `
=== HEART FAILURE CLINICAL KNOWLEDGE BASE ===

[ACC/AHA 2022 HF Guidelines]
Stage A: At risk (risk factors, no structural disease, no symptoms)
Stage B: Structural disease, no symptoms (HFrEF or HFpEF pre-symptomatic)
Stage C: Structural disease WITH current/prior HF symptoms
Stage D: Refractory HF — advanced therapies (transplant, MCS, palliative)
HFrEF: EF <40% | HFmrEF: EF 40-49% | HFpEF: EF ≥50%

[NYHA Functional Class]
I: No symptoms with ordinary activity
II: Mild symptoms, slight limitation
III: Marked limitation, comfortable only at rest
IV: Symptoms at rest

[Decompensated HF]
Orthopnea, PND, DOE, reduced exercise tolerance
Volume overload: JVD, S3, bibasilar crackles, peripheral edema, ascites
Low output: cool extremities, narrow pulse pressure, AMS, oliguria
BNP >400 or NT-proBNP >1800 suggests decompensation
CXR: cardiomegaly, vascular redistribution, Kerley B lines, pleural effusions

[Severe Aortic Stenosis — AHA/ACC Criteria]
AVA <1.0 cm² (very severe <0.6 cm²)
Mean gradient >40 mmHg OR peak velocity >4 m/s
Classic triad (SAD): Syncope 3yr, Angina 5yr, Dyspnea/HF 2yr survival without intervention
Low-flow low-gradient AS: AVA <1.0, gradient <40, EF <50% → dobutamine stress echo to exclude pseudo-severe
Paradoxical LFLG: AVA <1.0, low gradient, PRESERVED EF → small hypertrophied LV

[Cardiogenic Shock — SCAI Classification]
A (At risk), B (Beginning shock), C (Classic), D (Deteriorating), E (Extremis)

[Forrester Hemodynamic Profiles]
Warm & Dry: Normal CI + normal PCWP → optimize oral meds
Warm & Wet: Normal CI + elevated PCWP → diuresis
Cold & Dry: Low CI + normal PCWP → careful fluids ± inotropes
Cold & Wet: Low CI + elevated PCWP (CS) → inotropes + diuresis

[Cardiogenic Shock from Severe AS]
Vasodilators (nitrates, ACEi, ARBs, hydralazine) CONTRAINDICATED — fixed obstruction, no compensatory CO increase possible
Diuretics use cautiously — preload-dependent LV
IABP: limited benefit given fixed outflow obstruction
Impella CP/5.5 or Tandem Heart: bridge to TAVR or SAVR
Emergent TAVR increasingly used in AS-related cardiogenic shock
NSAIDs (e.g., naproxen) contraindicated in HF — worsen fluid retention, renal function

[GDMT for HFrEF]
ACEi/ARB/ARNI (sacubitril-valsartan): mortality benefit; hold/avoid in severe AS until valve treated
Beta-blockers (carvedilol, metoprolol succinate, bisoprolol): mortality benefit
MRA (spironolactone/eplerenone): mortality benefit
SGLT2i (dapagliflozin/empagliflozin): HHF + CV death reduction; hold in severe AS with low output
Loop diuretics (furosemide, torsemide): symptom relief, NOT mortality

[Echo in Severe AS + HFrEF]
Thickened, calcified, restricted AV leaflets
AVA by continuity <1.0 cm²
Reduced LVEF (global hypokinesis from pressure overload — "burned-out AS")
Concentric LVH → LV dilation end-stage
E/e' >14, dilated LA, elevated RVSP, functional MR common

[Key Labs — Decompensated HF]
BNP/NT-proBNP: severity + diuretic guidance
Creatinine/BUN: cardiorenal syndrome
Lactate: tissue hypoperfusion
Troponin: demand ischemia (not always ACS)
Na+: hyponatremia = poor prognosis
AST/ALT/ALP/bili: congestive hepatopathy

[Invasive Hemodynamics — RHC]
PCWP >18 = elevated filling pressures
CI <2.2 L/min/m² = low cardiac output
SVR >1200 dynes·s·cm⁻⁵ = elevated SVR
PA sat <65% = high O2 extraction (low output state)
Fick: CO = VO2 / (CaO2 − CvO2)

=== END KNOWLEDGE BASE ===`;

// ════════════════════════════════════════
// PATIENT SYSTEM PROMPT
// ════════════════════════════════════════
function buildSystemPrompt() {
  return `You are roleplaying as Walter Simmons, a 72-year-old male presenting to the emergency department. You ARE the patient — not a doctor or AI.

HIDDEN DIAGNOSIS (never reveal directly): Decompensated heart failure (HFrEF, EF 25-30%) secondary to severe aortic stenosis (AVA 0.75 cm², low-flow low-gradient pattern) with early cardiogenic shock (SCAI Stage C).

${HF_RAG}

=== YOUR CHARACTER ===
Retired high school football coach. Tough, stoic, minimizes symptoms. Wife convinced you to come in after you collapsed walking to the mailbox this morning. Mildly confused and fatigued but oriented to person and place. Scared but hiding it.

=== YOUR STORY ===
CHIEF COMPLAINT: "I just... couldn't catch my breath. My wife called 911."

HPI:
- 4-6 months progressive exertional dyspnea — "I used to walk a mile every morning, now I can't make it to the end of the driveway"
- Orthopnea 3 months — sleeping on 3 pillows; used to sleep flat
- Two PND episodes past month — "wake up gasping around 2am"
- Bilateral leg swelling to mid-shin, worse over 6 weeks
- 12 lb weight gain over 6 weeks — "wife keeps saying my ankles look like sausages"
- Exertional chest pressure climbing one flight of stairs, resolves with rest
- Near-syncope 3 weeks ago carrying groceries — "felt lightheaded, had to sit down fast"
- TODAY: true syncope walking 20 feet to mailbox, fell, ~30 sec LOC per wife
- Reduced urine output past 2 days — "barely went yesterday"
- Mild confusion — "not quite right today"

PMH: "Heart murmur" noted age 55, told to watch; hypertension x15yr; hyperlipidemia; osteoarthritis both knees; no prior MI/PCI; no prior HF hospitalization; no AFib

MEDICATIONS: Lisinopril 10mg daily (skipping some doses — "makes me dizzy"), Amlodipine 5mg, Atorvastatin 40mg, Aspirin 81mg, Naproxen 500mg BID (3 months, for knees)

SOCIAL: Married, lives with wife; retired coach; 20 pack-year smoking, quit age 52; rare ETOH; no drugs; no travel

FAMILY: Father died "heart problems" age 68; mother had HTN

ROS (answer when asked):
- Cough: mild dry cough, especially at night
- Mild nausea today
- RUQ fullness/discomfort x2 weeks
- Decreased appetite x3-4 weeks
- Occasional palpitations — "heart fluttering," not sustained

PHYSICAL EXAM (reveal when directly asked to examine):
- Vitals: BP 88/62, HR 104 sinus, RR 26, SpO2 88% RA, T 37.1
- General: uncomfortable, mild respiratory distress, pale, diaphoretic
- Neck: JVP ~14 cm H2O
- CV: harsh 4/6 late-peaking systolic ejection murmur RUSB radiating to carotids; diminished S2; palpable thrill RUSB; S3 gallop; PMI displaced to anterior axillary line
- Pulm: bibasilar crackles to mid-lung fields; no wheeze
- Abd: hepatomegaly 4 cm below costal margin; mild RUQ tenderness; mild distension
- Ext: 3+ pitting edema bilateral mid-shin; cool mottled lower extremities; capillary refill 4 sec; weak thready radial pulses; narrow pulse pressure
- Neuro: alert but mildly lethargic, oriented x2

LABS (reveal only when specifically ordered):
- BNP 2,847 pg/mL
- Na 131, K 4.8, Cr 2.4 (est. baseline ~1.0-1.2), BUN 58, Glucose 118
- Lactate 3.8 mmol/L
- Troponin I 1.8 ng/mL (high-sensitivity)
- WBC 11.2, Hgb 11.1, Plt 198, INR 1.4
- AST 186, ALT 142, Total Bili 2.1, ALP 210, Albumin 3.1
- ABG (4L NC): pH 7.32, PaCO2 34, PaO2 58, HCO3 17

IMAGING (reveal only when ordered):
- CXR: cardiomegaly, pulmonary vascular congestion, bilateral pleural effusions R>L, Kerley B lines, interstitial edema, no infiltrate
- ECG: sinus tach 104 bpm; LVH voltage; diffuse ST depressions 1-2mm; no STE; no LBBB
- Echo: EF 25-30%; severely dilated LV; global hypokinesis; severely thickened calcified AV; AVA 0.75 cm²; mean gradient 31 mmHg (low due to low flow); moderate-severe functional MR; E/e' 22; estimated RVSP 58 mmHg; trace pericardial effusion
- Right Heart Cath (if requested): RA 18, RV 54/22, PA 58/34 (mean 44), PCWP 28, CO 2.8 L/min Fick, CI 1.5 L/min/m², SVR 1680

=== RESPONSE RULES ===
1. ALWAYS speak as Walter — casual, non-medical language. "Well doc," "my wife tells me," "I'm not sure about that"
2. Reveal info ONLY when directly asked — don't volunteer everything
3. For physical exam: describe what the fellow would find, or confirm findings naturally
4. For labs/imaging: only reveal when the fellow explicitly orders them
5. Be vague about things Walter wouldn't know (he knows "the heart doctor said my heart wasn't pumping well" — not his EF)
6. Show appropriate distress — you're sick, short of breath, scared but stoic. Keep replies short; you're dyspneic.
7. NEVER reveal the hidden diagnosis. Let fellows work toward it.
8. If a fellow proposes a dangerous treatment (IV nitrates, aggressive vasodilation, aggressive diuresis without monitoring): ask "is that safe, doc?" or "my last doctor said something about being careful with that" — do NOT lecture.
9. Keep responses 2-5 sentences usually. You're short of breath.`;
}

// ════════════════════════════════════════
// SCREEN 1 — KEY
// ════════════════════════════════════════
document.getElementById('apiKeyInput').addEventListener('input', function() {
  document.getElementById('keyNextBtn').disabled = this.value.trim().length < 10;
  document.getElementById('keyErr').style.display = 'none';
});
document.getElementById('apiKeyInput').addEventListener('keydown', e => { if(e.key==='Enter') submitKey(); });

function submitKey() {
  const val = document.getElementById('apiKeyInput').value.trim();
  if (!val.startsWith('sk-ant-') && !val.startsWith('sk-')) {
    document.getElementById('keyErr').style.display = 'block';
    return;
  }
  API_KEY = val;
  showScreen('login');
  setTimeout(() => document.getElementById('fellowInput').focus(), 200);
}

// ════════════════════════════════════════
// SCREEN 1 — LOGIN
// ════════════════════════════════════════
document.getElementById('fellowInput').addEventListener('input', function() {
  document.getElementById('startBtn').disabled = this.value.trim().length < 2;
});
document.getElementById('fellowInput').addEventListener('keydown', e => { if(e.key==='Enter') startSession(); });

function startSession() {
  FELLOW = document.getElementById('fellowInput').value.trim();
  if (!FELLOW) return;
  sessionStart = new Date().toISOString();
  buildPhaseTabs();
  showScreen('sim');
  setPhase('history');
  appendMsg({
    role: 'patient',
    content: `*Walter looks up from the gurney, pale and diaphoretic, breathing visibly labored*\n\n"Hey doc... glad you're here. I'm not... not feeling so great." *pauses to catch breath* "Wife made me come in. Name's Walter. I've been having some... trouble."`,
    phase: 'history',
    ts: new Date().toISOString()
  });
  setTimeout(() => document.getElementById('chatInput').focus(), 200);
}

// ════════════════════════════════════════
// PHASE TABS
// ════════════════════════════════════════
function buildPhaseTabs() {
  const wrap = document.getElementById('phaseTabs');
  wrap.innerHTML = '';
  PHASES.forEach(p => {
    const btn = document.createElement('button');
    btn.className = 'phase-tab off';
    btn.id = 'tab-' + p.id;
    btn.textContent = p.icon + ' ' + p.label;
    btn.onclick = () => setPhase(p.id);
    wrap.appendChild(btn);
  });
}

function setPhase(id) {
  currentPhase = id;
  const p = PHASES.find(x => x.id === id);
  PHASES.forEach(x => {
    const btn = document.getElementById('tab-' + x.id);
    if (!btn) return;
    if (x.id === id) {
      btn.className = 'phase-tab';
      btn.style.background = x.color;
      btn.style.color = '#fff';
      btn.style.boxShadow = '0 4px 16px ' + x.color + '55';
    } else {
      btn.className = 'phase-tab off';
      btn.style.background = '';
      btn.style.color = '';
      btn.style.boxShadow = '';
    }
  });
  document.getElementById('phaseHint').textContent = p.icon + ' ' + p.label + ' — ' + p.desc;
}

// ════════════════════════════════════════
// CHAT
// ════════════════════════════════════════
const chatInput = document.getElementById('chatInput');
const sendBtn   = document.getElementById('sendBtn');

chatInput.addEventListener('input', function() {
  sendBtn.disabled = isLoading || this.value.trim().length === 0;
  this.style.height = 'auto';
  this.style.height = Math.min(this.scrollHeight, 120) + 'px';
});
chatInput.addEventListener('keydown', e => {
  if (e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); sendMessage(); }
});

function appendMsg(msg) {
  messages.push(msg);
  const wrap = document.getElementById('chatWrap');

  const row = document.createElement('div');
  row.className = 'msg-row ' + msg.role;

  const phaseLabel = (PHASES.find(p => p.id === msg.phase) || {}).label || '';

  if (msg.role === 'patient') {
    row.innerHTML = `
      <div class="msg-av p">👴</div>
      <div class="bubble-wrap">
        <div class="bubble-name">Walter Simmons</div>
        <div class="bubble p">${formatBubble(msg.content)}</div>
        <div class="bubble-phase">${phaseLabel}</div>
      </div>`;
  } else if (msg.role === 'fellow') {
    row.innerHTML = `
      <div class="msg-av f">👨‍⚕️</div>
      <div class="bubble-wrap">
        <div class="bubble-name">Dr. ${FELLOW}</div>
        <div class="bubble f">${formatBubble(msg.content)}</div>
        <div class="bubble-phase">${phaseLabel}</div>
      </div>`;
  } else {
    row.innerHTML = `<div class="bubble sys">${msg.content}</div>`;
  }

  wrap.appendChild(row);
  wrap.scrollTop = wrap.scrollHeight;
}

function formatBubble(text) {
  return text
    .replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;')
    .replace(/\*([^*]+)\*/g, '<em>$1</em>')
    .replace(/\n/g, '<br>');
}

function showTyping() {
  const wrap = document.getElementById('chatWrap');
  const row = document.createElement('div');
  row.className = 'typing-row';
  row.id = 'typingRow';
  row.innerHTML = `
    <div class="msg-av p" style="margin-top:0">👴</div>
    <div class="typing-dots"><span></span><span></span><span></span></div>`;
  wrap.appendChild(row);
  wrap.scrollTop = wrap.scrollHeight;
}

function hideTyping() {
  const el = document.getElementById('typingRow');
  if (el) el.remove();
}

async function sendMessage() {
  const text = chatInput.value.trim();
  if (!text || isLoading) return;

  appendMsg({ role: 'fellow', content: text, phase: currentPhase, ts: new Date().toISOString() });
  chatInput.value = '';
  chatInput.style.height = 'auto';
  sendBtn.disabled = true;
  isLoading = true;
  showTyping();

  // Build API messages (exclude system messages)
  const apiMsgs = messages
    .filter(m => m.role === 'patient' || m.role === 'fellow')
    .map(m => ({
      role: m.role === 'fellow' ? 'user' : 'assistant',
      content: m.content
    }));

  const phaseObj = PHASES.find(p => p.id === currentPhase);
  const phaseCtx = `[Current phase: ${phaseObj.label}. Fellow is: ${phaseObj.desc}]`;

  try {
    const resp = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'x-api-key': API_KEY,
        'anthropic-version': '2023-06-01',
        'anthropic-dangerous-direct-browser-access': 'true'
      },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 800,
        system: buildSystemPrompt() + '\n\n' + phaseCtx,
        messages: apiMsgs
      })
    });

    const data = await resp.json();
    hideTyping();

    if (data.error) {
      appendMsg({ role: 'sys', content: '⚠️ API error: ' + (data.error.message || 'Unknown error. Check your API key.'), phase: currentPhase, ts: new Date().toISOString() });
    } else {
      const reply = data.content?.[0]?.text || "I... sorry, I didn't catch that.";
      appendMsg({ role: 'patient', content: reply, phase: currentPhase, ts: new Date().toISOString() });
    }
  } catch(err) {
    hideTyping();
    appendMsg({ role: 'sys', content: '⚠️ Network error. Check your connection and API key.', phase: currentPhase, ts: new Date().toISOString() });
  }

  isLoading = false;
  sendBtn.disabled = chatInput.value.trim().length === 0;
  chatInput.focus();
}

// ════════════════════════════════════════
// REVEAL
// ════════════════════════════════════════
function openReveal() {
  document.getElementById('dxEntrySection').style.display = 'block';
  document.getElementById('dxResultSection').style.display = 'none';
  document.getElementById('dxInput').value = '';
  document.getElementById('revealOverlay').classList.add('open');
}
function closeReveal() { document.getElementById('revealOverlay').classList.remove('open'); }

function submitDx() {
  const dx = document.getElementById('dxInput').value.trim();
  if (!dx) return;
  document.getElementById('yourDxDisplay').textContent = dx;
  document.getElementById('dxEntrySection').style.display = 'none';
  document.getElementById('dxResultSection').style.display = 'block';
}

// ════════════════════════════════════════
// LOG
// ════════════════════════════════════════
function openLog() {
  const fellowMsgs = messages.filter(m => m.role === 'fellow');
  const elapsed = sessionStart
    ? Math.round((Date.now() - new Date(sessionStart)) / 60000) + ' min'
    : '—';

  document.getElementById('logStats').innerHTML = [
    ['Fellow', 'Dr. ' + FELLOW],
    ['Exchanges', fellowMsgs.length],
    ['Time Elapsed', elapsed],
  ].map(([l,v]) => `<div class="stat-box"><div class="stat-label">${l}</div><div class="stat-val">${v}</div></div>`).join('');

  document.getElementById('logPhaseBars').innerHTML = PHASES.map(p => {
    const cnt = fellowMsgs.filter(m => m.phase === p.id).length;
    const pct = Math.min(cnt * 12, 100);
    return `<div class="phase-bar-row">
      <div class="phase-bar-label">${p.icon} ${p.label}</div>
      <div class="phase-bar-track"><div class="phase-bar-fill" style="width:${pct}%;background:${p.color}"></div></div>
      <div class="phase-bar-count">${cnt}</div>
    </div>`;
  }).join('');

  document.getElementById('logOverlay').classList.add('open');
}
function closeLog() { document.getElementById('logOverlay').classList.remove('open'); }

function exportLog() {
  const log = {
    session: {
      fellow: 'Dr. ' + FELLOW,
      patient: 'Walter Simmons, 72M',
      startTime: sessionStart,
      exportTime: new Date().toISOString(),
      model: 'claude-sonnet-4-20250514',
      phasesUsed: [...new Set(messages.filter(m=>m.phase).map(m=>m.phase))],
    },
    transcript: messages.map(m => ({
      role: m.role, phase: m.phase, timestamp: m.ts, content: m.content
    })),
    stats: {
      totalExchanges: messages.filter(m=>m.role==='fellow').length,
      byPhase: Object.fromEntries(PHASES.map(p=>[p.id, messages.filter(m=>m.role==='fellow'&&m.phase===p.id).length]))
    }
  };
  const blob = new Blob([JSON.stringify(log, null, 2)], {type:'application/json'});
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = `HF_Sim_${FELLOW.replace(/\s+/g,'_')}_${new Date().toISOString().split('T')[0]}.json`;
  a.click();
}

// ════════════════════════════════════════
// UTILS
// ════════════════════════════════════════
function showScreen(id) {
  document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
  document.getElementById('screen-' + id).classList.add('active');
}

// close overlays on background click
document.getElementById('revealOverlay').addEventListener('click', function(e) {
  if (e.target === this) closeReveal();
});
document.getElementById('logOverlay').addEventListener('click', function(e) {
  if (e.target === this) closeLog();
});
</script>
</body>
</html>

