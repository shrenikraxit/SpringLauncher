<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Spring Ball Launcher — Physics Simulator</title>
<style>
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
:root {
  --bg: #f0f2f5;
  --surface: #ffffff;
  --surface2: #f5f7fa;
  --border: rgba(0,0,0,0.1);
  --accent: #1a7a3c;
  --miss: #d43f1e;
  --text: #1a1d23;
  --muted: #6b7280;
  --mono: 'Courier New', Courier, monospace;
  --sans: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif;
}
body {
  background: var(--bg);
  color: var(--text);
  font-family: var(--sans);
  font-size: 14px;
  height: 100vh;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}
header {
  border-bottom: 1px solid var(--border);
  padding: 12px 24px;
  display: flex;
  align-items: center;
  gap: 16px;
  background: var(--surface);
  flex-shrink: 0;
}
header h1 {
  font-family: var(--mono);
  font-size: 13px;
  font-weight: 700;
  color: var(--accent);
  text-transform: uppercase;
  letter-spacing: 0.1em;
}
header span { font-size: 12px; color: var(--muted); }

.layout { display: flex; flex: 1; overflow: hidden; min-height: 0; }

/* LEFT PANEL */
.panel {
  width: 270px;
  min-width: 270px;
  border-right: 1px solid var(--border);
  padding: 14px;
  display: flex;
  flex-direction: column;
  gap: 14px;
  overflow-y: auto;
  background: var(--surface);
}
.sec-title {
  font-family: var(--mono);
  font-size: 9px;
  font-weight: 700;
  color: var(--muted);
  text-transform: uppercase;
  letter-spacing: 0.15em;
  margin-bottom: 9px;
}
.ctrl { margin-bottom: 10px; }
.ctrl-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 5px; }
.ctrl-name { font-size: 12px; color: var(--muted); }
.ctrl-val { font-family: var(--mono); font-size: 12px; font-weight: 700; color: var(--accent); }
input[type=range] {
  -webkit-appearance: none; appearance: none;
  width: 100%; height: 3px;
  background: rgba(0,0,0,0.15);
  outline: none; border-radius: 2px; cursor: pointer;
}
input[type=range]::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 14px; height: 14px; border-radius: 50%;
  background: var(--accent); cursor: pointer;
}
input[type=range]::-moz-range-thumb {
  width: 14px; height: 14px; border-radius: 50%;
  background: var(--accent); cursor: pointer; border: none;
}
.range-bounds { display: flex; justify-content: space-between; margin-top: 3px; }
.range-bounds span { font-size: 10px; color: var(--muted); font-family: var(--mono); }
.divider { height: 1px; background: var(--border); flex-shrink: 0; }
.presets { display: grid; grid-template-columns: 1fr 1fr; gap: 5px; margin-top: 8px; }
.preset-btn {
  background: var(--surface2);
  border: 1px solid var(--border);
  color: var(--muted);
  font-family: var(--mono); font-size: 10px;
  padding: 6px; border-radius: 4px;
  cursor: pointer; text-align: center; line-height: 1.5;
  transition: border-color 0.15s, color 0.15s;
}
.preset-btn:hover, .preset-btn.active { border-color: var(--accent); color: var(--accent); }
.info-box {
  background: var(--surface2);
  border: 1px solid var(--border);
  border-radius: 5px;
  padding: 9px 11px;
  font-size: 11px; color: var(--muted); line-height: 1.7;
}
.info-box strong { color: var(--text); font-weight: 500; }

.checkbox-row {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 12px;
  background: var(--surface2);
  border: 1px solid var(--border);
  border-radius: 5px;
  cursor: pointer;
  user-select: none;
  transition: border-color 0.15s;
}
.checkbox-row:hover { border-color: var(--accent); }
.checkbox-row input[type=checkbox] {
  width: 16px; height: 16px;
  accent-color: var(--accent);
  cursor: pointer;
  flex-shrink: 0;
}
.checkbox-row .cb-label {
  font-size: 12px; color: var(--text); font-weight: 500; flex: 1;
}
.checkbox-row .cb-sub {
  font-size: 10px; color: var(--muted); font-family: var(--mono);
}
.drag-off-badge {
  display: none;
  font-family: var(--mono); font-size: 9px; font-weight: 700;
  padding: 1px 6px; border-radius: 3px;
  background: rgba(212,63,30,0.12); color: var(--miss);
  border: 1px solid rgba(212,63,30,0.3);
  letter-spacing: 0.05em;
}
.drag-off-badge.visible { display: inline-block; }

/* MAIN AREA */
.main {
  flex: 1;
  display: flex;
  flex-direction: column;
  padding: 14px;
  gap: 10px;
  min-width: 0;
  overflow-y: auto;
}
.metrics {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 7px;
  flex-shrink: 0;
}
.metric {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 6px;
  padding: 9px 11px;
}
.metric-label {
  font-size: 10px; font-family: var(--mono);
  color: var(--muted); text-transform: uppercase;
  letter-spacing: 0.06em; margin-bottom: 4px;
}
.metric-val {
  font-family: var(--mono); font-size: 19px;
  font-weight: 700; color: var(--text); line-height: 1;
}
.metric-unit { font-size: 10px; color: var(--muted); margin-top: 2px; }
.metric-val.miss { color: var(--miss); }
.metric-val.hit  { color: var(--accent); }
#goal-badge {
  margin-top: 3px; font-family: var(--mono); font-size: 9px;
  padding: 2px 7px; border-radius: 20px;
  background: rgba(212,63,30,0.1); color: var(--miss);
  border: 1px solid rgba(212,63,30,0.3); display: inline-block;
}
#goal-badge.hit {
  background: rgba(26,122,60,0.1); color: var(--accent);
  border-color: rgba(26,122,60,0.35);
}

/* CANVAS */
.canvas-wrap {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 6px;
  overflow: hidden;
  height: 480px;
  flex-shrink: 0;
  position: relative;
}
canvas { display: block; }

.legend {
  display: flex; gap: 16px; flex-wrap: wrap; flex-shrink: 0;
}
.leg { display: flex; align-items: center; gap: 6px; font-size: 11px; color: var(--muted); font-family: var(--mono); }
.leg-line { width: 18px; height: 2px; border-radius: 1px; }
.leg-dot  { width: 8px; height: 8px; border-radius: 50%; }

/* EQUATIONS PANEL */
.equations {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 6px;
  padding: 14px 16px;
  flex-shrink: 0;
}
.equations h3 {
  font-family: var(--mono);
  font-size: 9px; font-weight: 700;
  color: var(--muted); text-transform: uppercase;
  letter-spacing: 0.15em; margin-bottom: 12px;
}
.eq-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
}
.eq-card {
  background: var(--surface2);
  border: 1px solid var(--border);
  border-radius: 5px;
  padding: 10px 12px;
}
.eq-card-title {
  font-size: 10px; color: var(--muted);
  font-family: var(--mono); margin-bottom: 6px;
  text-transform: uppercase; letter-spacing: 0.06em;
}
.eq-formula {
  font-family: var(--mono);
  font-size: 12px; color: #1a4e8a;
  margin-bottom: 5px; line-height: 1.5;
}
.eq-result {
  font-family: var(--mono);
  font-size: 13px; font-weight: 700;
  color: var(--accent); margin-top: 4px;
}
.eq-sub {
  font-size: 10px; color: var(--muted); margin-top: 2px; line-height: 1.5;
}
</style>
</head>
<body>

<header>
  <h1>Spring Launcher Simulator</h1>
  <span>Tennis Ball &nbsp;·&nbsp; Physics Engine</span>
</header>

<div class="layout">
  <aside class="panel">

    <div>
      <div class="sec-title">Spring Properties</div>
      <div class="ctrl">
        <div class="ctrl-header">
          <span class="ctrl-name">Spring constant (k)</span>
          <span class="ctrl-val" id="lbl-k">500 N/m</span>
        </div>
        <input type="range" id="sl-k" min="100" max="4000" value="500" step="10">
        <div class="range-bounds"><span>100 N/m</span><span>4000 N/m</span></div>
      </div>
      <div class="ctrl">
        <div class="ctrl-header">
          <span class="ctrl-name">Compression (x)</span>
          <span class="ctrl-val" id="lbl-x">20.0 cm</span>
        </div>
        <input type="range" id="sl-x" min="1" max="100" value="20" step="0.5">
        <div class="range-bounds"><span>1 cm</span><span>100 cm</span></div>
      </div>
      <div class="info-box" id="energy-box">
        <strong>Energy:</strong> — J &nbsp;|&nbsp; <strong>Velocity:</strong> — m/s
      </div>
    </div>

    <div class="divider"></div>

    <div>
      <div class="sec-title">Launch Angle</div>
      <div class="ctrl">
        <div class="ctrl-header">
          <span class="ctrl-name">Angle (θ)</span>
          <span class="ctrl-val" id="lbl-a">40°</span>
        </div>
        <input type="range" id="sl-a" min="5" max="85" value="40" step="1">
        <div class="range-bounds"><span>5°</span><span>85°</span></div>
      </div>
    </div>

    <div class="divider"></div>

    <div>
      <div class="sec-title">Air Resistance</div>
      <label class="checkbox-row" for="cb-drag">
        <input type="checkbox" id="cb-drag" checked>
        <div>
          <div class="cb-label">Include air drag</div>
          <div class="cb-sub">C<sub>D</sub> = 0.47, ρ = 1.225 kg/m³</div>
        </div>
        <span class="drag-off-badge" id="drag-badge">OFF</span>
      </label>
    </div>

    <div class="divider"></div>

    <div>
      <div class="sec-title">Ground Friction (μ)</div>
      <div class="ctrl">
        <div class="ctrl-header">
          <span class="ctrl-name">Coefficient</span>
          <span class="ctrl-val" id="lbl-mu">0.25</span>
        </div>
        <input type="range" id="sl-mu" min="0.05" max="0.90" value="0.25" step="0.01">
        <div class="range-bounds"><span>0.05</span><span>0.90</span></div>
      </div>
      <div class="presets">
        <button class="preset-btn" data-mu="0.12">Ice<br>μ=0.12</button>
        <button class="preset-btn active" data-mu="0.25">Short grass<br>μ=0.25</button>
        <button class="preset-btn" data-mu="0.35">Long grass<br>μ=0.35</button>
        <button class="preset-btn" data-mu="0.55">Dirt / clay<br>μ=0.55</button>
        <button class="preset-btn" data-mu="0.65">Concrete<br>μ=0.65</button>
        <button class="preset-btn" data-mu="0.80">Rubber mat<br>μ=0.80</button>
      </div>
    </div>

    <div class="divider"></div>

    <div>
      <div class="sec-title">Constants Used</div>
      <div class="info-box" style="font-size:11px">
        <strong>Ball mass:</strong> 57.7 g (ITF)<br>
        <strong>Ball radius:</strong> 33.3 mm<br>
        <strong>Drag coeff CD:</strong> 0.47<br>
        <strong>Air density ρ:</strong> 1.225 kg/m³<br>
        <strong>Restitution e:</strong> 0.72<br>
        <strong>Gravity g:</strong> 9.81 m/s²
      </div>
    </div>

  </aside>

  <main class="main">

    <!-- METRICS -->
    <div class="metrics">
      <div class="metric">
        <div class="metric-label">Flight dist.</div>
        <div class="metric-val" id="m-flight">—</div>
        <div class="metric-unit">yards</div>
      </div>
      <div class="metric">
        <div class="metric-label">Roll dist.</div>
        <div class="metric-val" id="m-roll">—</div>
        <div class="metric-unit">yards</div>
      </div>
      <div class="metric">
        <div class="metric-label">Total dist.</div>
        <div class="metric-val miss" id="m-total">—</div>
        <div class="metric-unit">yards</div>
        <div id="goal-badge">GOAL: 120 yd</div>
      </div>
      <div class="metric">
        <div class="metric-label">Peak height</div>
        <div class="metric-val" id="m-height">—</div>
        <div class="metric-unit">yards</div>
      </div>
      <div class="metric">
        <div class="metric-label">Muzzle speed</div>
        <div class="metric-val" id="m-speed">—</div>
        <div class="metric-unit">m/s</div>
      </div>
    </div>

    <!-- CANVAS -->
    <div class="canvas-wrap" id="canvas-wrap">
      <canvas id="sim-canvas"></canvas>
    </div>

    <div class="legend">
      <div class="leg"><div class="leg-line" style="background:#1565c0"></div>Flight path</div>
      <div class="leg"><div class="leg-line" style="background:#2e7d32"></div>Roll path</div>
      <div class="leg"><div class="leg-dot" style="background:#d32f2f"></div>Landing</div>
      <div class="leg"><div class="leg-dot" style="background:#2e7d32"></div>Final stop</div>
      <div class="leg"><div class="leg-dot" style="background:#1565c0"></div>Peak</div>
      <div class="leg"><div class="leg-line" style="border-top:2px dashed #c97d00;height:0;margin-top:1px"></div>&nbsp;120 yd goal</div>
      <div class="leg" id="leg-nodrag"><div class="leg-line" style="border-top:2px dashed rgba(21,101,192,0.5);height:0;margin-top:1px"></div>&nbsp;No-drag path</div>
    </div>

    <!-- EQUATIONS -->
    <div class="equations">
      <h3>Live Calculations</h3>
      <div class="eq-grid">

        <div class="eq-card">
          <div class="eq-card-title">1. Spring Energy</div>
          <div class="eq-formula">E = ½ · k · x²</div>
          <div class="eq-result" id="eq-energy">—</div>
          <div class="eq-sub" id="eq-energy-sub">—</div>
        </div>

        <div class="eq-card">
          <div class="eq-card-title">2. Muzzle Velocity</div>
          <div class="eq-formula">v₀ = √(2E / m)</div>
          <div class="eq-result" id="eq-v0">—</div>
          <div class="eq-sub" id="eq-v0-sub">—</div>
        </div>

        <div class="eq-card">
          <div class="eq-card-title">3. Velocity Components</div>
          <div class="eq-formula">vₓ = v₀·cos θ<br>v_y = v₀·sin θ</div>
          <div class="eq-result" id="eq-vcomp">—</div>
          <div class="eq-sub" id="eq-vcomp-sub">—</div>
        </div>

        <div class="eq-card">
          <div class="eq-card-title">4. Air Drag Force</div>
          <div class="eq-formula">F_d = ½·ρ·CD·A·v²</div>
          <div class="eq-result" id="eq-drag">—</div>
          <div class="eq-sub" id="eq-drag-sub">A = π·r² = 0.00349 m²</div>
          <div class="eq-sub" id="eq-drag-ratio" style="margin-top:4px;color:#d43f1e;font-weight:500"></div>
        </div>

        <div class="eq-card">
          <div class="eq-card-title">5. Ideal Flight Range</div>
          <div class="eq-formula">R = v₀²·sin(2θ) / g</div>
          <div class="eq-result" id="eq-range">—</div>
          <div class="eq-sub" id="eq-range-sub">No drag (upper bound)</div>
        </div>

        <div class="eq-card">
          <div class="eq-card-title">6. Roll Deceleration</div>
          <div class="eq-formula">a = μ · g</div>
          <div class="eq-result" id="eq-roll">—</div>
          <div class="eq-sub" id="eq-roll-sub">—</div>
        </div>

      </div>
    </div>

  </main>
</div>

<script>
// Physics constants
var MASS=0.0577, G=9.81, RHO=1.225, R_BALL=0.0333;
var CD=0.47, AREA=Math.PI*R_BALL*R_BALL, COR=0.72, SPIN=0.55, M2Y=1.09361, GOAL=120;

function getVals() {
  return {
    k:       parseFloat(document.getElementById('sl-k').value),
    xm:      parseFloat(document.getElementById('sl-x').value)/100,
    deg:     parseFloat(document.getElementById('sl-a').value),
    mu:      parseFloat(document.getElementById('sl-mu').value),
    useDrag: document.getElementById('cb-drag').checked
  };
}

function simulate() {
  var p = getVals();
  var rad = p.deg * Math.PI / 180;
  var energy = 0.5 * p.k * p.xm * p.xm;
  var v0 = Math.sqrt(2 * energy / MASS);

  // Initial velocity components - horizontal and vertical
  var vx = v0 * Math.cos(rad);
  var vy = v0 * Math.sin(rad);  // positive = upward

  var x = 0, y = 0;
  var dt = 0.002;
  var maxH = 0;
  var fp = [{x:0, y:0}];

  for (var i = 0; i < 100000; i++) {
    var speed = Math.sqrt(vx*vx + vy*vy);
    if (speed < 0.001) break;

    // Drag deceleration magnitude
    var dragAcc = p.useDrag ? (0.5 * RHO * CD * AREA * speed * speed / MASS) : 0;

    // Update velocities: drag opposes motion, gravity pulls down
    vx += (-dragAcc * (vx / speed)) * dt;
    vy += (-G - dragAcc * (vy / speed)) * dt;

    // Update positions
    x += vx * dt;
    y += vy * dt;  // y increases going up, decreases coming down

    if (y > maxH) maxH = y;

    // Sample points for drawing
    if (i % 3 === 0) fp.push({x: x, y: Math.max(y, 0)});

    // Stop when ball hits ground (y<=0) and has been airborne
    if (y <= 0 && i > 5) {
      fp.push({x: x, y: 0});
      break;
    }
  }

  var landX = x;
  var landVx = vx;
  var landVy = vy; // negative (moving downward)

  // Roll: speed from horizontal velocity after bounce + some spin contribution
  var rollV = Math.abs(landVx) * COR + Math.abs(landVy) * COR * SPIN;
  var rx = landX;
  var rp = [{x: rx}];
  var rdt = 0.005;

  for (var j = 0; j < 200000 && rollV > 0.02; j++) {
    rollV -= p.mu * G * rdt;
    if (rollV < 0) rollV = 0;
    rx += rollV * rdt;
    if (j % 8 === 0) rp.push({x: rx});
  }
  rp.push({x: rx});

  // Also compute no-drag path for comparison ghost line
  var fp2 = [{x:0,y:0}];
  var vx2 = v0 * Math.cos(rad), vy2 = v0 * Math.sin(rad);
  var x2 = 0, y2 = 0;
  for (var k2 = 0; k2 < 100000; k2++) {
    vy2 += -G * dt;
    x2 += vx2 * dt;
    y2 += vy2 * dt;
    if (k2 % 3 === 0) fp2.push({x: x2, y: Math.max(y2, 0)});
    if (y2 <= 0 && k2 > 5) { fp2.push({x: x2, y: 0}); break; }
  }

  return {
    fp: fp, fp2: fp2, rp: rp,
    landX: landX, totalX: rx,
    maxH: maxH, v0: v0,
    energy: energy,
    vx0: v0 * Math.cos(p.deg*Math.PI/180),
    vy0: v0 * Math.sin(p.deg*Math.PI/180),
    dragAtLaunch: p.useDrag ? (0.5 * RHO * CD * AREA * v0 * v0) : 0,
    useDrag: p.useDrag
  };
}

function draw() {
  var wrap = document.getElementById('canvas-wrap');
  var canvas = document.getElementById('sim-canvas');
  var W = wrap.clientWidth;
  var H = wrap.clientHeight;
  if (W < 10 || H < 10) { setTimeout(draw, 60); return; }
  canvas.width = W;
  canvas.height = H;
  var ctx = canvas.getContext('2d');

  var r = simulate();
  var p = getVals();

  // Update metric cards
  var fyd = (r.landX * M2Y).toFixed(1);
  var ryd = ((r.totalX - r.landX) * M2Y).toFixed(1);
  var tyd = (r.totalX * M2Y).toFixed(1);
  var hyd = (r.maxH * M2Y).toFixed(1);

  document.getElementById('m-flight').textContent = fyd;
  document.getElementById('m-roll').textContent = ryd;
  document.getElementById('m-height').textContent = hyd;
  document.getElementById('m-speed').textContent = r.v0.toFixed(1);

  var tv = document.getElementById('m-total');
  var badge = document.getElementById('goal-badge');
  var hit = parseFloat(tyd) >= GOAL;
  tv.textContent = tyd;
  tv.className = 'metric-val ' + (hit ? 'hit' : 'miss');
  badge.className = hit ? 'hit' : '';
  badge.textContent = hit ? '✓ GOAL REACHED!' : 'GOAL: 120 yd';

  document.getElementById('energy-box').innerHTML =
    '<strong>Energy:</strong> ' + r.energy.toFixed(2) + ' J &nbsp;|&nbsp; <strong>Velocity:</strong> ' + r.v0.toFixed(2) + ' m/s';

  // Update equation cards
  document.getElementById('eq-energy').textContent = r.energy.toFixed(2) + ' J';
  document.getElementById('eq-energy-sub').textContent =
    '½ × ' + p.k + ' × ' + (p.xm*100).toFixed(1) + 'cm² = ' + r.energy.toFixed(2) + ' J';

  document.getElementById('eq-v0').textContent = r.v0.toFixed(2) + ' m/s';
  document.getElementById('eq-v0-sub').textContent =
    '√(2 × ' + r.energy.toFixed(2) + ' / 0.0577) = ' + r.v0.toFixed(2) + ' m/s';

  document.getElementById('eq-vcomp').textContent =
    'vₓ = ' + r.vx0.toFixed(2) + ' m/s';
  document.getElementById('eq-vcomp-sub').textContent =
    'v_y = ' + r.vy0.toFixed(2) + ' m/s  (θ = ' + p.deg + '°)';

  if (r.useDrag) {
    var dragRatio = (r.dragAtLaunch / (MASS * G));
    document.getElementById('eq-drag').textContent = r.dragAtLaunch.toFixed(2) + ' N';
    document.getElementById('eq-drag-sub').textContent =
      '½ × 1.225 × 0.47 × 0.00349 × ' + r.v0.toFixed(1) + '² = ' + r.dragAtLaunch.toFixed(2) + ' N';
    document.getElementById('eq-drag').style.textDecoration = 'none';
    var ratioEl = document.getElementById('eq-drag-ratio');
    if (ratioEl) {
      ratioEl.textContent = 'Drag = ' + dragRatio.toFixed(1) + '× gravity at launch';
      ratioEl.style.color = dragRatio > 5 ? '#d43f1e' : dragRatio > 2 ? '#c97d00' : '#1a7a3c';
    }
  } else {
    document.getElementById('eq-drag').textContent = '0 N (disabled)';
    document.getElementById('eq-drag-sub').textContent = 'Air drag ignored in this simulation';
    document.getElementById('eq-drag').style.textDecoration = 'none';
    var ratioEl = document.getElementById('eq-drag-ratio');
    if (ratioEl) { ratioEl.textContent = ''; }
  }

  var idealRange = r.v0 * r.v0 * Math.sin(2 * p.deg * Math.PI / 180) / G;
  document.getElementById('eq-range').textContent = (idealRange * M2Y).toFixed(1) + ' yd (no drag)';
  document.getElementById('eq-range-sub').textContent =
    r.v0.toFixed(1) + '² × sin(' + (2*p.deg) + '°) / 9.81 = ' + idealRange.toFixed(1) + ' m';

  var rollDecel = p.mu * G;
  document.getElementById('eq-roll').textContent = rollDecel.toFixed(2) + ' m/s²';
  document.getElementById('eq-roll-sub').textContent =
    'μ ' + p.mu.toFixed(2) + ' × 9.81 = ' + rollDecel.toFixed(2) + ' m/s²';

  // ---- CANVAS DRAWING ----
  var PL=54, PR=18, PT=26, PB=34;
  var PW = W - PL - PR;
  var PH = H - PT - PB;

  // Use a single metres-per-pixel scale for BOTH axes.
  // This makes angles visually correct: 30° looks like 30°, 5° looks nearly flat.
  var maxXM = Math.max(r.totalX * 1.08, GOAL / M2Y * 1.06);
  var metersPerPixel = maxXM / PW;
  var maxYM = metersPerPixel * PH;   // same scale → equal yards per pixel on both axes

  // Convert physics coords (x right, y up) to canvas pixels
  // Canvas: x right = same direction, y down = INVERTED from physics y
  function toC(physX, physY) {
    var cx = PL + (physX / maxXM) * PW;
    // physY=0 → bottom of plot area (PT+PH), physY=maxYM → top (PT)
    var cy = PT + PH - (physY / maxYM) * PH;
    return [cx, cy];
  }

  ctx.clearRect(0, 0, W, H);

  // Sky background
  var sky = ctx.createLinearGradient(PL, PT, PL, PT + PH);
  sky.addColorStop(0, '#c8e6f7');
  sky.addColorStop(1, '#dff0fa');
  ctx.fillStyle = sky;
  ctx.fillRect(PL, PT, PW, PH);

  // Ground strip
  ctx.fillStyle = '#9e9e9e';
  ctx.fillRect(PL, PT + PH, PW, 4);

  // Grid lines
  ctx.strokeStyle = 'rgba(0,0,0,0.08)';
  ctx.lineWidth = 1;
  for (var i = 0; i <= 4; i++) {
    var ym = maxYM / 4 * i;
    var gc = toC(0, ym);
    ctx.beginPath(); ctx.moveTo(PL, gc[1]); ctx.lineTo(PL + PW, gc[1]); ctx.stroke();
  }
  for (var i = 0; i <= 6; i++) {
    var xm = maxXM / 6 * i;
    var gc = toC(xm, 0);
    ctx.beginPath(); ctx.moveTo(gc[0], PT); ctx.lineTo(gc[0], PT + PH); ctx.stroke();
  }

  // Axis labels
  ctx.fillStyle = 'rgba(60,70,85,0.9)';
  ctx.font = '10px Courier New, monospace';
  for (var i = 0; i <= 4; i++) {
    var ym = maxYM / 4 * i;
    var lc = toC(0, ym);
    ctx.textAlign = 'right';
    ctx.fillText((ym * M2Y).toFixed(0) + ' yd', PL - 5, lc[1] + 4);
  }
  for (var i = 0; i <= 6; i++) {
    var xm = maxXM / 6 * i;
    var lc = toC(xm, 0);
    ctx.textAlign = 'center';
    ctx.fillText((xm * M2Y).toFixed(0), lc[0], PT + PH + 20);
  }
  ctx.fillStyle = 'rgba(60,70,85,0.55)';
  ctx.textAlign = 'center';
  ctx.fillText('Distance (yards)', PL + PW / 2, H - 4);

  // Goal line dashed
  var glc = toC(GOAL / M2Y, 0);
  ctx.save();
  ctx.setLineDash([6, 4]);
  ctx.strokeStyle = '#c97d00'; ctx.lineWidth = 1.5;
  ctx.beginPath(); ctx.moveTo(glc[0], PT); ctx.lineTo(glc[0], PT + PH); ctx.stroke();
  ctx.restore();
  ctx.fillStyle = '#c97d00'; ctx.font = '10px Courier New, monospace';
  ctx.textAlign = 'center';
  ctx.fillText('120 yd', glc[0], PT + 13);

  // Ghost no-drag trajectory (only shown when drag is ON, as a comparison)
  if (r.useDrag && r.fp2 && r.fp2.length > 1) {
    ctx.save();
    ctx.setLineDash([5, 5]);
    ctx.beginPath();
    ctx.strokeStyle = 'rgba(21,101,192,0.28)';
    ctx.lineWidth = 1.5;
    r.fp2.forEach(function(pt, i) {
      var c2 = toC(pt.x, pt.y);
      if (i === 0) ctx.moveTo(c2[0], c2[1]);
      else ctx.lineTo(c2[0], c2[1]);
    });
    ctx.stroke();
    ctx.restore();
    // Label it
    var midIdx = Math.floor(r.fp2.length * 0.42);
    var midPt = r.fp2[midIdx];
    if (midPt) {
      var mc = toC(midPt.x, midPt.y);
      ctx.save();
      ctx.fillStyle = 'rgba(21,101,192,0.5)';
      ctx.font = '10px Courier New,monospace';
      ctx.textAlign = 'center';
      ctx.fillText('no drag', mc[0], mc[1] - 7);
      ctx.restore();
    }
  }

  // Flight path (with drag, solid)
  if (r.fp.length > 1) {
    ctx.beginPath();
    ctx.strokeStyle = '#1565c0';
    ctx.lineWidth = 2.5;
    r.fp.forEach(function(pt, i) {
      var c = toC(pt.x, pt.y);
      if (i === 0) ctx.moveTo(c[0], c[1]);
      else ctx.lineTo(c[0], c[1]);
    });
    ctx.stroke();
  }

  // Roll path
  if (r.rp.length > 1) {
    ctx.beginPath();
    ctx.strokeStyle = '#2e7d32';
    ctx.lineWidth = 2.5;
    var lrc = toC(r.landX, 0);
    ctx.moveTo(lrc[0], lrc[1]);
    r.rp.forEach(function(pt) {
      var c = toC(pt.x, 0);
      ctx.lineTo(c[0], c[1]);
    });
    ctx.stroke();
  }

  // Dots
  function dot(physX, physY, color, size) {
    var c = toC(physX, physY);
    ctx.beginPath(); ctx.arc(c[0], c[1], size || 5, 0, Math.PI * 2);
    ctx.fillStyle = color; ctx.fill();
  }

  dot(r.landX, 0, '#d32f2f', 6);   // landing
  dot(r.totalX, 0, '#2e7d32', 6);  // final stop

  // Peak point
  var peakPt = r.fp.reduce(function(a, b) { return a.y > b.y ? a : b; });
  dot(peakPt.x, peakPt.y, '#1565c0', 6);

  // Launch angle guide arrow
  // Since both axes now use the same metres-per-pixel scale, the angle in
  // physics space maps 1:1 to the angle on screen. We just use toC() directly.
  var ar = p.deg * Math.PI / 180;
  var arrowPhysLen = maxXM * 0.09;   // 9% of x-range in physical metres
  var arrowEndX = arrowPhysLen * Math.cos(ar);
  var arrowEndY = arrowPhysLen * Math.sin(ar);  // positive = upward in physics
  var orig = toC(0, 0);
  var tipC = toC(arrowEndX, arrowEndY);
  ctx.save();
  ctx.setLineDash([5, 4]);
  ctx.strokeStyle = 'rgba(20,100,40,0.7)';
  ctx.lineWidth = 1.5;
  ctx.beginPath();
  ctx.moveTo(orig[0], orig[1]);
  ctx.lineTo(tipC[0], tipC[1]);
  ctx.stroke();
  ctx.setLineDash([]);
  ctx.fillStyle = 'rgba(20,100,40,0.9)';
  ctx.font = '11px Courier New, monospace';
  ctx.textAlign = 'left';
  ctx.fillText(p.deg + '\u00b0', tipC[0] + 5, tipC[1] - 3);
  ctx.restore();
}

function updateLabels() {
  var p = getVals();
  document.getElementById('lbl-k').textContent = p.k + ' N/m';
  document.getElementById('lbl-x').textContent = (p.xm * 100).toFixed(1) + ' cm';
  document.getElementById('lbl-a').textContent = p.deg + '°';
  document.getElementById('lbl-mu').textContent = p.mu.toFixed(2);
  draw();
}

document.getElementById('cb-drag').addEventListener('change', function() {
  var badge = document.getElementById('drag-badge');
  badge.className = this.checked ? 'drag-off-badge' : 'drag-off-badge visible';
  var legNd = document.getElementById('leg-nodrag');
  if (legNd) legNd.style.display = this.checked ? '' : 'none';
  updateLabels();
});

['sl-k','sl-x','sl-a','sl-mu'].forEach(function(id) {
  document.getElementById(id).addEventListener('input', function() {
    if (id === 'sl-mu') {
      document.querySelectorAll('.preset-btn').forEach(function(b) { b.classList.remove('active'); });
    }
    updateLabels();
  });
});

document.querySelectorAll('.preset-btn').forEach(function(btn) {
  btn.addEventListener('click', function() {
    document.querySelectorAll('.preset-btn').forEach(function(b) { b.classList.remove('active'); });
    btn.classList.add('active');
    document.getElementById('sl-mu').value = btn.getAttribute('data-mu');
    updateLabels();
  });
});

window.addEventListener('resize', draw);
window.addEventListener('load', function() { setTimeout(updateLabels, 60); });
</script>
</body>
</html>
