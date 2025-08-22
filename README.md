# Financial-planner-using-Montecarlo
Kiran's financial planner
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>FIRE Planner Pro — Financial Independence (Monte Carlo)</title>
  <style>
    :root{
      --bg:#0b0f14;
      --card:#121821;
      --muted:#7a8aa0;
      --text:#eaf2ff;
      --accent:#4fd1c5;
      --accent2:#60a5fa;
      --danger:#ef4444;
      --ok:#22c55e;
      --warn:#f59e0b;
      --input:#0e141c;
      --border:#1f2a37;
      --shadow: 0 8px 20px rgba(0,0,0,.35);
      --radius:14px;
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0;
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial;
      color:var(--text);
      background: linear-gradient(180deg, #0b0f14 0, #0a0e13 100%);
    }
    header{
      position: sticky; top:0; z-index:10;
      background: rgba(9,13,19,.8);
      backdrop-filter: blur(8px);
      border-bottom:1px solid var(--border);
    }
    .wrap{max-width:1260px; margin:0 auto; padding:14px 18px;}
    .title{
      display:flex; align-items:center; gap:12px;
      font-weight:700; letter-spacing:.2px;
    }
    .badge{padding:4px 10px; border-radius:999px; font-size:12px; background:#102332; color:#a6d9ff; border:1px solid #1c3348}
    .grid{
      display:grid; gap:18px; grid-template-columns: 420px 1fr;
      align-items:start;
      margin:18px auto; max-width:1260px; padding:0 18px 36px;
    }
    .card{
      background: radial-gradient(150% 150% at 0 0, #10151d 0, #0f141b 35%, #0d1118 100%);
      border:1px solid var(--border);
      border-radius: var(--radius);
      box-shadow: var(--shadow);
    }
    .card .card-h{padding:14px 16px; border-bottom:1px solid var(--border); display:flex; align-items:center; justify-content:space-between}
    .card .card-c{padding:16px}
    .muted{color:var(--muted)}
    .row{display:grid; gap:12px; grid-template-columns: repeat(2, minmax(0,1fr));}
    .row-3{display:grid; gap:12px; grid-template-columns: repeat(3, minmax(0,1fr));}
    .row-4{display:grid; gap:12px; grid-template-columns: repeat(4, minmax(0,1fr));}
    label{font-size:12px; color:#a8bdd6; display:block; margin:0 0 6px 2px}
    input[type="number"], input[type="text"], select{
      width:100%; padding:10px 12px; border-radius:10px;
      border:1px solid var(--border); background:var(--input); color:var(--text);
      outline:none;
    }
    input[type="checkbox"]{transform: scale(1.2);}
    .hint{font-size:12px; color:#8aa0bb; margin-top:6px}
    .inline{display:flex; align-items:center; gap:12px}
    .btn{
      appearance:none; border:1px solid #223241; background:linear-gradient(180deg, #1a2736, #162230);
      color:#d5e9ff; border-radius:12px; padding:10px 14px; cursor:pointer;
      font-weight:600; letter-spacing:.2px; box-shadow: var(--shadow);
    }
    .btn:hover{filter:brightness(1.08)}
    .btn:active{transform: translateY(1px)}
    .btn-ghost{background:#0e151e; border:1px solid #1f2a37; color:#bcd3f0}
    .btn-good{background: linear-gradient(180deg, #1a3a2b, #10251b); border-color:#1e3a2a; color:#c2ffdd}
    .btn-danger{background: linear-gradient(180deg, #3a1a1a, #251010); border-color:#3b1e1e; color:#ffd2d2}
    .section{border-top:1px dashed #233042; margin-top:12px; padding-top:12px}
    details{border:1px solid var(--border); border-radius:12px; overflow:hidden; margin-bottom:12px; background: #0f1520;}
    summary{cursor:pointer; list-style:none; padding:12px 14px; font-weight:700; color:#dcedff; background:linear-gradient(180deg, #0f1723, #0e1621);}
    summary::-webkit-details-marker{display:none}
    table{width:100%; border-collapse:collapse; font-size:14px}
    th,td{padding:8px 6px; border-bottom:1px solid #1f2a37; text-align:left}
    tbody tr:last-child td{border-bottom:none}
    .pill{display:inline-flex; align-items:center; gap:6px; background:#0f1b29; border:1px solid #1f2a37; color:#a8d3ff; border-radius:999px; padding:6px 10px; font-size:12px;}
    .stat{display:grid; gap:6px; padding:10px 12px; border:1px solid var(--border); border-radius:10px; background: #0e1520;}
    .stat .v{font-size:22px; font-weight:800}
    .stat .k{font-size:12px; color:#90a6c1}
    .good{color:var(--ok)} .bad{color:var(--danger)} .warn{color:var(--warn)}
    .foot{font-size:12px; color:#8aa0bb; padding:12px 16px; border-top:1px solid var(--border); background:#0e141c; border-radius:0 0 var(--radius) var(--radius)}
    .flex{display:flex; gap:12px; flex-wrap:wrap}
    .right .card-c{padding-bottom:6px}
    .sticky-actions{position:sticky; bottom:0; background:linear-gradient(180deg, rgba(15,21,32,0), #0f1520 50%); padding:12px; display:flex; gap:10px; justify-content:flex-end; border-top:1px solid #1f2a37}
    .note{font-size:12px; color:#9fb5cf}
    .kpi{display:grid; grid-template-columns: repeat(3, minmax(0,1fr)); gap:10px}
    .mono{font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, "Liberation Mono", monospace;}
    .tag{padding:2px 8px; background:#132033; border:1px solid #1e2d44; border-radius:999px; font-size:12px; color:#a4c7ff}
    .hr{height:1px; background:linear-gradient(90deg, transparent, #1f2a37, transparent); margin:10px 0}
    .help{font-size:12px; color:#b9cce6; padding:8px 10px; border:1px dashed #2a3b50; border-radius:10px; background:#0d1522}
    .small{font-size:12px}
    .hidden{display:none !important}
  </style>
</head>
<body>
  <header>
    <div class="wrap title">
      <div style="font-size:18px">🔥 <span style="color:#cde9ff">FIRE Planner Pro</span> — Financial Independence (Monte Carlo)</div>
      <span class="badge">Client‑side • No data leaves your browser</span>
      <span class="badge">No 4% shortcuts — customizable longevity, returns, fees, taxes</span>
    </div>
  </header>

  <main class="grid">
    <!-- LEFT: INPUTS -->
    <section class="card" id="inputs">
      <div class="card-h"><div>Inputs</div><div class="muted small">Tip: all money fields are in “today’s” currency. The engine converts to real terms automatically.</div></div>
      <div class="card-c">
        <details open>
          <summary>1) Profile & Longevity</summary>
          <div class="card-c">
            <div class="row">
              <div><label>Current age</label><input id="currentAge" type="number" min="18" max="100" step="1" value="38"></div>
              <div><label>Planned retirement age</label><input id="retireAge" type="number" min="30" max="80" step="1" value="55"></div>
            </div>
            <div class="row">
              <div><label>Plan until age (longevity assumption)</label><input id="planToAge" type="number" min="70" max="110" step="1" value="95"></div>
              <div><label>Target success probability (%)</label><input id="successTarget" type="number" min="50" max="99.9" step="0.1" value="90"></div>
            </div>
            <div class="row">
              <div><label>General inflation (CPI, %/yr)</label><input id="inflation" type="number" step="0.1" value="5"></div>
              <div><label>Currency symbol (display only)</label><input id="ccy" type="text" value="₹"></div>
            </div>
            <div class="hint">Longevity sets the horizon for withdrawals. Use a conservative age (95–100) or your preferred percentile.</div>
          </div>
        </details>

        <details open>
          <summary>2) Portfolio & Contributions (pre‑retirement)</summary>
          <div class="card-c">
            <div class="row">
              <div><label>Current investable portfolio</label><input id="currentPortfolio" type="number" step="1000" value="3000000"></div>
              <div><label>Annual contribution (today’s ₹)</label><input id="annualContribution" type="number" step="1000" value="600000"></div>
            </div>
            <div class="row">
              <div><label>Annual raise on contributions (real %, optional)</label><input id="contribRaise" type="number" step="0.1" value="0"></div>
              <div><label>Annual fee drag (ER, %, all-in)</label><input id="feeDrag" type="number" step="0.05" value="0.3"></div>
            </div>
            <div class="row">
              <div><label>Annual tax drag on returns (%)*</label><input id="taxDrag" type="number" step="0.1" value="0.5"></div>
              <div><label class="muted">*Approximate effective % of returns lost to taxes yearly.</label></div>
            </div>
            <div class="help">Contributions are added at year‑end in “real” terms. Fee & tax drags are applied before inflation conversion.</div>
          </div>
        </details>

        <details open>
          <summary>3) Expected Returns & Risk</summary>
          <div class="card-c">
            <div class="row">
              <div><label>Accumulation: expected <b>nominal</b> return (%/yr)</label><input id="muAcc" type="number" step="0.1" value="12"></div>
              <div><label>Accumulation: volatility (stdev, %/yr)</label><input id="volAcc" type="number" step="0.1" value="18"></div>
            </div>
            <div class="row">
              <div><label>Retirement: expected <b>nominal</b> return (%/yr)</label><input id="muRet" type="number" step="0.1" value="10"></div>
              <div><label>Retirement: volatility (stdev, %/yr)</label><input id="volRet" type="number" step="0.1" value="15"></div>
            </div>
            <div class="row">
              <div><label>Return floor per year (% min, to curb extremes)</label><input id="retFloor" type="number" step="0.1" value="-60"></div>
              <div><label>Number of Monte Carlo paths</label><input id="nsims" type="number" step="100" value="2000"></div>
            </div>
            <div class="row">
              <div><label>Random seed (optional for reproducibility)</label><input id="seed" type="number" step="1" placeholder="e.g., 12345"></div>
              <div class="inline" style="margin-top:24px"><input id="coupleCorr" type="checkbox"><label for="coupleCorr">Use same random shocks pre‑ and post‑retirement (simple path continuity)</label></div>
            </div>
            <div class="hint">Nominal returns are converted to “real” inside the model using CPI and your fee/tax drags.</div>
          </div>
        </details>

        <details open>
          <summary>4) Spending Plan (today’s currency)</summary>
          <div class="card-c">
            <div class="row">
              <div><label>Baseline annual living expenses in retirement</label><input id="retireExpenses" type="number" step="1000" value="1200000"></div>
              <div><label>Essential spending fraction (%)</label><input id="essentialFrac" type="number" step="1" value="60"></div>
            </div>
            <div class="help">All spending is entered in today’s rupees. The engine treats baseline spending as “real” (inflation‑indexed). You can add one‑off expenses below.</div>
            <div class="section">
              <div class="inline" style="justify-content:space-between">
                <div class="muted">One‑off flows (expenses as +, windfalls as −). Ages are your age at the time of the flow.</div>
                <button class="btn btn-ghost" onclick="addFlow()">+ Add flow</button>
              </div>
              <div class="hr"></div>
              <table id="flowsTable">
                <thead><tr><th>Label</th><th>Age</th><th>Amount (today)</th><th>Indexed to CPI?</th><th></th></tr></thead>
                <tbody></tbody>
              </table>
            </div>
          </div>
        </details>

        <details open>
          <summary>5) Retirement Incomes</summary>
          <div class="card-c">
            <div class="inline" style="justify-content:space-between">
              <div class="muted">Add pensions, rentals, annuities, part‑time income, etc. (positive amounts).</div>
              <button class="btn btn-ghost" onclick="addIncome()">+ Add income</button>
            </div>
            <div class="hr"></div>
            <table id="incomesTable">
              <thead><tr><th>Label</th><th>Start age</th><th>End age</th><th>Annual amount (today)</th><th>Indexed to CPI?</th><th></th></tr></thead>
              <tbody></tbody>
            </table>
          </div>
        </details>

        <details open>
          <summary>6) Withdrawal Policy</summary>
          <div class="card-c">
            <div class="row">
              <div>
                <label>Policy</label>
                <select id="policy">
                  <option value="fixed">Fixed real spending (inflation‑indexed)</option>
                  <option value="guardrails">Guardrails (Guyton‑Klinger‑style)</option>
                </select>
              </div>
              <div class="inline" style="gap:10px; margin-top:26px">
                <span class="muted">For guardrails:</span>
                <span class="tag">Bandwidth ±<input id="grWidth" type="number" step="1" value="20" style="width:60px">%</span>
                <span class="tag">Adjustment step <input id="grStep" type="number" step="1" value="10" style="width:60px">%</span>
              </div>
            </div>
            <div class="help">“Guardrails” raises or trims spending when your withdrawal rate drifts outside ±bandwidth around the starting withdrawal rate (based on net withdrawals after other incomes). Adjust by the step % to steer back.</div>
          </div>
        </details>

        <div class="sticky-actions">
          <button class="btn btn-good" onclick="runAll()">▶ Run Monte Carlo</button>
          <button class="btn" onclick="saveConfig()">Save</button>
          <button class="btn btn-ghost" onclick="loadConfig()">Load</button>
          <button class="btn btn-danger" onclick="resetConfig()">Reset</button>
          <button class="btn btn-ghost" onclick="exportConfig()">Export JSON</button>
          <button class="btn btn-ghost" onclick="importConfig()">Import JSON</button>
        </div>
      </div>
      <div class="foot small">Privacy: everything runs locally in your browser. No data is uploaded.</div>
    </section>

    <!-- RIGHT: RESULTS -->
    <section class="right">
      <div class="card">
        <div class="card-h">
          <div>Plan Summary</div>
          <div class="small muted" id="status">Ready.</div>
        </div>
        <div class="card-c">
          <div class="kpi">
            <div class="stat">
              <div class="k">Required nest egg at retirement (for target success)</div>
              <div class="v" id="reqNest">—</div>
              <div class="small muted">Computed via Monte Carlo bisection using your policy and cash‑flows.</div>
            </div>
            <div class="stat">
              <div class="k">Implied safe starting withdrawal rate</div>
              <div class="v" id="swr">—</div>
              <div class="small muted">= (Year‑0 net withdrawals) ÷ required nest egg.</div>
            </div>
            <div class="stat">
              <div class="k">Are you Coast FI today?</div>
              <div class="v" id="coastFI">—</div>
              <div class="small muted" id="coastDetail"></div>
            </div>
          </div>
        </div>
        <div class="foot small">All results are in today’s currency (real terms).</div>
      </div>

      <div class="card">
        <div class="card-h">Monte Carlo Results</div>
        <div class="card-c">
          <div class="flex">
            <div class="stat" style="min-width:220px">
              <div class="k">Success probability if retiring at planned age</div>
              <div class="v" id="planSuccess">—</div>
              <div class="small muted">Simulates accumulation + retirement under your assumptions.</div>
            </div>
            <div class="stat" style="min-width:220px">
              <div class="k">Retire‑today success probability</div>
              <div class="v" id="nowSuccess">—</div>
              <div class="small muted">What if you stopped work now with current portfolio?</div>
            </div>
            <div class="stat" style="min-width:220px">
              <div class="k">Retirement pot at start (percentiles)</div>
              <div class="v" id="potPct">—</div>
              <div class="small muted">10th / 50th / 90th percentiles.</div>
            </div>
          </div>
          <div class="section">
            <div class="row">
              <div class="stat">
                <div class="k">Earliest FI age (when pot can coast to nest egg)</div>
                <div class="v" id="fiAges">—</div>
                <div class="small muted">10th / median / 90th across paths.</div>
              </div>
              <div class="stat">
                <div class="k">Years until planned retirement</div>
                <div class="v" id="yrsToRet">—</div>
                <div class="small muted">For reference.</div>
              </div>
            </div>
          </div>
        </div>
        <div class="foot small">Caution: estimates only, not financial advice.</div>
      </div>

      <div class="card">
        <div class="card-h">Assumption Check</div>
        <div class="card-c small">
          <div id="assumptionEcho" class="mono"></div>
        </div>
      </div>
    </section>
  </main>

  <script>
    // ---------- Utilities ----------
    const $ = sel => document.querySelector(sel);
    const $$ = sel => Array.from(document.querySelectorAll(sel));
    const fmtC = (x, ccy='₹') => {
      if (!isFinite(x)) return '—';
      const abs = Math.abs(x);
      let unit = '', val = x;
      if (abs >= 1e7) { unit='Cr'; val = x/1e7; }
      else if (abs >= 1e5) { unit='L'; val = x/1e5; }
      else if (abs >= 1e3) { unit='k'; val = x/1e3; }
      return ccy + ' ' + val.toFixed(2) + (unit? ' ' + unit : '');
    };
    const fmtP = x => isFinite(x) ? (x*100).toFixed(1) + '%' : '—';
    const clamp = (x, lo, hi) => Math.max(lo, Math.min(hi, x));
    function setStatus(msg){ $('#status').textContent = msg; }

    // Seeded RNG (xorshift32)
    function XorShift(seed){
      this.state = seed>>>0 || 123456789;
      this.next = function(){
        let x = this.state;
        x ^= x << 13; x ^= x >>> 17; x ^= x << 5;
        this.state = x>>>0;
        return (this.state) / 0xFFFFFFFF;
      }
    }
    function normalBM(rng){
      // Box-Muller transform using our RNG
      let u = 0, v = 0;
      while (u === 0) u = rng.next();
      while (v === 0) v = rng.next();
      const mag = Math.sqrt(-2.0 * Math.log(u));
      const z0 = mag * Math.cos(2*Math.PI*v);
      const z1 = mag * Math.sin(2*Math.PI*v);
      return [z0, z1];
    }

    function parseF(id){ const v = parseFloat($(id).value); return isFinite(v) ? v : 0; }
    function parseI(id){ const v = parseInt($(id).value, 10); return isFinite(v) ? v : 0; }

    // ---------- Dynamic rows ----------
    function addIncome(row){
      const tbody = $('#incomesTable tbody');
      const tr = document.createElement('tr');
      tr.innerHTML = `
        <td><input type="text" value="${row?.label ?? 'Pension'}"></td>
        <td><input type="number" step="1" value="${row?.startAge ?? 60}"></td>
        <td><input type="number" step="1" value="${row?.endAge ?? 95}"></td>
        <td><input type="number" step="1000" value="${row?.amount ?? 300000}"></td>
        <td style="text-align:center"><input type="checkbox" ${row?.indexed ? 'checked':''}></td>
        <td><button class="btn btn-ghost small" onclick="this.closest('tr').remove()">✕</button></td>
      `;
      tbody.appendChild(tr);
    }
    function addFlow(row){
      const tbody = $('#flowsTable tbody');
      const tr = document.createElement('tr');
      tr.innerHTML = `
        <td><input type="text" value="${row?.label ?? 'One‑off expense'}"></td>
        <td><input type="number" step="1" value="${row?.age ?? 52}"></td>
        <td><input type="number" step="1000" value="${row?.amount ?? 500000}"></td>
        <td style="text-align:center"><input type="checkbox" ${row?.indexed ? 'checked':''}></td>
        <td><button class="btn btn-ghost small" onclick="this.closest('tr').remove()">✕</button></td>
      `;
      tbody.appendChild(tr);
    }

    function readIncomes(){
      const rows = [];
      $$('#incomesTable tbody tr').forEach(tr=>{
        const [label,startAge,endAge,amount,indexed] = [
          tr.children[0].querySelector('input').value,
          parseFloat(tr.children[1].querySelector('input').value),
          parseFloat(tr.children[2].querySelector('input').value),
          parseFloat(tr.children[3].querySelector('input').value),
          tr.children[4].querySelector('input').checked
        ];
        if (isFinite(amount) && amount !== 0){
          rows.push({label, startAge, endAge, amount, indexed});
        }
      });
      return rows;
    }
    function readFlows(){
      const rows = [];
      $$('#flowsTable tbody tr').forEach(tr=>{
        const [label, age, amount, indexed] = [
          tr.children[0].querySelector('input').value,
          parseFloat(tr.children[1].querySelector('input').value),
          parseFloat(tr.children[2].querySelector('input').value),
          tr.children[3].querySelector('input').checked
        ];
        if (isFinite(amount) && amount !== 0){
          rows.push({label, age, amount, indexed});
        }
      });
      return rows;
    }

    // ---------- Config ----------
    const STORAGE_KEY = 'fi_tool_config_v1';
    function getConfig(){
      const cfg = {
        currentAge: parseI('#currentAge'),
        retireAge: parseI('#retireAge'),
        planToAge: parseI('#planToAge'),
        successTarget: parseF('#successTarget')/100,
        inflation: parseF('#inflation')/100,
        ccy: $('#ccy').value || '₹',
        currentPortfolio: parseF('#currentPortfolio'),
        annualContribution: parseF('#annualContribution'),
        contribRaise: parseF('#contribRaise')/100,
        feeDrag: parseF('#feeDrag')/100,
        taxDrag: parseF('#taxDrag')/100,
        muAcc: parseF('#muAcc')/100,
        volAcc: parseF('#volAcc')/100,
        muRet: parseF('#muRet')/100,
        volRet: parseF('#volRet')/100,
        retFloor: parseF('#retFloor')/100,
        nsims: parseI('#nsims'),
        seed: parseI('#seed') || Math.floor(Math.random()*1e9),
        coupleCorr: $('#coupleCorr').checked,
        retireExpenses: parseF('#retireExpenses'),
        essentialFrac: clamp(parseF('#essentialFrac')/100, 0, 1),
        policy: $('#policy').value,
        grWidth: parseF('#grWidth')/100,
        grStep: parseF('#grStep')/100,
        incomes: readIncomes(),
        flows: readFlows(),
      };
      // guard rails defaults for fixed policy (ignored)
      return cfg;
    }

    function saveConfig(){
      const cfg = getConfig();
      localStorage.setItem(STORAGE_KEY, JSON.stringify(cfg));
      setStatus('Saved locally.');
    }
    function loadConfig(){
      const raw = localStorage.getItem(STORAGE_KEY);
      if (!raw){ setStatus('Nothing to load.'); return; }
      try{
        const cfg = JSON.parse(raw);
        applyConfig(cfg);
        setStatus('Loaded from local storage.');
      }catch(e){ setStatus('Load failed.'); console.error(e); }
    }
    function resetConfig(){
      localStorage.removeItem(STORAGE_KEY);
      location.reload();
    }
    function exportConfig(){
      const cfg = getConfig();
      navigator.clipboard.writeText(JSON.stringify(cfg, null, 2));
      setStatus('Copied JSON to clipboard.');
    }
    function importConfig(){
      const txt = prompt('Paste config JSON:');
      if (!txt) return;
      try{
        const cfg = JSON.parse(txt);
        applyConfig(cfg);
        setStatus('Imported config.');
      }catch(e){ alert('Invalid JSON'); }
    }
    function applyConfig(cfg){
      if (!cfg) return;
      $('#currentAge').value = cfg.currentAge ?? 38;
      $('#retireAge').value = cfg.retireAge ?? 55;
      $('#planToAge').value = cfg.planToAge ?? 95;
      $('#successTarget').value = cfg.successTarget ? (cfg.successTarget*100) : 90;
      $('#inflation').value = cfg.inflation ? (cfg.inflation*100) : 5;
      $('#ccy').value = cfg.ccy ?? '₹';
      $('#currentPortfolio').value = cfg.currentPortfolio ?? 3000000;
      $('#annualContribution').value = cfg.annualContribution ?? 600000;
      $('#contribRaise').value = cfg.contribRaise ? (cfg.contribRaise*100) : 0;
      $('#feeDrag').value = cfg.feeDrag ? (cfg.feeDrag*100) : 0.3;
      $('#taxDrag').value = cfg.taxDrag ? (cfg.taxDrag*100) : 0.5;
      $('#muAcc').value = cfg.muAcc ? (cfg.muAcc*100) : 12;
      $('#volAcc').value = cfg.volAcc ? (cfg.volAcc*100) : 18;
      $('#muRet').value = cfg.muRet ? (cfg.muRet*100) : 10;
      $('#volRet').value = cfg.volRet ? (cfg.volRet*100) : 15;
      $('#retFloor').value = cfg.retFloor ? (cfg.retFloor*100) : -60;
      $('#nsims').value = cfg.nsims ?? 2000;
      $('#seed').value = cfg.seed ?? '';
      $('#coupleCorr').checked = !!cfg.coupleCorr;
      $('#retireExpenses').value = cfg.retireExpenses ?? 1200000;
      $('#essentialFrac').value = cfg.essentialFrac ? (cfg.essentialFrac*100) : 60;
      $('#policy').value = cfg.policy ?? 'fixed';
      $('#grWidth').value = cfg.grWidth ? (cfg.grWidth*100) : 20;
      $('#grStep').value = cfg.grStep ? (cfg.grStep*100) : 10;
      // rebuild tables
      $('#incomesTable tbody').innerHTML = '';
      (cfg.incomes ?? []).forEach(addIncome);
      $('#flowsTable tbody').innerHTML = '';
      (cfg.flows ?? []).forEach(addFlow);
    }

    // ---------- Model helpers ----------
    function effRealReturn(rNom, infl, fee, tax){
      const nominalAfterDrags = (1 + rNom - fee - tax);
      return nominalAfterDrags/(1 + infl) - 1;
    }
    function yearDraws(nsims, nyears, mu, sigma, seed, floorPct){
      // returns nsims x nyears array of *realized nominal* returns r ~ N(mu, sigma), floored at floorPct/100
      const rng = new XorShift(seed || 123456789);
      const draws = new Array(nsims);
      for (let i=0; i<nsims; i++){
        const row = new Array(nyears);
        let zPair = normalBM(rng), useSecond = false;
        for (let t=0; t<nyears; t++){
          if (useSecond){
            var z = zPair[1];
          }else{
            zPair = normalBM(rng); z = zPair[0];
          }
          useSecond = !useSecond;
          let r = mu + sigma * z;
          const minR = floorPct/100;
          if (r < minR) r = minR;
          row[t] = r;
        }
        draws[i] = row;
      }
      return draws;
    }

    function sumIncomesAtAge(incomes, age, infl){
      let s = 0;
      for (const inc of incomes){
        if (age >= inc.startAge && age <= inc.endAge){
          // amounts entered in today's rupees; keep as real if indexed, else real value decays over time
          const yearsFromNow = age - cfg.currentAge; // uses global cfg set in runAll
          const amtReal = inc.indexed ? inc.amount : inc.amount/Math.pow(1+infl, yearsFromNow);
          s += amtReal;
        }
      }
      return s;
    }
    function sumFlowsAtAge(flows, age, infl){
      // positive is outflow (expense), negative is inflow
      let s = 0;
      for (const f of flows){
        if (Math.round(f.age) === Math.round(age)){
          const yearsFromNow = age - cfg.currentAge;
          const amtReal = f.indexed ? f.amount : f.amount/Math.pow(1+infl, yearsFromNow);
          s += amtReal;
        }
      }
      return s;
    }

    // ---------- Retirement simulation (given starting pot P0 in real rupees) ----------
    function retireSuccessForPot(P0, cfg, retDraws){
      const nsims = cfg.nsims;
      const horizon = Math.max(0, cfg.planToAge - cfg.retireAge);
      if (horizon === 0) return 1.0;

      let successes = 0;
      for (let i=0;i<nsims;i++){
        let P = P0;
        let spendReal = cfg.retireExpenses; // starting baseline real spend
        const WR0 = Math.max( (spendReal - sumIncomesAtAge(cfg.incomes, cfg.retireAge, cfg.inflation) + sumFlowsAtAge(cfg.flows, cfg.retireAge, cfg.inflation)) / Math.max(P0,1e-9), 0 );
        for (let t=0; t<horizon; t++){
          const age = cfg.retireAge + t;
          // Guardrails adjust spend *before* the year's return & withdrawal
          if (cfg.policy === 'guardrails'){
            const incomes = sumIncomesAtAge(cfg.incomes, age, cfg.inflation);
            const flows = sumFlowsAtAge(cfg.flows, age, cfg.inflation);
            let netW = Math.max(spendReal - incomes + flows, 0);
            let wr = netW / Math.max(P,1e-9);
            const upper = WR0 * (1 + cfg.grWidth), lower = WR0 * (1 - cfg.grWidth);
            if (wr > upper) spendReal *= (1 - cfg.grStep);
            else if (wr < lower) spendReal *= (1 + cfg.grStep);
            // fixed real otherwise (no inflation add in real space)
          }
          const incomes = sumIncomesAtAge(cfg.incomes, age, cfg.inflation);
          const flows = sumFlowsAtAge(cfg.flows, age, cfg.inflation);
          const netW = Math.max(spendReal - incomes + flows, 0); // withdrawals from portfolio (real)

          // Return for this year in real terms
          const rNom = retDraws[i][t];
          const rReal = effRealReturn(rNom, cfg.inflation, cfg.feeDrag, cfg.taxDrag);
          P = P * (1 + rReal) - netW;

          if (P <= 0){ P = 0; break; }
        }
        if (P > 0) successes++;
      }
      return successes / nsims;
    }

    // Compute required pot at retirement so that success prob >= target
    function computeRequiredPot(cfg, retDraws){
      // quick check: if incomes already cover spending at t0 and future, required pot may be ~0
      const horizon = Math.max(0, cfg.planToAge - cfg.retireAge);
      if (horizon === 0) return 0;

      // Establish a search bracket
      const t0Net = Math.max(cfg.retireExpenses - sumIncomesAtAge(cfg.incomes, cfg.retireAge, cfg.inflation) + sumFlowsAtAge(cfg.flows, cfg.retireAge, cfg.inflation), 0);
      let lo = 0, hi = Math.max(t0Net*2, 1); // start small
      let pHi = retireSuccessForPot(hi, cfg, retDraws);
      let iter = 0;
      while (pHi < cfg.successTarget && hi < 1e13 && iter < 40){
        hi *= 1.8;
        pHi = retireSuccessForPot(hi, cfg, retDraws);
        iter++;
      }
      // If even huge pot fails (pathological inputs), return NaN
      if (pHi < cfg.successTarget) return NaN;

      // Bisection
      let best = hi;
      for (let k=0; k<36; k++){
        const mid = (lo + hi)/2;
        const p = retireSuccessForPot(mid, cfg, retDraws);
        if (p >= cfg.successTarget){ best = mid; hi = mid; } else { lo = mid; }
      }
      return best;
    }

    // ---------- Full path simulation (accum + retirement) ----------
    function simulateFull(cfg){
      const yearsAcc = Math.max(0, cfg.retireAge - cfg.currentAge);
      const yearsRet = Math.max(0, cfg.planToAge - cfg.retireAge);
      const nsims = cfg.nsims;

      // Draws
      const accDraws = yearDraws(nsims, yearsAcc, cfg.muAcc, cfg.volAcc, cfg.seed, cfg.retFloor*100);
      const retSeed = cfg.coupleCorr ? cfg.seed : (cfg.seed ^ 0x9e3779b9);
      const retDraws = yearDraws(nsims, yearsRet, cfg.muRet, cfg.volRet, retSeed, cfg.retFloor*100);

      // Compute required nest egg using retirement draws (independent of accumulation dynamics)
      const reqPot = computeRequiredPot(cfg, retDraws);
      const t0Net = Math.max(cfg.retireExpenses - sumIncomesAtAge(cfg.incomes, cfg.retireAge, cfg.inflation) + sumFlowsAtAge(cfg.flows, cfg.retireAge, cfg.inflation), 0);
      const swr = reqPot>0 ? (t0Net/reqPot) : 0;

      // Coast FI threshold today (present value such that without any more contributions, expected growth reaches reqPot)
      const effRealAccMean = effRealReturn(cfg.muAcc, cfg.inflation, cfg.feeDrag, cfg.taxDrag);
      const yearsToRet = yearsAcc;
      const coastPV = yearsToRet>0 ? (reqPot / Math.pow(1 + Math.max(effRealAccMean, -0.99), yearsToRet)) : reqPot;

      // Simulate accumulation + retirement success per path
      let successes = 0;
      let potsAtRet = [];
      let fiAges = [];
      for (let i=0;i<nsims;i++){
        let P = cfg.currentPortfolio;
        let fiAgeReached = null;
        for (let t=0; t<yearsAcc; t++){
          const age = cfg.currentAge + t;
          const contrib = cfg.annualContribution * Math.pow(1 + cfg.contribRaise, t);
          const flows = sumFlowsAtAge(cfg.flows, age, cfg.inflation); // outflows positive
          const rNom = accDraws[i][t];
          const rReal = effRealReturn(rNom, cfg.inflation, cfg.feeDrag, cfg.taxDrag);
          P = P * (1 + rReal) + contrib - flows;
          // Check Coast-FI this year (if you stopped contributions now)
          const yearsLeft = (cfg.retireAge - (age+1));
          const growToRet = yearsLeft>0 ? P * Math.pow(1 + Math.max(effRealAccMean, -0.99), yearsLeft) : P;
          if (fiAgeReached===null && growToRet >= reqPot) fiAgeReached = age+1;
        }
        potsAtRet.push(P);
        if (fiAgeReached===null) fiAgeReached = cfg.retireAge; // at worst, by retirement
        fiAges.push(fiAgeReached);

        // Now simulate retirement on this path using the pre-drawn retirement shocks
        if (yearsRet === 0){
          successes++; continue;
        }
        let spendReal = cfg.retireExpenses;
        const WR0 = Math.max( (spendReal - sumIncomesAtAge(cfg.incomes, cfg.retireAge, cfg.inflation) + sumFlowsAtAge(cfg.flows, cfg.retireAge, cfg.inflation)) / Math.max(P,1e-9), 0 );

        let alive = true;
        for (let t=0; t<yearsRet; t++){
          const age = cfg.retireAge + t;
          if (cfg.policy === 'guardrails'){
            const incomes = sumIncomesAtAge(cfg.incomes, age, cfg.inflation);
            const flows = sumFlowsAtAge(cfg.flows, age, cfg.inflation);
            let netW = Math.max(spendReal - incomes + flows, 0);
            let wr = netW / Math.max(P,1e-9);
            const upper = WR0 * (1 + cfg.grWidth), lower = WR0 * (1 - cfg.grWidth);
            if (wr > upper) spendReal *= (1 - cfg.grStep);
            else if (wr < lower) spendReal *= (1 + cfg.grStep);
          }
          const incomes = sumIncomesAtAge(cfg.incomes, age, cfg.inflation);
          const flows = sumFlowsAtAge(cfg.flows, age, cfg.inflation);
          const netW = Math.max(spendReal - incomes + flows, 0);
          const rNom = retDraws[i][t];
          const rReal = effRealReturn(rNom, cfg.inflation, cfg.feeDrag, cfg.taxDrag);
          P = P * (1 + rReal) - netW;
          if (P <= 0){ alive = false; break; }
        }
        if (alive) successes++;
      }

      // Success probability
      const planSuccess = successes / nsims;

      // Distribution stats
      potsAtRet.sort((a,b)=>a-b);
      const p = q=> potsAtRet[Math.floor(clamp(q,0,1)*(potsAtRet.length-1))];
      const p10=p(0.10), p50=p(0.5), p90=p(0.9);

      // Retire-today success (with currentPortfolio)
      const retDrawsNow = yearDraws(nsims, yearsRet + Math.max(0, cfg.retireAge - cfg.currentAge), cfg.muRet, cfg.volRet, cfg.seed ^ 0x1234abcd, cfg.retFloor*100);
      // If retiring today, we should apply retirement dynamics immediately
      const cfgNow = Object.assign({}, cfg, { retireAge: cfg.currentAge });
      const yearsRetNow = Math.max(0, cfgNow.planToAge - cfgNow.retireAge);
      const retDrawsOnly = yearDraws(nsims, yearsRetNow, cfg.muRet, cfg.volRet, cfg.seed ^ 0x5555aaaa, cfg.retFloor*100);
      const nowSuccess = retireSuccessForPot(cfg.currentPortfolio, cfgNow, retDrawsOnly);

      // FI ages percentiles
      fiAges.sort((a,b)=>a-b);
      const fiAge10 = fiAges[Math.floor(0.10*(fiAges.length-1))];
      const fiAge50 = fiAges[Math.floor(0.50*(fiAges.length-1))];
      const fiAge90 = fiAges[Math.floor(0.90*(fiAges.length-1))];

      return { reqPot, swr, planSuccess, nowSuccess, p10, p50, p90, coastPV, fiAge10, fiAge50, fiAge90, yearsToRet };
    }

    // ---------- UI glue ----------
    let cfg = null;
    function runAll(){
      cfg = getConfig();
      if (cfg.retireAge < cfg.currentAge){
        alert('Retirement age must be >= current age');
        return;
      }
      if (cfg.planToAge <= cfg.retireAge){
        alert('Plan-to age must be > retirement age');
        return;
      }
      setStatus('Simulating…');
      setTimeout(()=>{
        const res = simulateFull(cfg);
        renderResults(cfg, res);
        setStatus('Done.');
      }, 10);
      saveConfig();
    }

    function renderResults(cfg, res){
      const ccy = cfg.ccy || '₹';
      $('#reqNest').textContent = isNaN(res.reqPot) ? 'Not solvable with given settings' : fmtC(res.reqPot, ccy);
      $('#swr').textContent = isNaN(res.reqPot) || res.reqPot===0 ? '—' : ( (res.swr*100).toFixed(2) + '%' );
      $('#planSuccess').innerHTML = `${(res.planSuccess*100).toFixed(1)}% ` + (res.planSuccess>=cfg.successTarget ? '<span class="good">✓ meets target</span>' : '<span class="bad">✗ below target</span>');
      $('#nowSuccess').textContent = (res.nowSuccess*100).toFixed(1) + '%';
      $('#potPct').textContent = `${fmtC(res.p10, ccy)} / ${fmtC(res.p50, ccy)} / ${fmtC(res.p90, ccy)}`;
      $('#yrsToRet').textContent = res.yearsToRet;

      const isCoast = cfg.currentPortfolio >= res.coastPV;
      $('#coastFI').innerHTML = isCoast ? '<span class="good">YES</span>' : '<span class="warn">Not yet</span>';
      $('#coastDetail').textContent = (isCoast
        ? `You have at least the present value (${fmtC(res.coastPV, ccy)}) needed to coast to the required nest egg.`
        : `Need ${fmtC(res.coastPV - cfg.currentPortfolio, ccy)} more (today’s money) to reach Coast FI.`);

      $('#fiAges').textContent = `${res.fiAge10} / ${res.fiAge50} / ${res.fiAge90}`;

      // Assumption echo
      const effRealAcc = effRealReturn(cfg.muAcc, cfg.inflation, cfg.feeDrag, cfg.taxDrag);
      const effRealRet = effRealReturn(cfg.muRet, cfg.inflation, cfg.feeDrag, cfg.taxDrag);
      $('#assumptionEcho').textContent =
        `Ages: now ${cfg.currentAge}, retire ${cfg.retireAge}, plan to ${cfg.planToAge}. Target success ${(cfg.successTarget*100).toFixed(1)}%.\n`+
        `Inflation (CPI): ${(cfg.inflation*100).toFixed(1)}%/yr. Effective real return (accum/retire): ${(effRealAcc*100).toFixed(2)}% / ${(effRealRet*100).toFixed(2)}% after fee ${(cfg.feeDrag*100).toFixed(2)}% and tax drag ${(cfg.taxDrag*100).toFixed(2)}%.\n`+
        `Policy: ${cfg.policy==='fixed' ? 'Fixed real' : `Guardrails ±${(cfg.grWidth*100).toFixed(0)}%, step ${(cfg.grStep*100).toFixed(0)}%`}.\n`+
        `Retirement incomes: ${cfg.incomes.length} item(s). One‑off flows: ${cfg.flows.length} item(s).\n`+
        `Required nest egg at retirement: ${isNaN(res.reqPot) ? 'n/a' : fmtC(res.reqPot, ccy)}. Coast‑FI PV today: ${fmtC(res.coastPV, ccy)}.`;
    }

    // Defaults
    addIncome({label:'Pension', startAge:60, endAge:95, amount:300000, indexed:true});
    addFlow({label:'Child education', age:52, amount:500000, indexed:true});

    // Auto-run on first open for a quick demo
    window.addEventListener('load', ()=>{
      runAll();
    });
  </script>
</body>
</html>
