
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>∞ Inventory Sheet</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;600&family=Syne:wght@400;600;700;800&display=swap');

  :root {
    --bg: #0e0f11;
    --surface: #13151a;
    --surface2: #1a1d24;
    --border: #252830;
    --border-light: #1e2028;
    --accent: #00e5a0;
    --accent-dim: rgba(0,229,160,0.10);
    --text: #dde1ea;
    --text-dim: #5a6070;
    --text-muted: #30343e;
    --header-bg: #0e0f11;
    --row-hover: rgba(255,255,255,0.018);
    --row-alt: rgba(255,255,255,0.008);
    --rem-pos: #34d399;
    --rem-neg: #f87171;
    --col-idx: 50px;
    --col-code: 120px;
    --col-name: 230px;
    --col-num: 140px;
    --col-rem: 140px;
    --col-del: 40px;
    --row-h: 40px;
    --header-h: 46px;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'JetBrains Mono', monospace;
    font-size: 13px;
    height: 100vh;
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }

  #topbar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 20px;
    height: 54px;
    background: var(--surface);
    border-bottom: 1px solid var(--border);
    flex-shrink: 0;
    gap: 16px;
  }

  #logo {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 20px;
    color: var(--accent);
    letter-spacing: -0.5px;
    display: flex;
    align-items: baseline;
    gap: 8px;
    white-space: nowrap;
  }
  #logo span {
    font-size: 10px;
    font-weight: 500;
    color: var(--text-dim);
    letter-spacing: 1.5px;
    font-family: 'JetBrains Mono', monospace;
  }

  #topbar-right {
    display: flex;
    align-items: center;
    gap: 10px;
    flex-wrap: wrap;
  }

  .chip {
    display: flex;
    align-items: center;
    gap: 5px;
    padding: 5px 12px;
    border-radius: 6px;
    background: var(--surface2);
    border: 1px solid var(--border);
    font-size: 11px;
    white-space: nowrap;
  }
  .chip .lbl { color: var(--text-dim); text-transform: uppercase; letter-spacing: 0.7px; font-size: 9px; }
  .chip .val { color: var(--accent); font-weight: 600; }
  .chip .val.neg { color: var(--rem-neg); }

  #btn-add {
    display: flex;
    align-items: center;
    gap: 6px;
    padding: 8px 18px;
    background: var(--accent);
    color: #0a0b0d;
    border: none;
    border-radius: 6px;
    font-family: 'Syne', sans-serif;
    font-weight: 700;
    font-size: 12px;
    letter-spacing: 0.5px;
    cursor: pointer;
    transition: opacity 0.15s, transform 0.1s;
    white-space: nowrap;
  }
  #btn-add:hover { opacity: 0.85; transform: translateY(-1px); }
  #btn-add:active { transform: translateY(0); }

  #sheet-wrapper {
    flex: 1;
    overflow: auto;
    position: relative;
  }
  #sheet-wrapper::-webkit-scrollbar { width: 8px; height: 8px; }
  #sheet-wrapper::-webkit-scrollbar-track { background: var(--surface); }
  #sheet-wrapper::-webkit-scrollbar-thumb { background: var(--border); border-radius: 4px; }
  #sheet-wrapper::-webkit-scrollbar-thumb:hover { background: #3a3e4a; }
  #sheet-wrapper::-webkit-scrollbar-corner { background: var(--surface); }

  table { border-collapse: collapse; width: 100%; }

  thead { position: sticky; top: 0; z-index: 20; }
  thead tr { background: var(--header-bg); border-bottom: 2px solid var(--border); }

  th {
    height: var(--header-h);
    padding: 0 12px;
    font-family: 'Syne', sans-serif;
    font-size: 10px;
    font-weight: 700;
    letter-spacing: 1.2px;
    color: var(--text-dim);
    text-transform: uppercase;
    text-align: left;
    border-right: 1px solid var(--border-light);
    user-select: none;
    white-space: nowrap;
  }
  th:last-child { border-right: none; }
  th.th-idx  { width: var(--col-idx);  text-align: center; color: var(--text-muted); }
  th.th-code { width: var(--col-code); }
  th.th-name { width: var(--col-name); }
  th.th-qty  { width: var(--col-num);  text-align: right; }
  th.th-sold { width: var(--col-num);  text-align: right; }
  th.th-rem  { width: var(--col-rem);  text-align: right; color: var(--accent); }
  th.th-del  { width: var(--col-del); }

  tbody tr { border-bottom: 1px solid var(--border-light); transition: background 0.08s; }
  tbody tr:nth-child(even) { background: var(--row-alt); }
  tbody tr:hover { background: var(--row-hover); }
  tbody tr:focus-within { background: rgba(0,229,160,0.025); }

  td {
    padding: 0 6px;
    height: var(--row-h);
    border-right: 1px solid var(--border-light);
    vertical-align: middle;
  }
  td:last-child { border-right: none; }
  td.td-idx { text-align: center; font-size: 10px; color: var(--text-muted); font-weight: 600; user-select: none; }

  input.ci {
    width: 100%;
    background: transparent;
    border: none;
    outline: none;
    color: var(--text);
    font-family: 'JetBrains Mono', monospace;
    font-size: 13px;
    padding: 5px 8px;
    border-radius: 5px;
    transition: background 0.12s, box-shadow 0.12s;
    caret-color: var(--accent);
  }
  input.ci::placeholder { color: var(--text-muted); font-size: 11px; }
  input.ci:focus { background: rgba(0,229,160,0.06); box-shadow: 0 0 0 1.5px rgba(0,229,160,0.30); }
  input.ci.ci-code { font-weight: 600; letter-spacing: 0.5px; color: #c4b5fd; }
  input.ci.ci-num  { text-align: right; color: #93c5fd; }

  td.td-rem {
    text-align: right;
    padding: 0 14px;
    font-weight: 600;
    font-size: 13px;
    font-variant-numeric: tabular-nums;
    transition: color 0.2s;
  }
  td.td-rem.empty { color: var(--text-muted); }
  td.td-rem.pos   { color: var(--rem-pos); }
  td.td-rem.neg   { color: var(--rem-neg); }
  td.td-rem.zero  { color: var(--text-dim); }

  td.td-del { text-align: center; }
  .btn-del {
    background: none; border: none; color: var(--text-muted);
    cursor: pointer; padding: 4px 7px; border-radius: 4px;
    font-size: 13px; transition: color 0.1s, background 0.1s; opacity: 0;
  }
  tr:hover .btn-del, tr:focus-within .btn-del { opacity: 1; }
  .btn-del:hover { color: var(--rem-neg); background: rgba(248,113,113,0.12); }

  #empty-state { text-align: center; padding: 70px 20px; color: var(--text-muted); }
  #empty-state p { font-size: 15px; margin-bottom: 8px; color: var(--text-dim); }
  #empty-state small { font-size: 11px; }

  #status-bar {
    display: flex; align-items: center; gap: 20px;
    padding: 0 20px; height: 32px;
    background: var(--surface); border-top: 1px solid var(--border);
    flex-shrink: 0; font-size: 11px; color: var(--text-dim);
  }
  .sb { display: flex; align-items: center; gap: 5px; }
  .sb-lbl { color: var(--text-muted); font-size: 10px; text-transform: uppercase; letter-spacing: 0.6px; }
  .sb-val { color: var(--accent); font-weight: 600; }
  .sb-hint { margin-left: auto; font-size: 10px; color: var(--text-muted); }

  #save-badge {
    position: fixed; bottom: 44px; right: 16px;
    padding: 6px 14px;
    background: rgba(0,229,160,0.15);
    border: 1px solid rgba(0,229,160,0.35);
    border-radius: 20px; color: var(--accent);
    font-size: 11px; font-weight: 600; letter-spacing: 0.5px;
    opacity: 0; transform: translateY(6px);
    transition: opacity 0.2s, transform 0.2s;
    pointer-events: none; z-index: 999;
  }
  #save-badge.visible { opacity: 1; transform: translateY(0); }

  #btn-clear {
    padding: 7px 14px; background: transparent;
    color: var(--text-dim); border: 1px solid var(--border);
    border-radius: 6px; font-family: 'JetBrains Mono', monospace;
    font-size: 11px; cursor: pointer;
    transition: color 0.15s, border-color 0.15s, background 0.15s;
    white-space: nowrap;
  }
  #btn-clear:hover { color: var(--rem-neg); border-color: rgba(248,113,113,0.4); background: rgba(248,113,113,0.07); }
</style>
</head>
<body>

<div id="topbar">
  <div id="logo">∞ Inventory <span>LIVE SHEET</span></div>
  <div id="topbar-right">
    <div class="chip"><span class="lbl">Rows</span><span class="val" id="c-rows">0</span></div>
    <div class="chip"><span class="lbl">Total Qty</span><span class="val" id="c-qty">0</span></div>
    <div class="chip"><span class="lbl">Total Sold</span><span class="val" id="c-sold">0</span></div>
    <div class="chip"><span class="lbl">Remaining</span><span class="val" id="c-rem">0</span></div>
    <button id="btn-clear" onclick="clearAll()">✕ Clear All</button>
    <button id="btn-add" onclick="addRow()">＋ Add Row</button>
  </div>
</div>

<div id="sheet-wrapper">
  <table id="table">
    <thead>
      <tr>
        <th class="th-idx">#</th>
        <th class="th-code">Code</th>
        <th class="th-name">Name</th>
        <th class="th-qty">Quantity</th>
        <th class="th-sold">Sold</th>
        <th class="th-rem">Remaining</th>
        <th class="th-del"></th>
      </tr>
    </thead>
    <tbody id="tbody"></tbody>
  </table>
  <div id="empty-state" style="display:none">
    <p>No rows yet</p>
    <small>Click <strong style="color:var(--accent)">＋ Add Row</strong> to start adding inventory</small>
  </div>
</div>

<div id="status-bar">
  <div class="sb"><span class="sb-lbl">Items</span><span class="sb-val" id="s-rows">0</span></div>
  <div class="sb"><span class="sb-lbl">Total Qty</span><span class="sb-val" id="s-qty">0</span></div>
  <div class="sb"><span class="sb-lbl">Total Sold</span><span class="sb-val" id="s-sold">0</span></div>
  <div class="sb"><span class="sb-lbl">Total Remaining</span><span class="sb-val" id="s-rem">0</span></div>
  <span class="sb-hint">TAB / ENTER → next cell &nbsp;·&nbsp; ↑↓ → move rows &nbsp;·&nbsp; Remaining = Quantity − Sold</span>
</div>

<div id="save-badge">✓ Saved</div>

<script>
const tbody = document.getElementById('tbody');
let rowCount = 0;
const STORAGE_KEY = 'inventory_sheet_v1';
let saveTimer = null;

function saveData() {
  const rows = [];
  tbody.querySelectorAll('tr').forEach(tr => {
    const inputs = tr.querySelectorAll('input.ci');
    rows.push({ code: inputs[0].value, name: inputs[1].value, qty: inputs[2].value, sold: inputs[3].value });
  });
  localStorage.setItem(STORAGE_KEY, JSON.stringify(rows));
  showSaved();
}

function scheduleSave() {
  clearTimeout(saveTimer);
  saveTimer = setTimeout(saveData, 400);
}

function showSaved() {
  const badge = document.getElementById('save-badge');
  badge.classList.add('visible');
  clearTimeout(badge._hideTimer);
  badge._hideTimer = setTimeout(() => badge.classList.remove('visible'), 2000);
}

function loadData() {
  const raw = localStorage.getItem(STORAGE_KEY);
  if (!raw) return false;
  try {
    const rows = JSON.parse(raw);
    if (!Array.isArray(rows) || rows.length === 0) return false;
    rows.forEach(r => {
      const tr = addRow(false);
      const inputs = tr.querySelectorAll('input.ci');
      inputs[0].value = r.code || '';
      inputs[1].value = r.name || '';
      inputs[2].value = r.qty  || '';
      inputs[3].value = r.sold || '';
      calcRow(tr);
    });
    recalcAll();
    return true;
  } catch(e) { return false; }
}

function clearAll() {
  if (!confirm('Clear all data? This cannot be undone.')) return;
  localStorage.removeItem(STORAGE_KEY);
  tbody.innerHTML = '';
  rowCount = 0;
  updateEmpty();
  recalcAll();
  for (let i = 0; i < 5; i++) addRow(i === 0);
}

function addRow(autoFocus = true) {
  rowCount++;
  const tr = document.createElement('tr');

  const tdIdx = el('td', 'td-idx');
  tdIdx.textContent = rowCount;
  tr.appendChild(tdIdx);

  const inpCode = inp('ci ci-code', 'e.g. SKU-001', 'text');
  const inpName = inp('ci', 'Product name', 'text');
  const inpQty  = inp('ci ci-num', '0', 'number');
  const inpSold = inp('ci ci-num', '0', 'number');

  tr.appendChild(wrap(inpCode));
  tr.appendChild(wrap(inpName));
  tr.appendChild(wrap(inpQty));
  tr.appendChild(wrap(inpSold));

  const tdRem = el('td', 'td-rem empty');
  tdRem.textContent = '—';
  tr.appendChild(tdRem);

  const tdDel = el('td', 'td-del');
  const btnDel = document.createElement('button');
  btnDel.className = 'btn-del';
  btnDel.title = 'Delete row';
  btnDel.innerHTML = '✕';
  btnDel.onclick = () => { tr.remove(); reindex(); recalcAll(); saveData(); };
  tdDel.appendChild(btnDel);
  tr.appendChild(tdDel);

  tbody.appendChild(tr);

  const inputs = [inpCode, inpName, inpQty, inpSold];

  inpQty.addEventListener('input',  () => { calcRow(tr); recalcAll(); scheduleSave(); });
  inpSold.addEventListener('input', () => { calcRow(tr); recalcAll(); scheduleSave(); });
  inpCode.addEventListener('input', () => { recalcAll(); scheduleSave(); });
  inpName.addEventListener('input', () => { recalcAll(); scheduleSave(); });

  inputs.forEach((input, i) => {
    input.addEventListener('keydown', e => {
      if (e.key === 'Tab' || e.key === 'Enter') {
        e.preventDefault();
        if (i < inputs.length - 1) {
          inputs[i + 1].focus();
        } else {
          const next = tr.nextElementSibling;
          if (next) next.querySelectorAll('input.ci')[0].focus();
          else addRow(true).querySelectorAll('input.ci')[0].focus();
        }
      }
      if (e.key === 'ArrowDown') {
        e.preventDefault();
        const next = tr.nextElementSibling;
        if (next) (next.querySelectorAll('input.ci')[i] || next.querySelectorAll('input.ci')[0]).focus();
        else addRow(false).querySelectorAll('input.ci')[i].focus();
      }
      if (e.key === 'ArrowUp') {
        e.preventDefault();
        const prev = tr.previousElementSibling;
        if (prev) (prev.querySelectorAll('input.ci')[i] || prev.querySelectorAll('input.ci')[0]).focus();
      }
    });
  });

  updateEmpty();
  recalcAll();
  if (autoFocus) { inpCode.focus(); scheduleSave(); }
  return tr;
}

function calcRow(tr) {
  const inputs = tr.querySelectorAll('input.ci');
  const qtyVal  = inputs[2].value.trim();
  const soldVal = inputs[3].value.trim();
  const tdRem   = tr.querySelector('.td-rem');
  if (qtyVal === '' && soldVal === '') {
    tdRem.className = 'td-rem empty';
    tdRem.textContent = '—';
    return 0;
  }
  const rem = (parseFloat(qtyVal) || 0) - (parseFloat(soldVal) || 0);
  tdRem.className = 'td-rem ' + (rem > 0 ? 'pos' : rem < 0 ? 'neg' : 'zero');
  tdRem.textContent = fmt(rem);
  return rem;
}

function recalcAll() {
  let tQty = 0, tSold = 0, tRem = 0;
  tbody.querySelectorAll('tr').forEach(tr => {
    const inputs = tr.querySelectorAll('input.ci');
    tQty  += parseFloat(inputs[2].value) || 0;
    tSold += parseFloat(inputs[3].value) || 0;
    tRem  += calcRow(tr);
  });
  const n = tbody.querySelectorAll('tr').length;
  document.getElementById('c-rows').textContent = n;
  document.getElementById('c-qty').textContent  = fmt(tQty);
  document.getElementById('c-sold').textContent = fmt(tSold);
  const cRem = document.getElementById('c-rem');
  cRem.textContent = fmt(tRem);
  cRem.className = 'val' + (tRem < 0 ? ' neg' : '');
  document.getElementById('s-rows').textContent = n;
  document.getElementById('s-qty').textContent  = fmt(tQty);
  document.getElementById('s-sold').textContent = fmt(tSold);
  document.getElementById('s-rem').textContent  = fmt(tRem);
}

function reindex() {
  tbody.querySelectorAll('tr').forEach((tr, i) => tr.querySelector('.td-idx').textContent = i + 1);
  rowCount = tbody.querySelectorAll('tr').length;
  updateEmpty();
}

function updateEmpty() {
  const has = tbody.querySelectorAll('tr').length > 0;
  document.getElementById('table').style.display        = has ? '' : 'none';
  document.getElementById('empty-state').style.display  = has ? 'none' : 'block';
}

function fmt(n) {
  if (Number.isInteger(n)) return n.toLocaleString();
  return n.toLocaleString(undefined, { maximumFractionDigits: 4 });
}

function el(tag, cls) {
  const e = document.createElement(tag);
  if (cls) e.className = cls;
  return e;
}
function inp(cls, ph, type) {
  const i = document.createElement('input');
  i.type = type || 'text';
  i.className = cls;
  i.placeholder = ph || '';
  if (type === 'number') { i.min = '0'; i.step = 'any'; }
  i.autocomplete = 'off';
  i.spellcheck = false;
  return i;
}
function wrap(input) {
  const td = document.createElement('td');
  td.appendChild(input);
  return td;
}

const loaded = loadData();
if (!loaded) for (let i = 0; i < 5; i++) addRow(i === 0);
</script>
</body>
</html># warehouse
