# jasper561.github.io
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Shinkei Fish Biologger</title>
<style>
  /* ── Tokens ─────────────────────────────────────────── */
  :root {
    --ocean:   #0a1628;   /* deep water navy */
    --surface: #0e2040;   /* mid-water blue  */
    --panel:   #132a52;   /* panel background */
    --accent:  #00c2ff;   /* bioluminescent cyan */
    --warm:    #ff7043;   /* thermal/internal temp */
    --cool:    #29b6f6;   /* ambient/water temp */
    --good:    #26a69a;   /* success teal */
    --warn:    #ffa726;   /* warning amber */
    --danger:  #ef5350;   /* error red */
    --text:    #e8f4f8;
    --muted:   #7899aa;
    --mono:    'Courier New', monospace;
    --sans:    'Segoe UI', system-ui, sans-serif;
    --radius:  10px;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--ocean);
    color: var(--text);
    font-family: var(--sans);
    min-height: 100vh;
    padding: 24px 16px 40px;
  }

  /* ── Header ─────────────────────────────────────────── */
  header {
    text-align: center;
    margin-bottom: 28px;
  }
  header .eyebrow {
    font-size: 11px;
    letter-spacing: 3px;
    text-transform: uppercase;
    color: var(--accent);
    margin-bottom: 6px;
  }
  header h1 {
    font-size: clamp(22px, 5vw, 32px);
    font-weight: 700;
    letter-spacing: -0.5px;
    color: var(--text);
  }
  header .depth-line {
    width: 48px;
    height: 2px;
    background: var(--accent);
    margin: 12px auto 0;
    border-radius: 2px;
  }

  /* ── Layout ─────────────────────────────────────────── */
  .app { max-width: 720px; margin: 0 auto; }

  .card {
    background: var(--panel);
    border: 1px solid rgba(0,194,255,0.12);
    border-radius: var(--radius);
    padding: 20px;
    margin-bottom: 16px;
  }
  .card-label {
    font-size: 10px;
    letter-spacing: 2px;
    text-transform: uppercase;
    color: var(--accent);
    margin-bottom: 14px;
    display: flex;
    align-items: center;
    gap: 8px;
  }
  .card-label::after {
    content: '';
    flex: 1;
    height: 1px;
    background: rgba(0,194,255,0.15);
  }

  /* ── Connect Button ──────────────────────────────────── */
  #btnConnect {
    display: block;
    width: 100%;
    padding: 16px;
    background: var(--accent);
    color: var(--ocean);
    border: none;
    border-radius: var(--radius);
    font-size: 16px;
    font-weight: 700;
    letter-spacing: 0.5px;
    cursor: pointer;
    transition: opacity 0.15s, transform 0.1s;
  }
  #btnConnect:hover { opacity: 0.88; }
  #btnConnect:active { transform: scale(0.98); }
  #btnConnect.connected {
    background: var(--good);
    color: #fff;
  }

  /* ── Status Bar ──────────────────────────────────────── */
  .status-bar {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px 16px;
    background: var(--surface);
    border-radius: var(--radius);
    margin-bottom: 16px;
    font-size: 13px;
  }
  .status-dot {
    width: 9px; height: 9px;
    border-radius: 50%;
    background: var(--muted);
    flex-shrink: 0;
    transition: background 0.3s;
  }
  .status-dot.ble-on  { background: var(--good); box-shadow: 0 0 8px var(--good); }
  .status-dot.logging { background: var(--warn); box-shadow: 0 0 8px var(--warn);
                        animation: pulse 1.5s infinite; }
  @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.4} }
  #statusText { color: var(--muted); }

  /* ── Control Buttons ─────────────────────────────────── */
  .controls {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
  }
  .controls button {
    padding: 13px 10px;
    border: none;
    border-radius: var(--radius);
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: opacity 0.15s, transform 0.1s;
    color: #fff;
  }
  .controls button:hover:not(:disabled) { opacity: 0.85; }
  .controls button:active:not(:disabled) { transform: scale(0.97); }
  .controls button:disabled { opacity: 0.35; cursor: not-allowed; }
  #btnStart  { background: var(--good); }
  #btnStop   { background: var(--warn); color: var(--ocean); }
  #btnLive   { background: var(--accent); color: var(--ocean); }
  #btnStatus { background: var(--surface); border: 1px solid rgba(0,194,255,0.25); }
  #btnDump   { background: #7c4dff; grid-column: span 2; }
  #btnErase  { background: var(--danger); grid-column: span 2; }

  /* ── Progress Bar ────────────────────────────────────── */
  .progress-wrap { display: none; margin-bottom: 16px; }
  .progress-wrap.active { display: block; }
  .progress-label { font-size: 12px; color: var(--muted); margin-bottom: 6px;
                    display: flex; justify-content: space-between; }
  .progress-bar { height: 6px; background: var(--surface); border-radius: 3px; overflow: hidden; }
  .progress-fill { height: 100%; background: var(--accent);
                   border-radius: 3px; transition: width 0.3s; width: 0%; }

  /* ── Terminal ────────────────────────────────────────── */
  #terminal {
    background: #060f1e;
    color: #57e8c4;
    font-family: var(--mono);
    font-size: 12.5px;
    line-height: 1.6;
    padding: 14px;
    border-radius: var(--radius);
    height: 300px;
    overflow-y: auto;
    white-space: pre-wrap;
    word-break: break-all;
    border: 1px solid rgba(0,194,255,0.1);
  }
  .t-muted  { color: var(--muted); }
  .t-warn   { color: var(--warn); }
  .t-danger { color: var(--danger); }
  .t-good   { color: var(--good); }
  .t-live   { color: var(--accent); font-weight: bold; }

  /* ── Erase confirm overlay ───────────────────────────── */
  .confirm-banner {
    display: none;
    background: rgba(239,83,80,0.15);
    border: 1px solid var(--danger);
    border-radius: var(--radius);
    padding: 14px;
    margin-bottom: 12px;
    font-size: 13px;
    text-align: center;
  }
  .confirm-banner.show { display: block; }
  .confirm-banner strong { color: var(--danger); }
  .confirm-banner .confirm-btns { margin-top: 10px; display: flex; gap: 8px; justify-content: center; }
  .confirm-banner button { padding: 8px 20px; border-radius: 6px; border: none;
                           font-weight: 600; cursor: pointer; font-size: 13px; }
  .btn-confirm-yes { background: var(--danger); color: #fff; }
  .btn-confirm-no  { background: var(--surface); color: var(--text); }

  /* ── Record count badge ──────────────────────────────── */
  .badge {
    display: inline-block;
    background: rgba(0,194,255,0.15);
    color: var(--accent);
    font-size: 11px;
    font-weight: 700;
    letter-spacing: 1px;
    padding: 2px 8px;
    border-radius: 20px;
    margin-left: 8px;
  }
</style>
</head>
<body>
<div class="app">

  <header>
    <div class="eyebrow">Shinkei Biologger</div>
    <h1>Fish Temperature Logger</h1>
    <div class="depth-line"></div>
  </header>

  <button id="btnConnect">Connect via Bluetooth</button>

  <div class="status-bar" style="margin-top:14px;">
    <div class="status-dot" id="statusDot"></div>
    <span id="statusText">Not connected</span>
    <span class="badge" id="recBadge" style="display:none"></span>
  </div>

  <div class="card">
    <div class="card-label">Controls</div>

    <div class="confirm-banner" id="eraseBanner">
      <strong>⚠ This will permanently erase all logged data.</strong><br>
      Are you sure? This cannot be undone.
      <div class="confirm-btns">
        <button class="btn-confirm-yes" id="btnEraseYes">Yes, erase</button>
        <button class="btn-confirm-no"  id="btnEraseNo">Cancel</button>
      </div>
    </div>

    <div class="controls">
      <button id="btnStart"  disabled>▶ Start Logging</button>
      <button id="btnStop"   disabled>■ Stop Logging</button>
      <button id="btnLive"   disabled>⚡ Live Readout</button>
      <button id="btnStatus" disabled>ℹ Status</button>
      <button id="btnDump"   disabled>⬇ Download CSV</button>
      <button id="btnErase"  disabled>🗑 Erase Flash</button>
    </div>
  </div>

  <div class="progress-wrap" id="progressWrap">
    <div class="progress-label">
      <span>Downloading data over BLE…</span>
      <span id="progressCount">0 records</span>
    </div>
    <div class="progress-bar"><div class="progress-fill" id="progressFill"></div></div>
  </div>

  <div class="card">
    <div class="card-label">Log</div>
    <div id="terminal"><span class="t-muted">Ready. Press Connect to begin.\n</span></div>
  </div>

</div>

<script>
// ── UUIDs (must match Arduino sketch) ────────────────────────────────
const SVC_UUID  = "19b10000-e8f2-537e-4f6c-d104768a1214";
const CMD_UUID  = "19b10001-e8f2-537e-4f6c-d104768a1214";
const DATA_UUID = "19b10002-e8f2-537e-4f6c-d104768a1214";

// ── State ─────────────────────────────────────────────────────────────
let device, cmdChar, dataChar;
let csvBuffer   = "";
let isDumping   = false;
const MAX_RECS  = 149000;

// ── DOM refs ──────────────────────────────────────────────────────────
const term         = document.getElementById('terminal');
const statusDot    = document.getElementById('statusDot');
const statusText   = document.getElementById('statusText');
const recBadge     = document.getElementById('recBadge');
const progressWrap = document.getElementById('progressWrap');
const progressFill = document.getElementById('progressFill');
const progressCnt  = document.getElementById('progressCount');
const eraseBanner  = document.getElementById('eraseBanner');

// ── Terminal helpers ──────────────────────────────────────────────────
function log(msg, cls = '') {
  const span = document.createElement('span');
  if (cls) span.className = cls;
  span.textContent = msg;
  term.appendChild(span);
  term.scrollTop = term.scrollHeight;
}
function logLine(msg, cls = '') { log(msg + '\n', cls); }

// ── BLE Connect ───────────────────────────────────────────────────────
document.getElementById('btnConnect').addEventListener('click', async () => {
  if (device && device.gatt.connected) {
    device.gatt.disconnect();
    return;
  }
  try {
    logLine('Scanning for FishLogger…', 't-muted');
    device = await navigator.bluetooth.requestDevice({
      filters: [{ name: 'FishLogger' }],
      optionalServices: [SVC_UUID]
    });
    device.addEventListener('gattserverdisconnected', onDisconnect);

    logLine('Connecting…', 't-muted');
    const server  = await device.gatt.connect();
    const service = await server.getPrimaryService(SVC_UUID);
    cmdChar  = await service.getCharacteristic(CMD_UUID);
    dataChar = await service.getCharacteristic(DATA_UUID);
    await dataChar.startNotifications();
    dataChar.addEventListener('characteristicvaluechanged', onData);

    onConnect();
  } catch (e) {
    logLine('Connection failed: ' + e, 't-danger');
  }
});

function onConnect() {
  statusDot.className  = 'status-dot ble-on';
  statusText.textContent = 'Connected — ' + device.name;
  document.getElementById('btnConnect').textContent = 'Disconnect';
  document.getElementById('btnConnect').classList.add('connected');
  setButtons(true);
  logLine('Connected to ' + device.name, 't-good');
  // Auto-request status on connect
  sendCmd('STATUS');
}

function onDisconnect() {
  statusDot.className    = 'status-dot';
  statusText.textContent = 'Disconnected';
  document.getElementById('btnConnect').textContent = 'Connect via Bluetooth';
  document.getElementById('btnConnect').classList.remove('connected');
  setButtons(false);
  isDumping = false;
  progressWrap.classList.remove('active');
  logLine('Device disconnected.', 't-warn');
}

// ── Incoming BLE data ─────────────────────────────────────────────────
// ── Incoming BLE data ─────────────────────────────────────────────────
function onData(event) {
  const chunk = new TextDecoder().decode(event.target.value);

  if (isDumping) {
    csvBuffer += chunk;

    // Count newlines as proxy for records
    const lines = (csvBuffer.match(/\n/g) || []).length - 1; 
    const pct   = Math.min((lines / MAX_RECS) * 100, 99);
    progressFill.style.width = pct + '%';
    progressCnt.textContent  = lines + ' records';

    if (csvBuffer.includes('EOF')) {
      csvBuffer = csvBuffer.substring(0, csvBuffer.indexOf('EOF'));
      isDumping = false;
      progressFill.style.width = '100%';
      progressCnt.textContent  = lines + ' records — complete';
      logLine('Download complete — saving file.', 't-good');
      setTimeout(() => progressWrap.classList.remove('active'), 2000);
      downloadCSV(csvBuffer);
      updateBadge(lines);
    }
    return;
  }

  // ── Accumulate normal BLE chunks until we hit a newline ──
  if (typeof window.bleTextBuffer === 'undefined') window.bleTextBuffer = "";
  window.bleTextBuffer += chunk;

  let newlineIdx;
  while ((newlineIdx = window.bleTextBuffer.indexOf('\n')) !== -1) {
    // Extract the full complete string
    let fullLine = window.bleTextBuffer.substring(0, newlineIdx).trim();
    window.bleTextBuffer = window.bleTextBuffer.substring(newlineIdx + 1);

    if (fullLine.startsWith('LIVE|')) {
      parseLive(fullLine);
    } 
    else if (fullLine.startsWith('STATUS:')) {
      parseStatus(fullLine);
      logLine('[Logger]: ' + fullLine, 't-muted');
    } 
    else {
      logLine('[Logger]: ' + fullLine);
    }
  }
}

// ── Parse live readout ────────────────────────────────────────────────
function parseLive(str) {
  // Format: LIVE|core|amb|fakeX|0|0
  const parts = str.trim().split('|');
  
  if (parts.length >= 4) {
    const core = parseFloat(parts[1]);
    const amb  = parseFloat(parts[2]);
    const mag  = parseInt(parts[3]); // We pre-calculated the magnitude on the Arduino!
    
    const coreStr = core === -99 ? '--.-' : core.toFixed(2);
    const ambStr  = amb  === -99 ? '--.-' : amb.toFixed(2);
    
    // Log beautifully formatted string to terminal
    logLine(`[Live]: Core=${coreStr}°C | Amb=${ambStr}°C | Act=${mag}mg`, 't-live');
  }
}

// ── Parse status ──────────────────────────────────────────────────────
function parseStatus(str) {
  const recs = str.match(/Records:\s*(\d+)/);
  if (recs) updateBadge(parseInt(recs[1]));
  const logging = str.includes('LOGGING: YES') || str.includes('LOGGING'); // Adjusting for both formats
  statusDot.className = 'status-dot ble-on' + (logging ? ' logging' : '');
  statusText.textContent = logging ? 'Connected — Logging active' : 'Connected — Idle';
}

function updateBadge(n) {
  recBadge.style.display = 'inline-block';
  recBadge.textContent   = n + ' records';
}

// ── Button wiring ─────────────────────────────────────────────────────
document.getElementById('btnStart').addEventListener('click', () => {
  const t = Math.floor(Date.now() / 1000);
  sendCmd('START:' + t);
  statusDot.classList.add('logging');
  statusText.textContent = 'Connected — Logging active';
});

document.getElementById('btnStop').addEventListener('click', () => {
  sendCmd('STOP');
  statusDot.classList.remove('logging');
  statusText.textContent = 'Connected — Idle';
});

document.getElementById('btnLive').addEventListener('click', () => {
  sendCmd('LIVE');
});

document.getElementById('btnStatus').addEventListener('click', () => {
  sendCmd('STATUS');
});

document.getElementById('btnDump').addEventListener('click', () => {
  csvBuffer = '';
  isDumping = true;
  progressWrap.classList.add('active');
  progressFill.style.width = '0%';
  progressCnt.textContent  = '0 records';
  logLine('Starting download…', 't-muted');
  sendCmd('DUMP');
});

// Erase — show inline confirm banner instead of browser dialog
document.getElementById('btnErase').addEventListener('click', () => {
  eraseBanner.classList.add('show');
});
document.getElementById('btnEraseYes').addEventListener('click', () => {
  eraseBanner.classList.remove('show');
  sendCmd('ERASE');
  // Arduino requires ERASE sent twice — send second after 1s
  setTimeout(() => {
    sendCmd('ERASE');
    logLine('Erase confirmed — wiping flash…', 't-warn');
    recBadge.style.display = 'none';
  }, 1000);
});
document.getElementById('btnEraseNo').addEventListener('click', () => {
  eraseBanner.classList.remove('show');
  logLine('Erase cancelled.', 't-muted');
});

// ── Send BLE command ──────────────────────────────────────────────────
async function sendCmd(cmd) {
  if (!cmdChar) return;
  logLine('→ ' + cmd, 't-muted');
  const enc = new TextEncoder();
  await cmdChar.writeValue(enc.encode(cmd));
}

// ── Download CSV file ─────────────────────────────────────────────────
function downloadCSV(csv) {
  const blob = new Blob([csv], { type: 'text/csv' });
  const url  = URL.createObjectURL(blob);
  const a    = document.createElement('a');
  a.href     = url;
  a.download = 'FishLogger_' + new Date().toISOString().split('T')[0] + '.csv';
  document.body.appendChild(a);
  a.click();
  document.body.removeChild(a);
  URL.revokeObjectURL(url);
}

// ── Enable/disable control buttons ───────────────────────────────────
function setButtons(on) {
  ['btnStart','btnStop','btnLive','btnStatus','btnDump','btnErase']
    .forEach(id => document.getElementById(id).disabled = !on);
}
</script>
</body>
</html>
