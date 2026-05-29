<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>Global Equity Dashboard v2</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<style>
:root{
  --bg:#f7f6f2;--surface:#fff;--surface2:#f2f1ed;--surface3:#eae9e4;
  --border:rgba(0,0,0,0.09);--border-strong:rgba(0,0,0,0.18);
  --text:#1a1915;--muted:#6b6a65;--faint:#9e9d97;
  --sp:#1b4f8c;--st:#0f7057;--nk:#8c3a1b;
  --pos:#166534;--neg:#991b1b;--pos-bg:#dcfce7;--neg-bg:#fee2e2;
  --warn-bg:#fff3cd;--warn-border:#ffc107;--warn-text:#856404;
  --info-bg:#dbeafe;--info-border:#3b82f6;--info-text:#1e40af;
  --r:10px;--rs:6px;
  font-family:'Georgia','Times New Roman',serif;
}
*{box-sizing:border-box;margin:0;padding:0}
body{background:var(--bg);color:var(--text);min-height:100vh}
a{color:var(--sp)}
.app{max-width:1300px;margin:0 auto;padding:0 24px 60px}

/* HEADER */
header{padding:24px 0 18px;border-bottom:1px solid var(--border-strong);margin-bottom:24px;display:flex;align-items:flex-start;justify-content:space-between;flex-wrap:wrap;gap:12px}
header h1{font-size:21px;font-weight:700;letter-spacing:-0.3px}
header .subtitle{font-size:12px;color:var(--muted);font-family:'Helvetica Neue',Arial,sans-serif;margin-top:3px}
.badge{font-size:11px;border-radius:4px;padding:3px 8px;font-family:'Helvetica Neue',Arial,sans-serif;font-weight:500;white-space:nowrap}
.badge-warn{background:var(--warn-bg);color:var(--warn-text);border:1px solid var(--warn-border)}
.badge-live{background:var(--pos-bg);color:var(--pos);border:1px solid #86efac}
.badge-info{background:var(--info-bg);color:var(--info-text);border:1px solid var(--info-border)}
.header-right{display:flex;flex-direction:column;align-items:flex-end;gap:6px}

/* NAV */
nav{display:flex;gap:2px;margin-bottom:24px;border-bottom:1px solid var(--border-strong);overflow-x:auto}
nav button{font-size:13px;font-family:'Helvetica Neue',Arial,sans-serif;padding:8px 16px;background:none;border:none;border-bottom:2px solid transparent;color:var(--muted);cursor:pointer;margin-bottom:-1px;transition:all .15s;white-space:nowrap;letter-spacing:.3px}
nav button:hover{color:var(--text)}
nav button.active{color:var(--text);border-bottom-color:var(--text);font-weight:600}
.tab-panel{display:none}.tab-panel.active{display:block}

/* METRIC CARDS */
.metric-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(155px,1fr));gap:10px;margin-bottom:20px}
.metric-card{background:var(--surface);border:1px solid var(--border);border-radius:var(--r);padding:13px 15px}
.mc-label{font-size:10px;color:var(--muted);text-transform:uppercase;letter-spacing:.7px;margin-bottom:5px;font-family:'Helvetica Neue',Arial,sans-serif}
.mc-value{font-size:19px;font-weight:700;letter-spacing:-0.5px}
.mc-row{display:flex;justify-content:space-between;align-items:center;margin-top:5px;font-size:11px;font-family:'Helvetica Neue',Arial,sans-serif;color:var(--muted)}
.mc-row span:last-child{font-weight:600}

/* CHART CARD */
.cc{background:var(--surface);border:1px solid var(--border);border-radius:var(--r);padding:18px;margin-bottom:18px}
.cc h3{font-size:13px;font-weight:600;margin-bottom:3px;font-family:'Helvetica Neue',Arial,sans-serif;letter-spacing:.1px}
.cc-sub{font-size:11px;color:var(--muted);margin-bottom:14px;font-family:'Helvetica Neue',Arial,sans-serif}
.cw{position:relative;width:100%}
.cw-sm{height:190px}.cw-md{height:260px}.cw-lg{height:320px}
.legend{display:flex;gap:18px;flex-wrap:wrap;margin-bottom:10px}
.legend-item{display:flex;align-items:center;gap:5px;font-size:11px;color:var(--muted);font-family:'Helvetica Neue',Arial,sans-serif}
.ldot{width:10px;height:10px;border-radius:2px;flex-shrink:0}

/* SECTION HEADER */
.sh{font-size:11px;font-weight:600;color:var(--muted);text-transform:uppercase;letter-spacing:.8px;margin:22px 0 12px;font-family:'Helvetica Neue',Arial,sans-serif;padding-bottom:5px;border-bottom:1px solid var(--border)}

/* PERIOD BAR */
.period-bar{display:flex;gap:3px;margin-bottom:18px;flex-wrap:wrap}
.period-bar button{font-size:12px;font-family:'Helvetica Neue',Arial,sans-serif;padding:4px 11px;border:1px solid var(--border-strong);border-radius:4px;background:none;cursor:pointer;color:var(--muted);transition:all .15s}
.period-bar button:hover{background:var(--surface2);color:var(--text)}
.period-bar button.active{background:var(--text);color:#fff;border-color:var(--text)}

/* TABLE */
.dt{width:100%;border-collapse:collapse;font-family:'Helvetica Neue',Arial,sans-serif;font-size:12px}
.dt th{text-align:left;padding:7px 10px;background:var(--surface2);border:1px solid var(--border);font-weight:600;color:var(--muted);font-size:10px;text-transform:uppercase;letter-spacing:.5px}
.dt td{padding:9px 10px;border:1px solid var(--border)}
.dt tr:hover td{background:var(--surface2)}
.tag{color:#fff;padding:2px 7px;border-radius:3px;font-size:10px;font-weight:700}
.tag-sp{background:var(--sp)}.tag-st{background:var(--st)}.tag-nk{background:var(--nk)}
.tag-etf{background:#7c3aed}.tag-stk{background:#475569}.tag-pea{background:#0369a1}

/* COLORING */
.up{color:var(--pos)}.dn{color:var(--neg)}

/* API SETTINGS PANEL */
.api-panel{background:var(--surface);border:1px solid var(--border);border-radius:var(--r);padding:18px;margin-bottom:20px}
.api-panel h3{font-size:13px;font-weight:600;margin-bottom:10px;font-family:'Helvetica Neue',Arial,sans-serif}
.api-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:14px;align-items:end}
.fg{display:flex;flex-direction:column;gap:4px}
.fg label{font-size:10px;color:var(--muted);text-transform:uppercase;letter-spacing:.6px;font-family:'Helvetica Neue',Arial,sans-serif}
.fg input,.fg select{font-size:12px;font-family:'Helvetica Neue',Arial,sans-serif;padding:7px 9px;border:1px solid var(--border-strong);border-radius:var(--rs);background:var(--surface);color:var(--text);outline:none}
.fg input:focus,.fg select:focus{border-color:var(--text)}
.btn{font-size:12px;font-family:'Helvetica Neue',Arial,sans-serif;padding:8px 18px;border-radius:var(--rs);cursor:pointer;font-weight:600;transition:all .15s;border:1px solid var(--border-strong)}
.btn-primary{background:var(--text);color:#fff;border-color:var(--text)}
.btn-primary:hover{opacity:.85}
.btn-secondary{background:none;color:var(--text)}
.btn-secondary:hover{background:var(--surface2)}
.btn-danger{background:#fee2e2;color:var(--neg);border-color:#fca5a5}
.btn-danger:hover{background:#fecaca}
.btn-sm{padding:5px 11px;font-size:11px}
.status-bar{margin-top:10px;font-size:11px;font-family:'Helvetica Neue',Arial,sans-serif;color:var(--muted)}
.loader{display:inline-block;width:12px;height:12px;border:2px solid var(--border-strong);border-top-color:var(--sp);border-radius:50%;animation:spin .7s linear infinite;vertical-align:middle;margin-right:5px}
@keyframes spin{to{transform:rotate(360deg)}}

/* DCA v2 — PORTFOLIO TABLE */
.dca-portfolio{margin-bottom:18px}
.portfolio-row{background:var(--surface);border:1px solid var(--border);border-radius:var(--rs);padding:12px 14px;margin-bottom:8px;display:grid;gap:8px;grid-template-columns:2fr 1.2fr 1fr 1fr 1fr 1fr 1fr auto;align-items:center}
.portfolio-row.header-row{background:var(--surface2);font-size:10px;font-weight:700;text-transform:uppercase;letter-spacing:.5px;color:var(--muted);font-family:'Helvetica Neue',Arial,sans-serif}
@media(max-width:900px){
  .portfolio-row{grid-template-columns:1fr 1fr;grid-template-rows:auto auto auto auto}
  .portfolio-row.header-row{display:none}
}
.dca-result-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(130px,1fr));gap:10px;margin-bottom:18px}
.dca-metric{background:var(--surface);border:1px solid var(--border);border-radius:var(--r);padding:13px}
.dm-label{font-size:10px;color:var(--muted);text-transform:uppercase;letter-spacing:.6px;margin-bottom:4px;font-family:'Helvetica Neue',Arial,sans-serif}
.dm-val{font-size:17px;font-weight:700}
.dm-sub{font-size:10px;color:var(--faint);font-family:'Helvetica Neue',Arial,sans-serif;margin-top:2px}

/* ENVELOPE BADGE */
.env-pea{background:#0369a1;color:#fff;font-size:9px;padding:1px 5px;border-radius:3px;font-weight:700;font-family:'Helvetica Neue',Arial,sans-serif}
.env-cto{background:#7c3aed;color:#fff;font-size:9px;padding:1px 5px;border-radius:3px;font-weight:700;font-family:'Helvetica Neue',Arial,sans-serif}
.env-std{background:#475569;color:#fff;font-size:9px;padding:1px 5px;border-radius:3px;font-weight:700;font-family:'Helvetica Neue',Arial,sans-serif}

/* CACHE STATUS */
.cache-table{width:100%;font-family:'Helvetica Neue',Arial,sans-serif;font-size:11px;border-collapse:collapse;margin-top:8px}
.cache-table td{padding:5px 10px;border:1px solid var(--border)}
.cache-table th{padding:5px 10px;border:1px solid var(--border);background:var(--surface2);font-size:10px;text-transform:uppercase;letter-spacing:.5px;color:var(--muted)}

/* TWO-COL GRID */
.two-col{display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:18px}

footer{font-size:10px;color:var(--faint);font-family:'Helvetica Neue',Arial,sans-serif;margin-top:36px;padding-top:14px;border-bottom:1px solid var(--border)}
</style>
</head>
<body>
<div class="app">
  <header>
    <div>
      <h1>Global Equity Dashboard</h1>
      <div class="subtitle">S&amp;P 500 · STOXX 600 · Nikkei 225 — with live data feed &amp; DCA portfolio simulator</div>
    </div>
    <div class="header-right">
      <span id="dataStatusBadge" class="badge badge-warn">⚠ Sample data — configure API key</span>
      <span id="lastUpdated" style="font-size:10px;color:var(--faint);font-family:'Helvetica Neue',Arial,sans-serif;display:none"></span>
    </div>
  </header>

  <nav>
    <button class="active" onclick="switchTab('overview',this)">Overview</button>
    <button onclick="switchTab('performance',this)">Performance</button>
    <button onclick="switchTab('compare',this)">Comparison</button>
    <button onclick="switchTab('dca',this)">DCA Portfolio</button>
    <button onclick="switchTab('settings',this)">⚙ Data Settings</button>
  </nav>

  <!-- ═══ OVERVIEW ══════════════════════════════════════════════ -->
  <div id="tab-overview" class="tab-panel active">
    <div id="overviewCards" class="metric-grid"></div>
    <div class="legend">
      <span class="legend-item"><span class="ldot" style="background:var(--sp)"></span>S&amp;P 500 (SPY)</span>
      <span class="legend-item"><span class="ldot" style="background:var(--st);"></span>STOXX 600 (VGK)</span>
      <span class="legend-item"><span class="ldot" style="background:var(--nk)"></span>Nikkei 225 (EWJ)</span>
    </div>
    <div class="cc">
      <h3>Normalized Performance — Base 100</h3>
      <div class="cc-sub">Weekly adjusted close, local currency · rebased to 100 at start of series</div>
      <div class="cw cw-lg"><canvas id="chartBase100" role="img" aria-label="Normalized performance rebased to 100"></canvas></div>
    </div>
    <div class="two-col">
      <div class="cc">
        <h3>Return Comparison by Period</h3>
        <div class="cc-sub">Total return % · local currency</div>
        <div class="cw cw-md"><canvas id="chartReturnBar" role="img" aria-label="Return comparison by period"></canvas></div>
      </div>
      <div class="cc">
        <h3>Maximum Drawdown (full period)</h3>
        <div class="cc-sub">Depth of decline from rolling all-time high</div>
        <div id="mddStats"></div>
      </div>
    </div>
  </div>

  <!-- ═══ PERFORMANCE ═══════════════════════════════════════════ -->
  <div id="tab-performance" class="tab-panel">
    <div class="sh">Historical Charts</div>
    <div class="cc">
      <h3>Cumulative Returns</h3>
      <div class="cc-sub">Rebased to 0% at series start · local currency</div>
      <div class="cw cw-lg"><canvas id="chartCumRet" role="img" aria-label="Cumulative return chart"></canvas></div>
    </div>
    <div class="cc">
      <h3>Drawdown Chart</h3>
      <div class="cc-sub">% decline from rolling all-time high</div>
      <div class="cw cw-md"><canvas id="chartDD" role="img" aria-label="Drawdown chart"></canvas></div>
    </div>
    <div class="two-col">
      <div class="cc">
        <h3>Rolling 52-Week Return — S&amp;P 500</h3>
        <div class="cc-sub">Trailing 1-year return, weekly</div>
        <div class="cw cw-sm"><canvas id="chartRoll" role="img" aria-label="Rolling 52 week return S&P 500"></canvas></div>
      </div>
      <div class="cc">
        <h3>Rolling 52-Week Volatility</h3>
        <div class="cc-sub">Annualized vol of weekly returns</div>
        <div class="cw cw-sm"><canvas id="chartVol" role="img" aria-label="Rolling volatility chart"></canvas></div>
      </div>
    </div>
    <div class="cc">
      <h3>S&amp;P 500 · Price + 50W / 200W Moving Averages</h3>
      <div class="cc-sub">Weekly adjusted close (USD)</div>
      <div class="cw cw-lg"><canvas id="chartMA" role="img" aria-label="S&P 500 with moving averages"></canvas></div>
    </div>
  </div>

  <!-- ═══ COMPARISON ════════════════════════════════════════════ -->
  <div id="tab-compare" class="tab-panel">
    <div class="sh">Period Selector</div>
    <div class="period-bar" id="periodBar">
      <button onclick="setPeriod('1M',this)">1M</button>
      <button onclick="setPeriod('3M',this)">3M</button>
      <button onclick="setPeriod('6M',this)">6M</button>
      <button class="active" onclick="setPeriod('1Y',this)">1Y</button>
      <button onclick="setPeriod('3Y',this)">3Y</button>
      <button onclick="setPeriod('5Y',this)">5Y</button>
      <button onclick="setPeriod('MAX',this)">Max</button>
    </div>
    <div id="compareTable" class="cc"></div>
    <div class="cc" style="margin-top:18px">
      <h3>Period Performance Chart</h3>
      <div class="cc-sub">Cumulative return % for selected period</div>
      <div class="cw cw-lg"><canvas id="chartPeriod" role="img" aria-label="Period performance chart"></canvas></div>
    </div>
    <div class="cc" style="margin-top:18px">
      <h3>Correlation Matrix</h3>
      <div class="cc-sub">Weekly return correlations over full period · local currency</div>
      <div id="corrMatrix"></div>
    </div>
  </div>

  <!-- ═══ DCA PORTFOLIO v2 ══════════════════════════════════════ -->
  <div id="tab-dca" class="tab-panel">
    <div class="sh">Portfolio Positions</div>

    <!-- Global sim params -->
    <div class="cc">
      <h3>Global Parameters</h3>
      <div class="cc-sub">Applied to all positions unless overridden at row level</div>
      <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(170px,1fr));gap:12px;margin-top:12px">
        <div class="fg"><label>Simulation Start</label><input type="date" id="gStart" value="2020-01-06"></div>
        <div class="fg"><label>Simulation End</label><input type="date" id="gEnd" value="2025-01-06"></div>
        <div class="fg"><label>Annual Fee / Slippage (%)</label><input type="number" id="gFees" value="0.2" min="0" max="5" step="0.1"></div>
        <div class="fg"><label>Annual Contrib Increase (%)</label><input type="number" id="gAnnInc" value="0" min="0" max="20" step="0.5"></div>
        <div class="fg"><label>Base Currency Display</label>
          <select id="gCurrency"><option value="USD">USD</option><option value="EUR">EUR</option><option value="JPY">JPY</option><option value="local">Local (each)</option></select>
        </div>
      </div>
    </div>

    <!-- Position rows -->
    <div class="sh">Positions <span style="color:var(--faint);font-weight:400;text-transform:none;letter-spacing:0">(add one row per asset)</span></div>
    <div id="positionList" class="dca-portfolio"></div>
    <div style="display:flex;gap:8px;margin-bottom:20px;flex-wrap:wrap">
      <button class="btn btn-secondary btn-sm" onclick="addPosition('index')">＋ Add Index</button>
      <button class="btn btn-secondary btn-sm" onclick="addPosition('etf')">＋ Add ETF</button>
      <button class="btn btn-secondary btn-sm" onclick="addPosition('stock')">＋ Add Stock</button>
      <button class="btn btn-primary" onclick="runPortfolio()" style="margin-left:auto">Run Simulation →</button>
    </div>
    <div id="dcaResults" style="display:none">
      <div class="sh">Portfolio Results</div>
      <div id="dcaMetrics" class="dca-result-grid"></div>
      <div class="cc">
        <h3>Portfolio Value vs. Capital Invested</h3>
        <div class="cc-sub">All positions combined · converted to display currency where possible</div>
        <div class="cw cw-lg"><canvas id="chartDCA1" role="img" aria-label="DCA portfolio value vs capital invested"></canvas></div>
      </div>
      <div class="cc" style="margin-top:18px">
        <h3>DCA vs. Lump Sum</h3>
        <div class="cc-sub">Same total capital deployed at start vs spread over time</div>
        <div class="cw cw-md"><canvas id="chartDCA2" role="img" aria-label="DCA vs lump sum comparison"></canvas></div>
      </div>
      <div class="cc" style="margin-top:18px">
        <h3>Position Breakdown</h3>
        <div id="posBreakdown"></div>
      </div>
      <div class="cc" style="margin-top:18px">
        <h3>Contribution Timeline</h3>
        <div id="dcaContribTable"></div>
      </div>
    </div>
  </div>

  <!-- ═══ DATA SETTINGS ══════════════════════════════════════════ -->
  <div id="tab-settings" class="tab-panel">
    <div class="sh">Live Data Feed — Alpha Vantage API</div>
    <div class="api-panel">
      <h3>API Configuration</h3>
      <div style="font-size:12px;color:var(--muted);font-family:'Helvetica Neue',Arial,sans-serif;margin-bottom:14px;line-height:1.7">
        Get a free API key at <a href="https://www.alphavantage.co/support/#api-key" target="_blank">alphavantage.co</a> (25 req/day free).
        Data is cached locally in your browser (IndexedDB) and refreshed automatically if older than 24h.
        The three benchmarks are pulled as ETF proxies: <strong>SPY</strong> (S&amp;P 500), <strong>VGK</strong> (STOXX 600), <strong>EWJ</strong> (Nikkei 225).
        You may also add any other ticker symbol in the DCA simulator.
      </div>
      <div class="api-grid">
        <div class="fg" style="grid-column:1/-1">
          <label>Alpha Vantage API Key</label>
          <input type="text" id="apiKeyInput" placeholder="Paste your free API key here (e.g. ABC123XYZ)" style="font-family:monospace;font-size:13px">
        </div>
        <div class="fg">
          <label>Benchmark 1 — S&amp;P 500 proxy</label>
          <input type="text" id="sym0" value="SPY" style="font-family:monospace">
        </div>
        <div class="fg">
          <label>Benchmark 2 — STOXX 600 proxy</label>
          <input type="text" id="sym1" value="VGK" style="font-family:monospace">
        </div>
        <div class="fg">
          <label>Benchmark 3 — Nikkei 225 proxy</label>
          <input type="text" id="sym2" value="EWJ" style="font-family:monospace">
        </div>
        <div style="display:flex;gap:8px;align-items:flex-end;flex-wrap:wrap">
          <button class="btn btn-primary" onclick="fetchAndStore()">Fetch &amp; Refresh Data</button>
          <button class="btn btn-secondary" onclick="clearCache()">Clear Cache</button>
        </div>
      </div>
      <div class="status-bar" id="fetchStatus"></div>
    </div>

    <div class="sh">Cache Status</div>
    <div class="cc">
      <h3>Locally Stored Data</h3>
      <div class="cc-sub">Data is stored in your browser's IndexedDB. It persists across sessions.</div>
      <div id="cacheStatus"><em style="font-size:12px;color:var(--faint);font-family:'Helvetica Neue',Arial,sans-serif">Loading cache info…</em></div>
      <div style="margin-top:12px;font-size:11px;color:var(--muted);font-family:'Helvetica Neue',Arial,sans-serif;line-height:1.8">
        <strong>How to update data:</strong><br>
        1. Enter your API key above and click "Fetch &amp; Refresh Data".<br>
        2. The dashboard fetches weekly adjusted close for each benchmark (20+ years of history).<br>
        3. Data is stored in IndexedDB under the ticker symbol as key.<br>
        4. On next load, cached data is used unless it's older than 24 hours (auto-refresh).<br>
        5. Custom tickers added in the DCA simulator are also fetched and cached on demand.<br>
        <br>
        <strong>Rate limits:</strong> Alpha Vantage free tier = 25 requests/day. The 3 benchmarks = 3 requests. Each custom DCA ticker = 1 additional request.
      </div>
    </div>

    <div class="sh">Input Schema</div>
    <div class="cc">
      <h3>Expected Data Format</h3>
      <div class="cc-sub">If you replace the API with your own data source, format it as follows</div>
      <pre style="background:var(--surface2);padding:14px;border-radius:var(--rs);font-size:11px;overflow-x:auto;font-family:monospace;color:var(--text);margin-top:10px">{
  "sp500": {
    "label": "S&amp;P 500",
    "currency": "USD",
    "ticker": "SPY",
    "series": [
      { "date": "2020-01-06", "close": 323.54 },
      { "date": "2020-01-13", "close": 325.11 },
      ...
    ]
  },
  "stoxx":  { "label": "STOXX 600", "currency": "EUR", "ticker": "VGK", "series": [...] },
  "nikkei": { "label": "Nikkei 225","currency": "JPY", "ticker": "EWJ", "series": [...] }
}
// Dates: ISO 8601 (YYYY-MM-DD), chronological order, weekly frequency
// Close: adjusted close (split + dividend adjusted) preferred</pre>
    </div>
  </div>

  <footer>
    Price return data via Alpha Vantage (ETF proxies) or sample data. All comparisons in local currency unless converted. Not investment advice.
    Dashboard v2 — data cached in browser IndexedDB, refreshed every 24h.
  </footer>
</div>

<!-- ═══════════════════════════════════════════════════════════════
     JAVASCRIPT — DATA LAYER + CHARTS + DCA SIMULATOR v2
════════════════════════════════════════════════════════════════ -->
<script>
'use strict';

// ╔══════════════════════════════════════════════════════════════╗
// ║  FALLBACK SAMPLE DATA — used before live API data is loaded  ║
// ╚══════════════════════════════════════════════════════════════╝
const FALLBACK_DATA = {"sp500":{"label":"S&P 500","currency":"USD","series":[{"date":"2020-01-06","close":3343.18},{"date":"2020-01-13","close":3474.28},{"date":"2020-01-20","close":3488.0},{"date":"2020-01-27","close":3429.89},{"date":"2020-02-03","close":3345.28},{"date":"2020-02-10","close":3355.55},{"date":"2020-02-17","close":3229.33},{"date":"2020-02-24","close":3075.85},{"date":"2020-03-02","close":3051.78},{"date":"2020-03-09","close":3022.93},{"date":"2020-03-16","close":3025.38},{"date":"2020-03-23","close":2934.05},{"date":"2020-03-30","close":2955.83},{"date":"2020-04-06","close":2972.6},{"date":"2020-04-13","close":2883.83},{"date":"2020-04-20","close":2944.15},{"date":"2020-04-27","close":2989.48},{"date":"2020-05-04","close":3196.34},{"date":"2020-05-11","close":3236.02},{"date":"2020-05-18","close":3247.89},{"date":"2020-05-25","close":3373.83},{"date":"2020-06-01","close":3415.36},{"date":"2020-06-08","close":3519.25},{"date":"2020-06-15","close":3512.73},{"date":"2020-06-22","close":3557.69},{"date":"2020-06-29","close":3676.47},{"date":"2020-07-06","close":3768.22},{"date":"2020-07-13","close":3807.92},{"date":"2020-07-20","close":3733.47},{"date":"2020-07-27","close":3802.75},{"date":"2020-08-03","close":3818.77},{"date":"2020-08-10","close":3896.96},{"date":"2020-08-17","close":3927.01},{"date":"2020-08-24","close":4044.37},{"date":"2020-08-31","close":4048.41},{"date":"2020-09-07","close":4078.17},{"date":"2020-09-14","close":4156.1},{"date":"2020-09-21","close":4054.11},{"date":"2020-09-28","close":4022.85},{"date":"2020-10-05","close":3982.04},{"date":"2020-10-12","close":4193.42},{"date":"2020-10-19","close":4193.28},{"date":"2020-10-26","close":4271.86},{"date":"2020-11-02","close":4348.34},{"date":"2020-11-09","close":4327.84},{"date":"2020-11-16","close":4173.03},{"date":"2020-11-23","close":4284.53},{"date":"2020-11-30","close":4250.9},{"date":"2020-12-07","close":4337.66},{"date":"2020-12-14","close":4208.22},{"date":"2020-12-21","close":4171.98},{"date":"2020-12-28","close":4314.79},{"date":"2021-01-04","close":4481.93},{"date":"2021-01-11","close":4348.48},{"date":"2021-01-18","close":4215.81},{"date":"2021-01-25","close":4220.79},{"date":"2021-02-01","close":4308.04},{"date":"2021-02-08","close":4335.23},{"date":"2021-02-15","close":4378.2},{"date":"2021-02-22","close":4281.22},{"date":"2021-03-01","close":4354.33},{"date":"2021-03-08","close":4487.67},{"date":"2021-03-15","close":4449.28},{"date":"2021-03-22","close":4302.71},{"date":"2021-03-29","close":4231.64},{"date":"2021-04-05","close":4322.72},{"date":"2021-04-12","close":4149.11},{"date":"2021-04-19","close":4149.08},{"date":"2021-04-26","close":4056.96},{"date":"2021-05-03","close":4052.95},{"date":"2021-05-10","close":4037.5},{"date":"2021-05-17","close":4048.34},{"date":"2021-05-24","close":4212.53},{"date":"2021-05-31","close":4266.74},{"date":"2021-06-07","close":4421.26},{"date":"2021-06-14","close":4415.76},{"date":"2021-06-21","close":4373.19},{"date":"2021-06-28","close":4424.84},{"date":"2021-07-05","close":4131.88},{"date":"2021-07-12","close":4137.21},{"date":"2021-07-19","close":4163.28},{"date":"2021-07-26","close":4046.11},{"date":"2021-08-02","close":4102.65},{"date":"2021-08-09","close":4055.03},{"date":"2021-08-16","close":3822.33},{"date":"2021-08-23","close":3810.72},{"date":"2021-08-30","close":3727.25},{"date":"2021-09-06","close":3687.54},{"date":"2021-09-13","close":3681.95},{"date":"2021-09-20","close":3807.43},{"date":"2021-09-27","close":3825.97},{"date":"2021-10-04","close":3832.0},{"date":"2021-10-11","close":3878.24},{"date":"2021-10-18","close":3715.2},{"date":"2021-10-25","close":3840.77},{"date":"2021-11-01","close":3747.44},{"date":"2021-11-08","close":3797.4},{"date":"2021-11-15","close":3700.53},{"date":"2021-11-22","close":3619.68},{"date":"2021-11-29","close":3592.25},{"date":"2021-12-06","close":3774.94},{"date":"2021-12-13","close":3850.04},{"date":"2021-12-20","close":3801.09},{"date":"2021-12-27","close":3782.84},{"date":"2022-01-03","close":3684.07},{"date":"2022-01-10","close":3689.35},{"date":"2022-01-17","close":3645.27},{"date":"2022-01-24","close":3720.04},{"date":"2022-01-31","close":3604.36},{"date":"2022-02-07","close":3582.55},{"date":"2022-02-14","close":3516.07},{"date":"2022-02-21","close":3461.45},{"date":"2022-02-28","close":3531.5},{"date":"2022-03-07","close":3550.76},{"date":"2022-03-14","close":3611.25},{"date":"2022-03-21","close":3728.55},{"date":"2022-03-28","close":3845.86},{"date":"2022-04-04","close":3724.9},{"date":"2022-04-11","close":3783.8},{"date":"2022-04-18","close":3629.34},{"date":"2022-04-25","close":3631.84},{"date":"2022-05-02","close":3818.77},{"date":"2022-05-09","close":3809.05},{"date":"2022-05-16","close":3782.74},{"date":"2022-05-23","close":3807.55},{"date":"2022-05-30","close":3817.96},{"date":"2022-06-06","close":3829.24},{"date":"2022-06-13","close":3766.13},{"date":"2022-06-20","close":3878.05},{"date":"2022-06-27","close":3974.15},{"date":"2022-07-04","close":3962.19},{"date":"2022-07-11","close":4002.58},{"date":"2022-07-18","close":4078.21},{"date":"2022-07-25","close":4194.22},{"date":"2022-08-01","close":4245.23},{"date":"2022-08-08","close":4329.41},{"date":"2022-08-15","close":4310.9},{"date":"2022-08-22","close":4206.95},{"date":"2022-08-29","close":4164.76},{"date":"2022-09-05","close":4281.84},{"date":"2022-09-12","close":4397.66},{"date":"2022-09-19","close":4423.84},{"date":"2022-09-26","close":4371.6},{"date":"2022-10-03","close":4415.38},{"date":"2022-10-10","close":4613.1},{"date":"2022-10-17","close":4782.65},{"date":"2022-10-24","close":4712.49},{"date":"2022-10-31","close":4718.14},{"date":"2022-11-07","close":4560.64},{"date":"2022-11-14","close":4443.32},{"date":"2022-11-21","close":4474.46},{"date":"2022-11-28","close":4487.47},{"date":"2022-12-05","close":4607.36},{"date":"2022-12-12","close":4766.35},{"date":"2022-12-19","close":4877.86},{"date":"2022-12-26","close":5052.76},{"date":"2023-01-02","close":4995.63},{"date":"2023-01-09","close":4867.94},{"date":"2023-01-16","close":4940.41},{"date":"2023-01-23","close":5294.12},{"date":"2023-01-30","close":5353.7},{"date":"2023-02-06","close":5213.88},{"date":"2023-02-13","close":5257.52},{"date":"2023-02-20","close":5460.44},{"date":"2023-02-27","close":5333.43},{"date":"2023-03-06","close":5453.91},{"date":"2023-03-13","close":5383.62},{"date":"2023-03-20","close":5570.13},{"date":"2023-03-27","close":5693.41},{"date":"2023-04-03","close":5749.9},{"date":"2023-04-10","close":6058.08},{"date":"2023-04-17","close":6010.27},{"date":"2023-04-24","close":5921.73},{"date":"2023-05-01","close":6216.53},{"date":"2023-05-08","close":6095.92},{"date":"2023-05-15","close":6454.6},{"date":"2023-05-22","close":6462.88},{"date":"2023-05-29","close":6312.19},{"date":"2023-06-05","close":6326.33},{"date":"2023-06-12","close":6361.48},{"date":"2023-06-19","close":6408.13},{"date":"2023-06-26","close":6392.08},{"date":"2023-07-03","close":6581.97},{"date":"2023-07-10","close":6225.86},{"date":"2023-07-17","close":6154.3},{"date":"2023-07-24","close":6128.15},{"date":"2023-07-31","close":6427.58},{"date":"2023-08-07","close":6129.68},{"date":"2023-08-14","close":6091.82},{"date":"2023-08-21","close":5933.99},{"date":"2023-08-28","close":5849.68},{"date":"2023-09-04","close":5957.55},{"date":"2023-09-11","close":6032.74},{"date":"2023-09-18","close":6267.84},{"date":"2023-09-25","close":6188.86},{"date":"2023-10-02","close":6244.75},{"date":"2023-10-09","close":6445.07},{"date":"2023-10-16","close":6607.14},{"date":"2023-10-23","close":6566.94},{"date":"2023-10-30","close":6769.94},{"date":"2023-11-06","close":6630.74},{"date":"2023-11-13","close":6951.91},{"date":"2023-11-20","close":6994.71},{"date":"2023-11-27","close":6991.02},{"date":"2023-12-04","close":7054.55},{"date":"2023-12-11","close":7222.13},{"date":"2023-12-18","close":7560.26},{"date":"2023-12-25","close":7550.73},{"date":"2024-01-01","close":7498.89},{"date":"2024-01-08","close":7626.89},{"date":"2024-01-15","close":7479.82},{"date":"2024-01-22","close":7186.14},{"date":"2024-01-29","close":7354.45},{"date":"2024-02-05","close":7301.79},{"date":"2024-02-12","close":7527.25},{"date":"2024-02-19","close":7353.58},{"date":"2024-02-26","close":6856.36},{"date":"2024-03-04","close":6920.76},{"date":"2024-03-11","close":6963.46},{"date":"2024-03-18","close":7263.85},{"date":"2024-03-25","close":7376.61},{"date":"2024-04-01","close":7450.8},{"date":"2024-04-08","close":7578.0},{"date":"2024-04-15","close":7526.03},{"date":"2024-04-22","close":7557.85},{"date":"2024-04-29","close":7323.78},{"date":"2024-05-06","close":7436.27},{"date":"2024-05-13","close":7304.95},{"date":"2024-05-20","close":7240.63},{"date":"2024-05-27","close":7385.06},{"date":"2024-06-03","close":7573.06},{"date":"2024-06-10","close":7401.93},{"date":"2024-06-17","close":7799.39},{"date":"2024-06-24","close":7702.63},{"date":"2024-07-01","close":7882.85},{"date":"2024-07-08","close":8090.74},{"date":"2024-07-15","close":8154.72},{"date":"2024-07-22","close":8208.58},{"date":"2024-07-29","close":8604.84},{"date":"2024-08-05","close":8818.31},{"date":"2024-08-12","close":8937.3},{"date":"2024-08-19","close":8558.98},{"date":"2024-08-26","close":8420.06},{"date":"2024-09-02","close":8687.84},{"date":"2024-09-09","close":8749.99},{"date":"2024-09-16","close":8563.41},{"date":"2024-09-23","close":8446.45},{"date":"2024-09-30","close":8401.32},{"date":"2024-10-07","close":8565.98},{"date":"2024-10-14","close":8668.92},{"date":"2024-10-21","close":8907.77},{"date":"2024-10-28","close":8747.91},{"date":"2024-11-04","close":8986.4},{"date":"2024-11-11","close":8894.82},{"date":"2024-11-18","close":8848.89},{"date":"2024-11-25","close":9261.24},{"date":"2024-12-02","close":9299.61},{"date":"2024-12-09","close":9288.51},{"date":"2024-12-16","close":9261.01},{"date":"2024-12-23","close":9193.45},{"date":"2024-12-30","close":9580.23},{"date":"2025-01-06","close":9937.82}]},"stoxx":{"label":"STOXX 600","currency":"EUR","series":[{"date":"2020-01-06","close":419.67},{"date":"2020-01-13","close":414.01},{"date":"2020-01-20","close":418.13},{"date":"2020-01-27","close":419.97},{"date":"2020-02-03","close":428.31},{"date":"2020-02-10","close":415.67},{"date":"2020-02-17","close":406.14},{"date":"2020-02-24","close":393.89},{"date":"2020-03-02","close":379.27},{"date":"2020-03-09","close":367.07},{"date":"2020-03-16","close":357.88},{"date":"2020-03-23","close":352.43},{"date":"2020-03-30","close":347.53},{"date":"2020-04-06","close":352.96},{"date":"2020-04-13","close":350.84},{"date":"2020-04-20","close":328.81},{"date":"2020-04-27","close":339.69},{"date":"2020-05-04","close":338.82},{"date":"2020-05-11","close":335.33},{"date":"2020-05-18","close":339.4},{"date":"2020-05-25","close":343.23},{"date":"2020-06-01","close":345.75},{"date":"2020-06-08","close":341.34},{"date":"2020-06-15","close":344.9},{"date":"2020-06-22","close":335.38},{"date":"2020-06-29","close":348.42},{"date":"2020-07-06","close":340.86},{"date":"2020-07-13","close":341.38},{"date":"2020-07-20","close":343.63},{"date":"2020-07-27","close":347.42},{"date":"2020-08-03","close":345.92},{"date":"2020-08-10","close":350.05},{"date":"2020-08-17","close":323.29},{"date":"2020-08-24","close":321.99},{"date":"2020-08-31","close":320.29},{"date":"2020-09-07","close":316.67},{"date":"2020-09-14","close":327.04},{"date":"2020-09-21","close":319.47},{"date":"2020-09-28","close":318.32},{"date":"2020-10-05","close":303.72},{"date":"2020-10-12","close":305.04},{"date":"2020-10-19","close":293.69},{"date":"2020-10-26","close":283.08},{"date":"2020-11-02","close":297.83},{"date":"2020-11-09","close":302.01},{"date":"2020-11-16","close":301.41},{"date":"2020-11-23","close":302.02},{"date":"2020-11-30","close":291.92},{"date":"2020-12-07","close":284.61},{"date":"2020-12-14","close":286.81},{"date":"2020-12-21","close":273.03},{"date":"2020-12-28","close":274.21},{"date":"2021-01-04","close":263.26},{"date":"2021-01-11","close":263.5},{"date":"2021-01-18","close":256.54},{"date":"2021-01-25","close":266.38},{"date":"2021-02-01","close":272.05},{"date":"2021-02-08","close":268.43},{"date":"2021-02-15","close":256.79},{"date":"2021-02-22","close":251.84},{"date":"2021-03-01","close":251.07},{"date":"2021-03-08","close":245.09},{"date":"2021-03-15","close":246.22},{"date":"2021-03-22","close":251.31},{"date":"2021-03-29","close":250.55},{"date":"2021-04-05","close":247.67},{"date":"2021-04-12","close":251.62},{"date":"2021-04-19","close":249.5},{"date":"2021-04-26","close":253.87},{"date":"2021-05-03","close":251.61},{"date":"2021-05-10","close":260.47},{"date":"2021-05-17","close":258.38},{"date":"2021-05-24","close":251.84},{"date":"2021-05-31","close":251.95},{"date":"2021-06-07","close":247.94},{"date":"2021-06-14","close":242.33},{"date":"2021-06-21","close":241.21},{"date":"2021-06-28","close":244.9},{"date":"2021-07-05","close":232.84},{"date":"2021-07-12","close":232.21},{"date":"2021-07-19","close":231.02},{"date":"2021-07-26","close":229.87},{"date":"2021-08-02","close":233.67},{"date":"2021-08-09","close":226.51},{"date":"2021-08-16","close":229.53},{"date":"2021-08-23","close":227.98},{"date":"2021-08-30","close":228.18},{"date":"2021-09-06","close":226.68},{"date":"2021-09-13","close":224.65},{"date":"2021-09-20","close":221.66},{"date":"2021-09-27","close":223.39},{"date":"2021-10-04","close":233.89},{"date":"2021-10-11","close":239.17},{"date":"2021-10-18","close":243.47},{"date":"2021-10-25","close":246.23},{"date":"2021-11-01","close":243.27},{"date":"2021-11-08","close":246.3},{"date":"2021-11-15","close":257.76},{"date":"2021-11-22","close":250.13},{"date":"2021-11-29","close":254.56},{"date":"2021-12-06","close":260.16},{"date":"2021-12-13","close":261.55},{"date":"2021-12-20","close":265.97},{"date":"2021-12-27","close":274.18},{"date":"2022-01-03","close":287.95},{"date":"2022-01-10","close":296.3},{"date":"2022-01-17","close":307.33},{"date":"2022-01-24","close":309.44},{"date":"2022-01-31","close":315.07},{"date":"2022-02-07","close":316.15},{"date":"2022-02-14","close":318.1},{"date":"2022-02-21","close":314.68},{"date":"2022-02-28","close":319.4},{"date":"2022-03-07","close":329.87},{"date":"2022-03-14","close":328.62},{"date":"2022-03-21","close":330.42},{"date":"2022-03-28","close":335.02},{"date":"2022-04-04","close":335.14},{"date":"2022-04-11","close":342.25},{"date":"2022-04-18","close":344.22},{"date":"2022-04-25","close":335.3},{"date":"2022-05-02","close":327.65},{"date":"2022-05-09","close":333.07},{"date":"2022-05-16","close":337.85},{"date":"2022-05-23","close":346.37},{"date":"2022-05-30","close":348.39},{"date":"2022-06-06","close":350.06},{"date":"2022-06-13","close":337.99},{"date":"2022-06-20","close":348.82},{"date":"2022-06-27","close":341.78},{"date":"2022-07-04","close":349.97},{"date":"2022-07-11","close":341.24},{"date":"2022-07-18","close":336.33},{"date":"2022-07-25","close":337.63},{"date":"2022-08-01","close":334.55},{"date":"2022-08-08","close":329.49},{"date":"2022-08-15","close":336.32},{"date":"2022-08-22","close":341.61},{"date":"2022-08-29","close":344.81},{"date":"2022-09-05","close":342.38},{"date":"2022-09-12","close":336.29},{"date":"2022-09-19","close":332.89},{"date":"2022-09-26","close":329.2},{"date":"2022-10-03","close":329.19},{"date":"2022-10-10","close":335.1},{"date":"2022-10-17","close":334.04},{"date":"2022-10-24","close":328.39},{"date":"2022-10-31","close":324.09},{"date":"2022-11-07","close":333.73},{"date":"2022-11-14","close":335.18},{"date":"2022-11-21","close":337.18},{"date":"2022-11-28","close":339.55},{"date":"2022-12-05","close":344.34},{"date":"2022-12-12","close":345.68},{"date":"2022-12-19","close":355.18},{"date":"2022-12-26","close":361.99},{"date":"2023-01-02","close":340.11},{"date":"2023-01-09","close":339.38},{"date":"2023-01-16","close":362.79},{"date":"2023-01-23","close":352.89},{"date":"2023-01-30","close":354.23},{"date":"2023-02-06","close":363.22},{"date":"2023-02-13","close":363.58},{"date":"2023-02-20","close":374.96},{"date":"2023-02-27","close":364.91},{"date":"2023-03-06","close":355.23},{"date":"2023-03-13","close":354.11},{"date":"2023-03-20","close":348.7},{"date":"2023-03-27","close":340.82},{"date":"2023-04-03","close":345.54},{"date":"2023-04-10","close":348.0},{"date":"2023-04-17","close":348.46},{"date":"2023-04-24","close":345.71},{"date":"2023-05-01","close":348.13},{"date":"2023-05-08","close":347.59},{"date":"2023-05-15","close":342.38},{"date":"2023-05-22","close":346.69},{"date":"2023-05-29","close":349.96},{"date":"2023-06-05","close":350.99},{"date":"2023-06-12","close":356.85},{"date":"2023-06-19","close":348.58},{"date":"2023-06-26","close":347.86},{"date":"2023-07-03","close":344.22},{"date":"2023-07-10","close":354.96},{"date":"2023-07-17","close":359.4},{"date":"2023-07-24","close":377.11},{"date":"2023-07-31","close":390.95},{"date":"2023-08-07","close":388.18},{"date":"2023-08-14","close":379.3},{"date":"2023-08-21","close":383.83},{"date":"2023-08-28","close":381.86},{"date":"2023-09-04","close":380.96},{"date":"2023-09-11","close":372.51},{"date":"2023-09-18","close":377.94},{"date":"2023-09-25","close":379.94},{"date":"2023-10-02","close":383.79},{"date":"2023-10-09","close":386.88},{"date":"2023-10-16","close":379.63},{"date":"2023-10-23","close":361.64},{"date":"2023-10-30","close":359.88},{"date":"2023-11-06","close":355.25},{"date":"2023-11-13","close":351.5},{"date":"2023-11-20","close":359.47},{"date":"2023-11-27","close":359.09},{"date":"2023-12-04","close":371.74},{"date":"2023-12-11","close":373.67},{"date":"2023-12-18","close":379.85},{"date":"2023-12-25","close":384.59},{"date":"2024-01-01","close":391.97},{"date":"2024-01-08","close":381.61},{"date":"2024-01-15","close":391.4},{"date":"2024-01-22","close":392.78},{"date":"2024-01-29","close":384.8},{"date":"2024-02-05","close":390.53},{"date":"2024-02-12","close":393.98},{"date":"2024-02-19","close":405.86},{"date":"2024-02-26","close":412.97},{"date":"2024-03-04","close":416.81},{"date":"2024-03-11","close":402.44},{"date":"2024-03-18","close":418.17},{"date":"2024-03-25","close":432.68},{"date":"2024-04-01","close":440.7},{"date":"2024-04-08","close":445.74},{"date":"2024-04-15","close":458.47},{"date":"2024-04-22","close":450.24},{"date":"2024-04-29","close":457.82},{"date":"2024-05-06","close":458.57},{"date":"2024-05-13","close":449.0},{"date":"2024-05-20","close":453.19},{"date":"2024-05-27","close":457.28},{"date":"2024-06-03","close":475.44},{"date":"2024-06-10","close":486.05},{"date":"2024-06-17","close":469.6},{"date":"2024-06-24","close":450.11},{"date":"2024-07-01","close":449.83},{"date":"2024-07-08","close":448.26},{"date":"2024-07-15","close":439.54},{"date":"2024-07-22","close":426.03},{"date":"2024-07-29","close":424.68},{"date":"2024-08-05","close":414.38},{"date":"2024-08-12","close":408.54},{"date":"2024-08-19","close":416.9},{"date":"2024-08-26","close":419.59},{"date":"2024-09-02","close":413.1},{"date":"2024-09-09","close":403.38},{"date":"2024-09-16","close":402.16},{"date":"2024-09-23","close":418.41},{"date":"2024-09-30","close":414.2},{"date":"2024-10-07","close":430.91},{"date":"2024-10-14","close":423.93},{"date":"2024-10-21","close":422.43},{"date":"2024-10-28","close":429.49},{"date":"2024-11-04","close":422.66},{"date":"2024-11-11","close":423.66},{"date":"2024-11-18","close":411.58},{"date":"2024-11-25","close":418.15},{"date":"2024-12-02","close":429.52},{"date":"2024-12-09","close":423.85},{"date":"2024-12-16","close":426.08},{"date":"2024-12-23","close":422.72},{"date":"2024-12-30","close":403.41},{"date":"2025-01-06","close":429.23}]},"nikkei":{"label":"Nikkei 225","currency":"JPY","series":[{"date":"2020-01-06","close":23752.45},{"date":"2020-01-13","close":24625.87},{"date":"2020-01-20","close":24032.52},{"date":"2020-01-27","close":24738.83},{"date":"2020-02-03","close":24597.1},{"date":"2020-02-10","close":24454.59},{"date":"2020-02-17","close":25427.61},{"date":"2020-02-24","close":25192.18},{"date":"2020-03-02","close":24820.54},{"date":"2020-03-09","close":24983.93},{"date":"2020-03-16","close":25427.08},{"date":"2020-03-23","close":25185.68},{"date":"2020-03-30","close":25764.91},{"date":"2020-04-06","close":25240.18},{"date":"2020-04-13","close":25145.88},{"date":"2020-04-20","close":25002.42},{"date":"2020-04-27","close":24250.85},{"date":"2020-05-04","close":23407.18},{"date":"2020-05-11","close":22518.79},{"date":"2020-05-18","close":22514.53},{"date":"2020-05-25","close":22551.66},{"date":"2020-06-01","close":22496.37},{"date":"2020-06-08","close":22684.93},{"date":"2020-06-15","close":22001.01},{"date":"2020-06-22","close":22094.18},{"date":"2020-06-29","close":22384.03},{"date":"2020-07-06","close":23002.61},{"date":"2020-07-13","close":22613.96},{"date":"2020-07-20","close":22508.81},{"date":"2020-07-27","close":21422.59},{"date":"2020-08-03","close":21155.91},{"date":"2020-08-10","close":19934.19},{"date":"2020-08-17","close":19192.36},{"date":"2020-08-24","close":19816.3},{"date":"2020-08-31","close":18669.38},{"date":"2020-09-07","close":19115.04},{"date":"2020-09-14","close":19317.51},{"date":"2020-09-21","close":19178.53},{"date":"2020-09-28","close":19452.46},{"date":"2020-10-05","close":19767.62},{"date":"2020-10-12","close":20378.54},{"date":"2020-10-19","close":20277.97},{"date":"2020-10-26","close":19976.41},{"date":"2020-11-02","close":19672.58},{"date":"2020-11-09","close":19169.28},{"date":"2020-11-16","close":19173.04},{"date":"2020-11-23","close":18786.8},{"date":"2020-11-30","close":19379.86},{"date":"2020-12-07","close":18427.21},{"date":"2020-12-14","close":17902.41},{"date":"2020-12-21","close":17460.51},{"date":"2020-12-28","close":16499.63},{"date":"2021-01-04","close":17418.63},{"date":"2021-01-11","close":16316.67},{"date":"2021-01-18","close":16212.34},{"date":"2021-01-25","close":16000.95},{"date":"2021-02-01","close":16777.14},{"date":"2021-02-08","close":15901.19},{"date":"2021-02-15","close":16404.63},{"date":"2021-02-22","close":16098.41},{"date":"2021-03-01","close":16052.38},{"date":"2021-03-08","close":15779.16},{"date":"2021-03-15","close":16085.15},{"date":"2021-03-22","close":15608.04},{"date":"2021-03-29","close":15596.4},{"date":"2021-04-05","close":15772.76},{"date":"2021-04-12","close":16622.65},{"date":"2021-04-19","close":15572.37},{"date":"2021-04-26","close":16268.65},{"date":"2021-05-03","close":16726.21},{"date":"2021-05-10","close":16527.19},{"date":"2021-05-17","close":16691.61},{"date":"2021-05-24","close":16501.05},{"date":"2021-05-31","close":17297.09},{"date":"2021-06-07","close":17423.04},{"date":"2021-06-14","close":17344.19},{"date":"2021-06-21","close":17259.7},{"date":"2021-06-28","close":17189.06},{"date":"2021-07-05","close":17129.76},{"date":"2021-07-12","close":16741.33},{"date":"2021-07-19","close":17750.45},{"date":"2021-07-26","close":16858.45},{"date":"2021-08-02","close":15276.09},{"date":"2021-08-09","close":15246.1},{"date":"2021-08-16","close":15206.17},{"date":"2021-08-23","close":15386.23},{"date":"2021-08-30","close":15321.29},{"date":"2021-09-06","close":15280.5},{"date":"2021-09-13","close":15444.26},{"date":"2021-09-20","close":15887.6},{"date":"2021-09-27","close":15714.07},{"date":"2021-10-04","close":15574.87},{"date":"2021-10-11","close":16459.81},{"date":"2021-10-18","close":16727.67},{"date":"2021-10-25","close":16300.16},{"date":"2021-11-01","close":17410.47},{"date":"2021-11-08","close":17814.88},{"date":"2021-11-15","close":17551.51},{"date":"2021-11-22","close":17013.55},{"date":"2021-11-29","close":17179.66},{"date":"2021-12-06","close":16811.91},{"date":"2021-12-13","close":16349.53},{"date":"2021-12-20","close":15795.05},{"date":"2021-12-27","close":15597.34},{"date":"2022-01-03","close":16107.1},{"date":"2022-01-10","close":15937.57},{"date":"2022-01-17","close":15331.69},{"date":"2022-01-24","close":15641.29},{"date":"2022-01-31","close":15692.43},{"date":"2022-02-07","close":16087.23},{"date":"2022-02-14","close":16656.47},{"date":"2022-02-21","close":16602.62},{"date":"2022-02-28","close":16559.93},{"date":"2022-03-07","close":16562.92},{"date":"2022-03-14","close":16073.0},{"date":"2022-03-21","close":16397.64},{"date":"2022-03-28","close":17058.32},{"date":"2022-04-04","close":17165.49},{"date":"2022-04-11","close":17077.43},{"date":"2022-04-18","close":16979.25},{"date":"2022-04-25","close":16639.57},{"date":"2022-05-02","close":16296.16},{"date":"2022-05-09","close":16139.25},{"date":"2022-05-16","close":15789.51},{"date":"2022-05-23","close":15621.66},{"date":"2022-05-30","close":14974.59},{"date":"2022-06-06","close":15142.52},{"date":"2022-06-13","close":15185.05},{"date":"2022-06-20","close":14728.56},{"date":"2022-06-27","close":13838.97},{"date":"2022-07-04","close":13855.96},{"date":"2022-07-11","close":14307.38},{"date":"2022-07-18","close":14039.47},{"date":"2022-07-25","close":13872.64},{"date":"2022-08-01","close":13675.38},{"date":"2022-08-08","close":13945.52},{"date":"2022-08-15","close":13615.34},{"date":"2022-08-22","close":14012.95},{"date":"2022-08-29","close":13915.71},{"date":"2022-09-05","close":14297.83},{"date":"2022-09-12","close":14331.42},{"date":"2022-09-19","close":14260.31},{"date":"2022-09-26","close":13707.24},{"date":"2022-10-03","close":13467.87},{"date":"2022-10-10","close":13389.93},{"date":"2022-10-17","close":13656.86},{"date":"2022-10-24","close":13769.8},{"date":"2022-10-31","close":13525.76},{"date":"2022-11-07","close":13700.09},{"date":"2022-11-14","close":14100.75},{"date":"2022-11-21","close":14063.04},{"date":"2022-11-28","close":13912.89},{"date":"2022-12-05","close":13781.64},{"date":"2022-12-12","close":14115.35},{"date":"2022-12-19","close":14348.36},{"date":"2022-12-26","close":14003.32},{"date":"2023-01-02","close":14170.11},{"date":"2023-01-09","close":14002.9},{"date":"2023-01-16","close":13733.97},{"date":"2023-01-23","close":14234.88},{"date":"2023-01-30","close":14583.81},{"date":"2023-02-06","close":14314.17},{"date":"2023-02-13","close":14366.41},{"date":"2023-02-20","close":14588.64},{"date":"2023-02-27","close":14352.87},{"date":"2023-03-06","close":14325.65},{"date":"2023-03-13","close":14614.13},{"date":"2023-03-20","close":13926.67},{"date":"2023-03-27","close":14074.04},{"date":"2023-04-03","close":14386.13},{"date":"2023-04-10","close":14609.82},{"date":"2023-04-17","close":14096.52},{"date":"2023-04-24","close":14242.16},{"date":"2023-05-01","close":13925.2},{"date":"2023-05-08","close":14167.52},{"date":"2023-05-15","close":14427.89},{"date":"2023-05-22","close":14535.71},{"date":"2023-05-29","close":14250.94},{"date":"2023-06-05","close":14039.45},{"date":"2023-06-12","close":14396.72},{"date":"2023-06-19","close":14062.55},{"date":"2023-06-26","close":14277.83},{"date":"2023-07-03","close":14502.71},{"date":"2023-07-10","close":14411.85},{"date":"2023-07-17","close":15423.71},{"date":"2023-07-24","close":15476.06},{"date":"2023-07-31","close":16449.06},{"date":"2023-08-07","close":15576.93},{"date":"2023-08-14","close":14658.28},{"date":"2023-08-21","close":15084.64},{"date":"2023-08-28","close":15375.34},{"date":"2023-09-04","close":15264.75},{"date":"2023-09-11","close":15264.09},{"date":"2023-09-18","close":14500.25},{"date":"2023-09-25","close":14270.04},{"date":"2023-10-02","close":13886.81},{"date":"2023-10-09","close":13821.56},{"date":"2023-10-16","close":14185.87},{"date":"2023-10-23","close":14227.24},{"date":"2023-10-30","close":14395.43},{"date":"2023-11-06","close":14139.75},{"date":"2023-11-13","close":13990.36},{"date":"2023-11-20","close":14054.2},{"date":"2023-11-27","close":13964.74},{"date":"2023-12-04","close":14484.14},{"date":"2023-12-11","close":14156.98},{"date":"2023-12-18","close":14940.82},{"date":"2023-12-25","close":14560.89},{"date":"2024-01-01","close":15016.8},{"date":"2024-01-08","close":14719.43},{"date":"2024-01-15","close":15430.17},{"date":"2024-01-22","close":15511.03},{"date":"2024-01-29","close":15705.96},{"date":"2024-02-05","close":16056.81},{"date":"2024-02-12","close":15799.3},{"date":"2024-02-19","close":15371.43},{"date":"2024-02-26","close":14552.29},{"date":"2024-03-04","close":15074.86},{"date":"2024-03-11","close":14807.93},{"date":"2024-03-18","close":14587.79},{"date":"2024-03-25","close":14596.32},{"date":"2024-04-01","close":15446.31},{"date":"2024-04-08","close":14744.58},{"date":"2024-04-15","close":14868.33},{"date":"2024-04-22","close":14727.19},{"date":"2024-04-29","close":14967.62},{"date":"2024-05-06","close":14259.7},{"date":"2024-05-13","close":14123.59},{"date":"2024-05-20","close":14477.59},{"date":"2024-05-27","close":15139.17},{"date":"2024-06-03","close":15847.52},{"date":"2024-06-10","close":15498.53},{"date":"2024-06-17","close":15544.46},{"date":"2024-06-24","close":15519.53},{"date":"2024-07-01","close":14959.79},{"date":"2024-07-08","close":14394.72},{"date":"2024-07-15","close":14721.32},{"date":"2024-07-22","close":14843.33},{"date":"2024-07-29","close":14806.07},{"date":"2024-08-05","close":15335.94},{"date":"2024-08-12","close":14937.24},{"date":"2024-08-19","close":15178.84},{"date":"2024-08-26","close":15204.96},{"date":"2024-09-02","close":15201.86},{"date":"2024-09-09","close":15432.23},{"date":"2024-09-16","close":15530.1},{"date":"2024-09-23","close":15668.04},{"date":"2024-09-30","close":15806.66},{"date":"2024-10-07","close":16703.83},{"date":"2024-10-14","close":16584.34},{"date":"2024-10-21","close":17081.16},{"date":"2024-10-28","close":17396.89},{"date":"2024-11-04","close":17253.86},{"date":"2024-11-11","close":17664.33},{"date":"2024-11-18","close":17274.04},{"date":"2024-11-25","close":17864.8},{"date":"2024-12-02","close":17493.41},{"date":"2024-12-09","close":17282.26},{"date":"2024-12-16","close":17462.97},{"date":"2024-12-23","close":17896.63},{"date":"2024-12-30","close":18379.29},{"date":"2025-01-06","close":18867.58}]}};

// ── RUNTIME STATE ──────────────────────────────────────────────
let MARKET_DATA = JSON.parse(JSON.stringify(FALLBACK_DATA)); // deep copy fallback
let activePeriod = '1Y';
let chartInstances = {};
let positions = []; // DCA portfolio rows
let posCounter = 0;

// ── COLORS ─────────────────────────────────────────────────────
const C = {
  sp500:  { line:'#1b4f8c', fill:'rgba(27,79,140,0.07)',  dash:[] },
  stoxx:  { line:'#0f7057', fill:'rgba(15,112,87,0.07)',  dash:[5,3] },
  nikkei: { line:'#8c3a1b', fill:'rgba(140,58,27,0.07)',  dash:[2,2] },
};
const PALETTE = ['#1b4f8c','#0f7057','#8c3a1b','#7c3aed','#b45309','#0e7490','#166534','#9f1239','#374151'];
const PERIOD_WEEKS = {'1M':4,'3M':13,'6M':26,'1Y':52,'3Y':156,'5Y':260,'MAX':9999};

// ╔══════════════════════════════════════════════════════════════╗
// ║  INDEXEDDB CACHE LAYER                                       ║
// ╚══════════════════════════════════════════════════════════════╝
const DB_NAME = 'EquityDashboard';
const DB_VER  = 2;
const STORE   = 'tickers';
const CACHE_TTL_MS = 24 * 60 * 60 * 1000; // 24 hours

function openDB() {
  return new Promise((res, rej) => {
    const req = indexedDB.open(DB_NAME, DB_VER);
    req.onupgradeneeded = e => {
      const db = e.target.result;
      if (!db.objectStoreNames.contains(STORE)) db.createObjectStore(STORE, { keyPath: 'ticker' });
    };
    req.onsuccess = e => res(e.target.result);
    req.onerror   = e => rej(e.target.error);
  });
}
async function dbGet(ticker) {
  const db = await openDB();
  return new Promise((res, rej) => {
    const tx = db.transaction(STORE, 'readonly');
    const req = tx.objectStore(STORE).get(ticker.toUpperCase());
    req.onsuccess = e => res(e.target.result || null);
    req.onerror   = e => rej(e.target.error);
  });
}
async function dbSet(ticker, data) {
  const db = await openDB();
  return new Promise((res, rej) => {
    const tx = db.transaction(STORE, 'readwrite');
    tx.objectStore(STORE).put({ ticker: ticker.toUpperCase(), data, fetchedAt: Date.now() });
    tx.oncomplete = () => res();
    tx.onerror    = e => rej(e.target.error);
  });
}
async function dbGetAll() {
  const db = await openDB();
  return new Promise((res, rej) => {
    const tx = db.transaction(STORE, 'readonly');
    const req = tx.objectStore(STORE).getAll();
    req.onsuccess = e => res(e.target.result);
    req.onerror   = e => rej(e.target.error);
  });
}
async function dbDelete(ticker) {
  const db = await openDB();
  return new Promise((res, rej) => {
    const tx = db.transaction(STORE, 'readwrite');
    tx.objectStore(STORE).delete(ticker.toUpperCase());
    tx.oncomplete = () => res();
    tx.onerror    = e => rej(e.target.error);
  });
}
async function dbClear() {
  const db = await openDB();
  return new Promise((res, rej) => {
    const tx = db.transaction(STORE, 'readwrite');
    tx.objectStore(STORE).clear();
    tx.oncomplete = () => res();
    tx.onerror    = e => rej(e.target.error);
  });
}
function isFresh(fetchedAt) {
  return Date.now() - fetchedAt < CACHE_TTL_MS;
}

// ╔══════════════════════════════════════════════════════════════╗
// ║  ALPHA VANTAGE FETCH LAYER                                   ║
// ╚══════════════════════════════════════════════════════════════╝
function getApiKey() { return document.getElementById('apiKeyInput')?.value?.trim() || ''; }

// Parse Alpha Vantage TIME_SERIES_WEEKLY_ADJUSTED response → [{date, close}]
function parseAVWeekly(json) {
  const ts = json['Weekly Adjusted Time Series'];
  if (!ts) return null;
  return Object.entries(ts)
    .map(([date, vals]) => ({ date, close: parseFloat(vals['5. adjusted close']) }))
    .sort((a, b) => a.date > b.date ? 1 : -1);
}

async function fetchTicker(symbol, statusEl) {
  const key = getApiKey();
  if (!key) throw new Error('No API key configured');
  const url = `https://www.alphavantage.co/query?function=TIME_SERIES_WEEKLY_ADJUSTED&symbol=${encodeURIComponent(symbol)}&outputsize=full&apikey=${key}`;
  if (statusEl) statusEl.innerHTML = `<span class="loader"></span>Fetching ${symbol}…`;
  const resp = await fetch(url);
  if (!resp.ok) throw new Error(`HTTP ${resp.status}`);
  const json = await resp.json();
  if (json['Note'] || json['Information']) throw new Error(`API limit: ${json['Note'] || json['Information']}`);
  const series = parseAVWeekly(json);
  if (!series || !series.length) throw new Error(`No data for ${symbol}`);
  return series;
}

async function fetchAndCacheTicker(symbol, label, currency, key3, statusEl) {
  // Check cache first (unless force refresh)
  const cached = await dbGet(symbol);
  if (cached && isFresh(cached.fetchedAt)) {
    if (statusEl) statusEl.innerHTML += `<br>✓ ${symbol} — using cached data (fetched ${timeAgo(cached.fetchedAt)})`;
    return cached.data;
  }
  const series = await fetchTicker(symbol, statusEl);
  const entry = { label, currency, ticker: symbol.toUpperCase(), series };
  await dbSet(symbol, entry);
  if (statusEl) statusEl.innerHTML += `<br>✓ ${symbol} — ${series.length} weeks fetched`;
  return entry;
}

// ── Fetch all 3 benchmarks + apply to MARKET_DATA ──────────────
async function fetchAndStore() {
  const statusEl = document.getElementById('fetchStatus');
  const sym = ['sp500','stoxx','nikkei'].map((_,i) => document.getElementById(`sym${i}`).value.trim().toUpperCase());
  const labels = ['S&P 500','STOXX 600','Nikkei 225'];
  const currencies = ['USD','EUR','JPY'];
  const keys3 = ['sp500','stoxx','nikkei'];
  statusEl.innerHTML = '<span class="loader"></span>Starting data refresh…';
  try {
    for (let i = 0; i < 3; i++) {
      const entry = await fetchAndCacheTicker(sym[i], labels[i], currencies[i], keys3[i], statusEl);
      MARKET_DATA[keys3[i]] = { ...entry, label: labels[i], currency: currencies[i] };
    }
    document.getElementById('dataStatusBadge').className = 'badge badge-live';
    document.getElementById('dataStatusBadge').textContent = '✓ Live data — Alpha Vantage';
    const lu = document.getElementById('lastUpdated');
    lu.textContent = `Last refreshed: ${new Date().toLocaleString()}`;
    lu.style.display = '';
    statusEl.innerHTML += '<br><strong style="color:var(--pos)">✓ All benchmarks updated. Reloading charts…</strong>';
    renderCurrentTab();
    renderCacheStatus();
  } catch(e) {
    statusEl.innerHTML = `<span style="color:var(--neg)">✗ Error: ${e.message}</span>`;
  }
}

async function clearCache() {
  await dbClear();
  MARKET_DATA = JSON.parse(JSON.stringify(FALLBACK_DATA));
  document.getElementById('dataStatusBadge').className = 'badge badge-warn';
  document.getElementById('dataStatusBadge').textContent = '⚠ Sample data — configure API key';
  document.getElementById('fetchStatus').innerHTML = 'Cache cleared. Using fallback sample data.';
  renderCacheStatus();
  renderCurrentTab();
}

async function renderCacheStatus() {
  const all = await dbGetAll();
  const el = document.getElementById('cacheStatus');
  if (!all.length) { el.innerHTML = '<em style="font-size:12px;color:var(--faint);font-family:\'Helvetica Neue\',Arial,sans-serif">No data cached yet.</em>'; return; }
  let html = '<div style="overflow-x:auto"><table class="cache-table"><thead><tr><th>Ticker</th><th>Label</th><th>Points</th><th>Date Range</th><th>Fetched</th><th>Status</th><th></th></tr></thead><tbody>';
  for (const row of all) {
    const s = row.data.series;
    const fresh = isFresh(row.fetchedAt);
    html += `<tr>
      <td style="font-family:monospace;font-weight:700">${row.ticker}</td>
      <td>${row.data.label||'—'}</td>
      <td>${s.length}</td>
      <td style="font-family:monospace">${s[0]?.date||'?'} → ${s[s.length-1]?.date||'?'}</td>
      <td>${timeAgo(row.fetchedAt)}</td>
      <td><span class="badge ${fresh?'badge-live':'badge-warn'}">${fresh?'Fresh':'Stale'}</span></td>
      <td><button class="btn btn-danger btn-sm" onclick="deleteTickerCache('${row.ticker}')">Delete</button></td>
    </tr>`;
  }
  html += '</tbody></table></div>';
  el.innerHTML = html;
}

async function deleteTickerCache(ticker) {
  await dbDelete(ticker);
  renderCacheStatus();
}

function timeAgo(ts) {
  const mins = Math.floor((Date.now()-ts)/60000);
  if (mins < 2) return 'just now';
  if (mins < 60) return `${mins}m ago`;
  const hrs = Math.floor(mins/60);
  if (hrs < 24) return `${hrs}h ago`;
  return `${Math.floor(hrs/24)}d ago`;
}

// On load: try to pull data from IndexedDB cache
async function loadFromCache() {
  const syms = ['sp500','stoxx','nikkei'];
  const labels = ['S&P 500','STOXX 600','Nikkei 225'];
  const currencies = ['USD','EUR','JPY'];
  const tickers = ['SPY','VGK','EWJ'];
  let anyLoaded = false;
  for (let i = 0; i < 3; i++) {
    const cached = await dbGet(tickers[i]);
    if (cached) {
      MARKET_DATA[syms[i]] = { ...cached.data, label: labels[i], currency: currencies[i] };
      anyLoaded = true;
    }
  }
  if (anyLoaded) {
    document.getElementById('dataStatusBadge').className = 'badge badge-live';
    document.getElementById('dataStatusBadge').textContent = '✓ Live data — cached';
    renderCurrentTab();
    // Auto-refresh stale data if API key is set and any are stale
    const key = localStorage.getItem('avApiKey') || '';
    if (key) {
      document.getElementById('apiKeyInput').value = key;
      const allFresh = await Promise.all(tickers.map(t => dbGet(t).then(r => r && isFresh(r.fetchedAt))));
      if (allFresh.includes(false)) fetchAndStore();
    }
  }
}

// ╔══════════════════════════════════════════════════════════════╗
// ║  ANALYTICS UTILITIES                                         ║
// ╚══════════════════════════════════════════════════════════════╝
function getSeries(key) { return MARKET_DATA[key].series; }
function sliceLast(s, w) { return w >= s.length ? s : s.slice(-w); }
function calcReturns(s) {
  const r = [];
  for (let i=1;i<s.length;i++) r.push(Math.log(s[i].close/s[i-1].close));
  return r;
}
function drawdowns(s) {
  let peak = s[0].close;
  return s.map(r => { if (r.close>peak) peak=r.close; return (r.close-peak)/peak*100; });
}
function maxDD(s) { return Math.min(...drawdowns(s)); }
function rollingVol(rets, w=52) {
  return rets.map((_,i) => {
    if (i<w-1) return null;
    const sl = rets.slice(i-w+1,i+1);
    const m = sl.reduce((a,b)=>a+b,0)/w;
    return Math.sqrt(sl.reduce((a,b)=>a+(b-m)**2,0)/(w-1)*52)*100;
  });
}
function rollingRet(s, w=52) {
  return s.map((_,i) => i<w ? null : (s[i].close/s[i-w].close-1)*100);
}
function movingAvg(s, w) {
  return s.map((_,i) => {
    if(i<w-1) return null;
    return s.slice(i-w+1,i+1).reduce((a,r)=>a+r.close,0)/w;
  });
}
function getReturn(s, wks) {
  const sl = sliceLast(s, wks);
  return sl.length<2 ? null : (sl[sl.length-1].close/sl[0].close-1)*100;
}
function getCAGR(totalInvested, finalValue, years) {
  if (!totalInvested || years<0.01) return 0;
  return (Math.pow(finalValue/totalInvested, 1/years)-1)*100;
}
function getYTD(s) {
  const yr = s[s.length-1].date.slice(0,4);
  const jan = s.find(r=>r.date.startsWith(yr));
  return jan ? (s[s.length-1].close/jan.close-1)*100 : 0;
}
function corrMatrix() {
  const keys = ['sp500','stoxx','nikkei'];
  const allRets = keys.map(k => calcReturns(getSeries(k)));
  const minLen = Math.min(...allRets.map(r=>r.length));
  const tr = allRets.map(r=>r.slice(-minLen));
  const corr = {};
  keys.forEach((k,i) => {
    corr[k]={};
    keys.forEach((k2,j) => {
      const a=tr[i],b=tr[j];
      const ma=a.reduce((s,v)=>s+v,0)/a.length, mb=b.reduce((s,v)=>s+v,0)/b.length;
      const num=a.reduce((s,v,idx)=>s+(v-ma)*(b[idx]-mb),0);
      const da=Math.sqrt(a.reduce((s,v)=>s+(v-ma)**2,0));
      const db=Math.sqrt(b.reduce((s,v)=>s+(v-mb)**2,0));
      corr[k][k2]=da*db===0?0:num/(da*db);
    });
  });
  return corr;
}
const sign = v => v>=0?'+':'';
const cls  = v => v>=0?'up':'dn';
const num  = (v,dp=2) => v==null?'—':(+v).toLocaleString('en-US',{minimumFractionDigits:dp,maximumFractionDigits:dp});
function money(v, currency='USD') {
  const sym={USD:'$',EUR:'€',JPY:'¥'}[currency]||'';
  return sym+num(v, currency==='JPY'?0:2);
}

// ╔══════════════════════════════════════════════════════════════╗
// ║  CHART HELPERS                                               ║
// ╚══════════════════════════════════════════════════════════════╝
function destroyChart(id) { if(chartInstances[id]){chartInstances[id].destroy();delete chartInstances[id];} }
function mkChart(id, cfg) { destroyChart(id); chartInstances[id]=new Chart(document.getElementById(id),cfg); return chartInstances[id]; }
function lineOpts(legendOn=false) {
  return {
    responsive:true, maintainAspectRatio:false,
    interaction:{mode:'index',intersect:false},
    plugins:{
      legend:{display:legendOn,labels:{boxWidth:10,font:{size:11},padding:14}},
      tooltip:{mode:'index',callbacks:{}}
    },
    scales:{
      x:{grid:{color:'rgba(0,0,0,0.04)'},ticks:{font:{size:10},maxTicksLimit:10,color:'#9e9d97'}},
      y:{grid:{color:'rgba(0,0,0,0.04)'},ticks:{font:{size:10},color:'#9e9d97'}}
    },
    elements:{point:{radius:0,hoverRadius:4}}
  };
}

// ╔══════════════════════════════════════════════════════════════╗
// ║  TAB NAVIGATION                                              ║
// ╚══════════════════════════════════════════════════════════════╝
let currentTab = 'overview';
function switchTab(name, btn) {
  document.querySelectorAll('.tab-panel').forEach(p=>p.classList.remove('active'));
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  document.getElementById('tab-'+name).classList.add('active');
  if (btn) btn.classList.add('active');
  else document.querySelectorAll('nav button')[['overview','performance','compare','dca','settings'].indexOf(name)]?.classList.add('active');
  currentTab = name;
  if (name==='overview')    renderOverview();
  if (name==='performance') renderPerformance();
  if (name==='compare')     renderCompare();
  if (name==='settings')    renderCacheStatus();
}
function renderCurrentTab() { switchTab(currentTab); }

// ╔══════════════════════════════════════════════════════════════╗
// ║  OVERVIEW                                                    ║
// ╚══════════════════════════════════════════════════════════════╝
function renderOverview() {
  renderCards(); renderBase100(); renderReturnBar(); renderMDD();
}
function renderCards() {
  const keys=['sp500','stoxx','nikkei'];
  let h='';
  keys.forEach(k=>{
    const s=getSeries(k), d=MARKET_DATA[k], latest=s[s.length-1].close;
    const dayR=s.length>1?(latest/s[s.length-2].close-1)*100:0;
    const rows=[['1W',getReturn(s,1)],['1M',getReturn(s,4)],['YTD',getYTD(s)],['1Y',getReturn(s,52)],['3Y',getReturn(s,156)],['5Y',getReturn(s,260)]];
    h+=`<div class="metric-card">
      <div class="mc-label">${d.label} <span class="tag tag-${k==='sp500'?'sp':k==='stoxx'?'st':'nk'}">${d.ticker||''}</span></div>
      <div class="mc-value">${money(latest,d.currency)}</div>
      <div class="mc-row"><span>Today</span><span class="${cls(dayR)}">${sign(dayR)}${dayR.toFixed(2)}%</span></div>
      ${rows.map(([l,v])=>`<div class="mc-row"><span>${l}</span><span class="${cls(v)}">${v!=null?sign(v)+v.toFixed(2)+'%':'—'}</span></div>`).join('')}
    </div>`;
  });
  document.getElementById('overviewCards').innerHTML=h;
}
function renderBase100() {
  const keys=['sp500','stoxx','nikkei'];
  const labels=getSeries('sp500').map(r=>r.date.slice(0,7));
  const datasets=keys.map(k=>{
    const s=getSeries(k), base=s[0].close;
    return {label:MARKET_DATA[k].label,data:s.map(r=>+((r.close/base*100).toFixed(2))),borderColor:C[k].line,borderDash:C[k].dash,fill:false,borderWidth:1.8,tension:.2};
  });
  const o=lineOpts(true);
  o.plugins.tooltip.callbacks.label=ctx=>`${ctx.dataset.label}: ${ctx.parsed.y.toFixed(1)}`;
  mkChart('chartBase100',{type:'line',data:{labels,datasets},options:o});
}
function renderReturnBar() {
  const periods=['1M','3M','6M','1Y','3Y','5Y'],wks={'1M':4,'3M':13,'6M':26,'1Y':52,'3Y':156,'5Y':260};
  const keys=['sp500','stoxx','nikkei'];
  const datasets=keys.map(k=>({
    label:MARKET_DATA[k].label,
    data:periods.map(p=>{const v=getReturn(getSeries(k),wks[p]);return v!=null?+v.toFixed(2):null;}),
    backgroundColor:C[k].line, borderRadius:3
  }));
  const o={responsive:true,maintainAspectRatio:false,interaction:{mode:'index',intersect:false},
    plugins:{legend:{display:true,labels:{boxWidth:10,font:{size:11}}},tooltip:{callbacks:{label:ctx=>`${ctx.dataset.label}: ${sign(ctx.parsed.y)}${ctx.parsed.y.toFixed(2)}%`}}},
    scales:{x:{grid:{display:false},ticks:{font:{size:10},color:'#9e9d97'}},y:{grid:{color:'rgba(0,0,0,0.04)'},ticks:{font:{size:10},color:'#9e9d97',callback:v=>v+'%'}}}};
  mkChart('chartReturnBar',{type:'bar',data:{labels:periods,datasets},options:o});
}
function renderMDD() {
  const keys=['sp500','stoxx','nikkei'];
  let h='<div style="padding:6px 0">';
  keys.forEach(k=>{
    const mdd=maxDD(getSeries(k));
    h+=`<div style="margin-bottom:14px">
      <div style="display:flex;justify-content:space-between;margin-bottom:4px">
        <span style="font-size:12px;font-family:'Helvetica Neue',Arial,sans-serif"><span class="tag tag-${k==='sp500'?'sp':k==='stoxx'?'st':'nk'}">${MARKET_DATA[k].label}</span></span>
        <span style="font-size:13px;font-weight:700;color:var(--neg);font-family:'Helvetica Neue',Arial,sans-serif">${mdd.toFixed(2)}%</span>
      </div>
      <div style="background:var(--neg-bg);border-radius:3px;height:7px;overflow:hidden">
        <div style="background:var(--neg);width:${Math.min(100,Math.abs(mdd)/60*100)}%;height:100%;border-radius:3px"></div>
      </div>
    </div>`;
  });
  h+='</div>';
  document.getElementById('mddStats').innerHTML=h;
}

// ╔══════════════════════════════════════════════════════════════╗
// ║  PERFORMANCE                                                 ║
// ╚══════════════════════════════════════════════════════════════╝
function renderPerformance() {
  renderCumRet(); renderDD(); renderRolling(); renderMA();
}
function renderCumRet() {
  const keys=['sp500','stoxx','nikkei'],labels=getSeries('sp500').map(r=>r.date.slice(0,7));
  const datasets=keys.map(k=>{const s=getSeries(k),b=s[0].close;return{label:MARKET_DATA[k].label,data:s.map(r=>+((r.close/b-1)*100).toFixed(2)),borderColor:C[k].line,borderDash:C[k].dash,fill:false,borderWidth:1.8,tension:.2};});
  const o=lineOpts(true);o.scales.y.ticks.callback=v=>v+'%';
  o.plugins.tooltip.callbacks.label=ctx=>`${ctx.dataset.label}: ${sign(ctx.parsed.y)}${ctx.parsed.y.toFixed(2)}%`;
  mkChart('chartCumRet',{type:'line',data:{labels,datasets},options:o});
}
function renderDD() {
  const keys=['sp500','stoxx','nikkei'],labels=getSeries('sp500').map(r=>r.date.slice(0,7));
  const datasets=keys.map(k=>({label:MARKET_DATA[k].label,data:drawdowns(getSeries(k)).map(v=>+v.toFixed(2)),borderColor:C[k].line,borderDash:C[k].dash,fill:true,backgroundColor:C[k].fill,borderWidth:1.5,tension:.2}));
  const o=lineOpts(true);o.scales.y.ticks.callback=v=>v.toFixed(0)+'%';
  o.plugins.tooltip.callbacks.label=ctx=>`${ctx.dataset.label}: ${ctx.parsed.y.toFixed(2)}%`;
  mkChart('chartDD',{type:'line',data:{labels,datasets},options:o});
}
function renderRolling() {
  const s=getSeries('sp500'),labels=s.map(r=>r.date.slice(0,7));
  const rr=rollingRet(s,52).map(v=>v!=null?+v.toFixed(2):null);
  const o1=lineOpts(false);o1.scales.y.ticks.callback=v=>v.toFixed(0)+'%';
  mkChart('chartRoll',{type:'line',data:{labels,datasets:[{label:'52W Return',data:rr,borderColor:'#1b4f8c',fill:false,borderWidth:1.5,tension:.2}]},options:o1});
  const keys=['sp500','stoxx','nikkei'],labels2=getSeries('sp500').map(r=>r.date.slice(0,7));
  const dsv=keys.map(k=>{const ret=calcReturns(getSeries(k));return{label:MARKET_DATA[k].label,data:rollingVol(ret,52).map(v=>v!=null?+v.toFixed(2):null),borderColor:C[k].line,borderDash:C[k].dash,fill:false,borderWidth:1.5,tension:.2};});
  const o2=lineOpts(true);o2.scales.y.ticks.callback=v=>v.toFixed(0)+'%';
  o2.plugins.tooltip.callbacks.label=ctx=>`${ctx.dataset.label}: ${ctx.parsed.y!=null?ctx.parsed.y.toFixed(2)+'%':'—'}`;
  mkChart('chartVol',{type:'line',data:{labels:labels2,datasets:dsv},options:o2});
}
function renderMA() {
  const s=getSeries('sp500'),labels=s.map(r=>r.date.slice(0,7));
  const ma50=movingAvg(s,50).map(v=>v!=null?+v.toFixed(2):null);
  const ma200=movingAvg(s,200).map(v=>v!=null?+v.toFixed(2):null);
  const o=lineOpts(true);
  o.plugins.tooltip.callbacks.label=ctx=>`${ctx.dataset.label}: ${ctx.parsed.y!=null?num(ctx.parsed.y):'—'}`;
  mkChart('chartMA',{type:'line',data:{labels,datasets:[
    {label:'S&P 500',data:s.map(r=>r.close),borderColor:'#1b4f8c',fill:false,borderWidth:1.5,tension:.2},
    {label:'50W MA',data:ma50,borderColor:'#d97706',borderDash:[4,3],fill:false,borderWidth:1.5,tension:.2},
    {label:'200W MA',data:ma200,borderColor:'#dc2626',borderDash:[8,4],fill:false,borderWidth:1.5,tension:.2},
  ]},options:o});
}

// ╔══════════════════════════════════════════════════════════════╗
// ║  COMPARISON                                                  ║
// ╚══════════════════════════════════════════════════════════════╝
function setPeriod(p, btn) {
  activePeriod=p;
  document.querySelectorAll('#periodBar button').forEach(b=>b.classList.remove('active'));
  if (btn) btn.classList.add('active');
  renderCompare();
}
function renderCompare() { renderCompareTable(); renderPeriodChart(); renderCorrMatrix(); }
function renderCompareTable() {
  const keys=['sp500','stoxx','nikkei'],pList=['1M','3M','6M','1Y','3Y','5Y','MAX'];
  let h='<h3 style="font-size:13px;font-weight:600;margin-bottom:10px;font-family:\'Helvetica Neue\',Arial,sans-serif">Return Comparison Table</h3>';
  h+='<div style="overflow-x:auto"><table class="dt"><thead><tr><th>Index</th><th>CCY</th>';
  pList.forEach(p=>h+=`<th>${p}</th>`);
  h+='<th>Vol 1Y</th><th>Max DD</th></tr></thead><tbody>';
  const retsSel=[];
  keys.forEach(k=>{
    const s=getSeries(k),d=MARKET_DATA[k];
    const rets1y=calcReturns(sliceLast(s,52));
    const mean=rets1y.reduce((a,r)=>a+r,0)/(rets1y.length||1);
    const vol=(Math.sqrt(rets1y.reduce((a,r)=>a+(r-mean)**2,0)/(rets1y.length||1))*Math.sqrt(52)*100).toFixed(1);
    const mdd=maxDD(s).toFixed(2);
    const selV=getReturn(s,PERIOD_WEEKS[activePeriod]);
    retsSel.push({k,v:selV});
    h+=`<tr><td><span class="tag tag-${k==='sp500'?'sp':k==='stoxx'?'st':'nk'}">${d.label}</span></td><td style="color:var(--muted);font-size:11px">${d.currency}</td>`;
    pList.forEach(p=>{
      const v=getReturn(s,PERIOD_WEEKS[p]);
      const sel=p===activePeriod?'font-weight:700;background:var(--surface2)':'';
      h+=`<td class="${cls(v)}" style="${sel}">${v!=null?sign(v)+v.toFixed(2)+'%':'—'}</td>`;
    });
    h+=`<td>${vol}%</td><td class="dn">${mdd}%</td></tr>`;
  });
  h+='</tbody></table></div>';
  retsSel.sort((a,b)=>b.v-a.v);
  if(retsSel.length){
    const b=retsSel[0],w=retsSel[retsSel.length-1];
    h+=`<div style="display:flex;gap:10px;margin-top:12px;flex-wrap:wrap">
      <div style="padding:7px 12px;background:var(--pos-bg);border-radius:5px;font-size:11px;font-family:'Helvetica Neue',Arial,sans-serif">🏆 <b>Best (${activePeriod}):</b> ${MARKET_DATA[b.k].label} <span class="up">${sign(b.v)}${b.v?.toFixed(2)}%</span></div>
      <div style="padding:7px 12px;background:var(--neg-bg);border-radius:5px;font-size:11px;font-family:'Helvetica Neue',Arial,sans-serif">⚠ <b>Worst (${activePeriod}):</b> ${MARKET_DATA[w.k].label} <span class="dn">${sign(w.v)}${w.v?.toFixed(2)}%</span></div>
    </div>`;
  }
  document.getElementById('compareTable').innerHTML=h;
}
function renderPeriodChart() {
  const keys=['sp500','stoxx','nikkei'],wks=PERIOD_WEEKS[activePeriod];
  const datasets=keys.map(k=>{const s=sliceLast(getSeries(k),wks),b=s[0].close;return{label:MARKET_DATA[k].label,data:s.map(r=>+((r.close/b-1)*100).toFixed(2)),borderColor:C[k].line,borderDash:C[k].dash,fill:false,borderWidth:1.8,tension:.2};});
  const labels=sliceLast(getSeries('sp500'),wks).map(r=>r.date.slice(0,7));
  const o=lineOpts(true);o.scales.y.ticks.callback=v=>v+'%';
  o.plugins.tooltip.callbacks.label=ctx=>`${ctx.dataset.label}: ${sign(ctx.parsed.y)}${ctx.parsed.y.toFixed(2)}%`;
  mkChart('chartPeriod',{type:'line',data:{labels,datasets},options:o});
}
function renderCorrMatrix() {
  const corr=corrMatrix(),keys=['sp500','stoxx','nikkei'];
  const labels=keys.map(k=>MARKET_DATA[k].label);
  function corrColor(v){const a=Math.abs(v)*180;return v>=0?`rgb(${255-a|0},${255-(a*.3)|0},${255-a|0})`:`rgb(255,${255-a|0},${255-a|0})`;}
  let h='<div style="overflow-x:auto"><table class="dt"><thead><tr><th></th>';
  labels.forEach(l=>h+=`<th style="text-align:center">${l}</th>`);
  h+='</tr></thead><tbody>';
  keys.forEach((k,i)=>{
    h+=`<tr><td style="font-weight:600;font-size:11px">${labels[i]}</td>`;
    keys.forEach(k2=>{const v=corr[k][k2];h+=`<td style="padding:9px 13px;text-align:center;font-size:12px;font-weight:600;font-family:'Helvetica Neue',Arial,sans-serif;background:${corrColor(v)}">${v.toFixed(3)}</td>`;});
    h+='</tr>';
  });
  h+='</tbody></table></div><div style="margin-top:8px;font-size:10px;color:var(--faint);font-family:\'Helvetica Neue\',Arial,sans-serif">Weekly log returns, full series, local currency</div>';
  document.getElementById('corrMatrix').innerHTML=h;
}

// ╔══════════════════════════════════════════════════════════════╗
// ║  DCA PORTFOLIO SIMULATOR v2                                  ║
// ╚══════════════════════════════════════════════════════════════╝

// Position defaults by type
const POS_DEFAULTS = {
  index: { ticker:'SPY',  label:'S&P 500 (SPY)', type:'index', envelope:'standard', lump:10000, contrib:500,  freq:'monthly',  currency:'USD' },
  etf:   { ticker:'VGK',  label:'STOXX 600 ETF', type:'etf',   envelope:'standard', lump:0,     contrib:300,  freq:'monthly',  currency:'EUR' },
  stock: { ticker:'AAPL', label:'Apple Inc.',     type:'stock', envelope:'standard', lump:5000,  contrib:200,  freq:'monthly',  currency:'USD' },
};
const ENVELOPE_LABELS = { standard:'Standard', pea:'PEA (France)', cto:'CTO', isa:'ISA (UK)', tfsa:'TFSA (CA)' };
const ENVELOPE_LIMITS = { pea:150000, isa:20000, tfsa:7000, standard:Infinity, cto:Infinity };
const ENVELOPE_CSS = { pea:'env-pea', cto:'env-cto', isa:'env-pea', tfsa:'env-pea', standard:'env-std' };

function addPosition(type='index') {
  const id = ++posCounter;
  const def = POS_DEFAULTS[type] || POS_DEFAULTS.index;
  const pos = { id, ...JSON.parse(JSON.stringify(def)) };
  positions.push(pos);
  renderPositionList();
}
function removePosition(id) {
  positions = positions.filter(p=>p.id!==id);
  renderPositionList();
}
function getPosVal(id, field) {
  const el=document.getElementById(`pos-${id}-${field}`);
  return el ? el.value : null;
}
function readPositions() {
  return positions.map(p => ({
    ...p,
    ticker:   getPosVal(p.id,'ticker') || p.ticker,
    label:    getPosVal(p.id,'label')  || p.label,
    envelope: getPosVal(p.id,'envelope') || p.envelope,
    lump:     parseFloat(getPosVal(p.id,'lump')) || 0,
    contrib:  parseFloat(getPosVal(p.id,'contrib')) || 0,
    freq:     getPosVal(p.id,'freq') || p.freq,
    currency: getPosVal(p.id,'currency') || p.currency,
    annInc:   parseFloat(getPosVal(p.id,'annInc')) || 0,
    fees:     parseFloat(getPosVal(p.id,'fees')) || parseFloat(document.getElementById('gFees').value) || 0,
  }));
}

function renderPositionList() {
  const el=document.getElementById('positionList');
  if (!positions.length) {
    el.innerHTML=`<div style="padding:20px;text-align:center;color:var(--muted);font-family:'Helvetica Neue',Arial,sans-serif;font-size:13px;border:1px dashed var(--border-strong);border-radius:var(--r)">No positions yet. Add a position above to start.</div>`;
    return;
  }
  // Header row
  let h=`<div class="portfolio-row header-row">
    <span>Asset</span><span>Envelope</span><span>Lump Sum</span>
    <span>Contrib.</span><span>Frequency</span><span>Ann. Inc%</span><span>Fees%</span><span></span>
  </div>`;
  positions.forEach(p=>{
    const typeTag=p.type==='etf'?'tag-etf':p.type==='stock'?'tag-stk':'tag-sp';
    h+=`<div class="portfolio-row" id="posrow-${p.id}">
      <div style="display:flex;flex-direction:column;gap:5px">
        <div style="display:flex;gap:6px;align-items:center">
          <span class="tag ${typeTag}">${p.type.toUpperCase()}</span>
          <input id="pos-${p.id}-ticker" style="font-family:monospace;font-size:12px;font-weight:700;width:80px;padding:3px 6px;border:1px solid var(--border-strong);border-radius:3px;background:var(--surface);color:var(--text)" value="${p.ticker}" placeholder="TICKER">
        </div>
        <input id="pos-${p.id}-label" style="font-size:11px;color:var(--muted);font-family:'Helvetica Neue',Arial,sans-serif;width:100%;padding:3px 6px;border:1px solid var(--border);border-radius:3px;background:var(--surface);color:var(--muted)" value="${p.label}" placeholder="Name">
      </div>
      <select id="pos-${p.id}-envelope" style="font-size:11px;font-family:'Helvetica Neue',Arial,sans-serif;padding:4px 6px;border:1px solid var(--border-strong);border-radius:3px;background:var(--surface);color:var(--text)">
        ${Object.entries(ENVELOPE_LABELS).map(([v,l])=>`<option value="${v}" ${p.envelope===v?'selected':''}>${l}</option>`).join('')}
      </select>
      <div style="display:flex;gap:3px;align-items:center">
        <input id="pos-${p.id}-currency" style="width:44px;font-size:11px;font-family:'Helvetica Neue',Arial,sans-serif;padding:3px 4px;border:1px solid var(--border-strong);border-radius:3px;background:var(--surface);color:var(--text);text-align:center" value="${p.currency}" placeholder="USD">
        <input id="pos-${p.id}-lump" type="number" min="0" style="width:80px;font-size:12px;font-family:'Helvetica Neue',Arial,sans-serif;padding:4px 6px;border:1px solid var(--border-strong);border-radius:3px;background:var(--surface);color:var(--text)" value="${p.lump}">
      </div>
      <input id="pos-${p.id}-contrib" type="number" min="0" style="font-size:12px;font-family:'Helvetica Neue',Arial,sans-serif;padding:4px 6px;border:1px solid var(--border-strong);border-radius:3px;background:var(--surface);color:var(--text);width:80px" value="${p.contrib}">
      <select id="pos-${p.id}-freq" style="font-size:11px;font-family:'Helvetica Neue',Arial,sans-serif;padding:4px 6px;border:1px solid var(--border-strong);border-radius:3px;background:var(--surface);color:var(--text)">
        <option value="weekly" ${p.freq==='weekly'?'selected':''}>Weekly</option>
        <option value="monthly" ${p.freq==='monthly'?'selected':''}>Monthly</option>
        <option value="quarterly" ${p.freq==='quarterly'?'selected':''}>Quarterly</option>
      </select>
      <input id="pos-${p.id}-annInc" type="number" min="0" max="20" step="0.5" style="width:56px;font-size:12px;font-family:'Helvetica Neue',Arial,sans-serif;padding:4px 6px;border:1px solid var(--border-strong);border-radius:3px;background:var(--surface);color:var(--text)" value="${p.annInc||0}">
      <input id="pos-${p.id}-fees" type="number" min="0" max="5" step="0.1" style="width:56px;font-size:12px;font-family:'Helvetica Neue',Arial,sans-serif;padding:4px 6px;border:1px solid var(--border-strong);border-radius:3px;background:var(--surface);color:var(--text)" value="${p.fees||0.2}">
      <button class="btn btn-danger btn-sm" onclick="removePosition(${p.id})">✕</button>
    </div>`;
  });
  el.innerHTML=h;
}

// ── DCA CORE ENGINE ────────────────────────────────────────────
function runSingleDCA(pos, startDate, endDate) {
  // Find series: check MARKET_DATA for matching ticker, or fallback
  let series = null;
  const ticker = pos.ticker.toUpperCase();
  // Look in MARKET_DATA by ticker
  for (const k of Object.keys(MARKET_DATA)) {
    if ((MARKET_DATA[k].ticker||'').toUpperCase()===ticker) { series=MARKET_DATA[k].series; break; }
  }
  // Fallback: use sp500 as proxy with a warning
  if (!series) {
    const allKeys=Object.keys(MARKET_DATA);
    if (allKeys.length) series=MARKET_DATA[allKeys[0]].series;
  }
  if (!series) return null;

  const filtered = series.filter(r=>r.date>=startDate&&r.date<=endDate);
  if (filtered.length<2) return null;

  const freqW = {weekly:1,monthly:4,quarterly:13}[pos.freq]||4;
  const annFees = (pos.fees||0)/100;
  const annInc  = (pos.annInc||0)/100;
  const weeklyFee = Math.pow(1-annFees, 1/52)-1;
  const envLimit = ENVELOPE_LIMITS[pos.envelope||'standard']||Infinity;

  let shares=0, totalInvested=0, yearlyContrib=pos.contrib, lastContribYear=null;
  let envelopeTotal=0; // track envelope ceiling
  const timeline=[];

  filtered.forEach((r,i)=>{
    const yr=r.date.slice(0,4);
    shares *= (1+weeklyFee); // fee drag

    // Lump sum on first point
    if (i===0 && pos.lump>0) {
      const amt=Math.min(pos.lump, envLimit-envelopeTotal);
      if (amt>0){ shares+=amt/r.close; totalInvested+=amt; envelopeTotal+=amt; }
    }
    // Recurring
    if (pos.contrib>0 && i%freqW===0 && i>0) {
      if (annInc>0 && lastContribYear && yr!==lastContribYear) yearlyContrib*=(1+annInc);
      lastContribYear=yr;
      const remaining=envLimit-envelopeTotal;
      const amt=Math.min(yearlyContrib, remaining);
      if (amt>0){ shares+=amt/r.close; totalInvested+=amt; envelopeTotal+=amt; }
    }
    timeline.push({date:r.date,price:r.close,shares:+shares.toFixed(4),invested:+totalInvested.toFixed(2),value:+(shares*r.close).toFixed(2)});
  });

  if (!timeline.length) return null;
  const last=timeline[timeline.length-1];
  const years=filtered.length/52;
  const lumpShares=last.invested/filtered[0].close;
  const lumpFinal=lumpShares*filtered[filtered.length-1].close*Math.pow(1+weeklyFee,filtered.length);

  return {
    pos, timeline,
    totalInvested:last.invested,
    finalValue:last.value,
    gain:last.value-last.invested,
    totalRet:(last.value/last.invested-1)*100,
    cagr:getCAGR(last.invested,last.value,years),
    lumpFinal, lumpRet:(lumpFinal/last.invested-1)*100,
    envelopeHit:envelopeTotal>=envLimit*0.99,
  };
}

async function runPortfolio() {
  const gStart=document.getElementById('gStart').value;
  const gEnd=document.getElementById('gEnd').value;
  const current=readPositions();

  // For any ticker not in MARKET_DATA cache, try to fetch it
  const apiKey=getApiKey();
  for (const pos of current) {
    const ticker=pos.ticker.toUpperCase();
    let found=false;
    for (const k of Object.keys(MARKET_DATA)) {
      if ((MARKET_DATA[k].ticker||'').toUpperCase()===ticker) { found=true; break; }
    }
    if (!found && apiKey) {
      try {
        const cached=await dbGet(ticker);
        if (cached && isFresh(cached.fetchedAt)) {
          MARKET_DATA[ticker]={ ...cached.data, label:pos.label, currency:pos.currency, ticker };
        } else {
          const series=await fetchTicker(ticker, null);
          const entry={label:pos.label, currency:pos.currency, ticker, series};
          await dbSet(ticker, entry);
          MARKET_DATA[ticker]=entry;
        }
      } catch(e) { console.warn('Could not fetch', ticker, e.message); }
    }
  }

  const results = current.map(pos => runSingleDCA(pos, gStart, gEnd)).filter(Boolean);
  if (!results.length) return;

  document.getElementById('dcaResults').style.display='block';
  renderDCAMetrics(results);
  renderDCACharts(results);
  renderPosBreakdown(results);
  renderDCAContrib(results[0]);
}

function renderDCAMetrics(results) {
  const totInv = results.reduce((a,r)=>a+r.totalInvested,0);
  const totVal  = results.reduce((a,r)=>a+r.finalValue,0);
  const totGain = totVal-totInv;
  const avgRet  = results.reduce((a,r)=>a+r.totalRet,0)/results.length;
  const avgCAGR = results.reduce((a,r)=>a+r.cagr,0)/results.length;
  const lumpTot = results.reduce((a,r)=>a+r.lumpFinal,0);
  const items=[
    {label:'Total Invested',  val:num(totInv)},
    {label:'Portfolio Value', val:num(totVal)},
    {label:'Total Gain',      val:num(totGain), col:cls(totGain)},
    {label:'Avg Total Return',val:sign(avgRet)+avgRet.toFixed(2)+'%', col:cls(avgRet)},
    {label:'Avg CAGR',        val:sign(avgCAGR)+avgCAGR.toFixed(2)+'%', sub:'annualized'},
    {label:'Lump Sum Value',  val:num(lumpTot), sub:'same capital at start'},
    {label:'Positions',       val:results.length+''},
  ];
  document.getElementById('dcaMetrics').innerHTML=items.map(it=>
    `<div class="dca-metric"><div class="dm-label">${it.label}</div><div class="dm-val ${it.col||''}">${it.val}</div>${it.sub?`<div class="dm-sub">${it.sub}</div>`:''}</div>`
  ).join('');
}

function renderDCACharts(results) {
  // Align all timelines on common dates
  const labels=results[0].timeline.map(p=>p.date.slice(0,7));
  // Aggregate value and invested
  const aggVal=labels.map((_,i)=>results.reduce((a,r)=>a+(r.timeline[Math.min(i,r.timeline.length-1)]?.value||0),0));
  const aggInv=labels.map((_,i)=>results.reduce((a,r)=>a+(r.timeline[Math.min(i,r.timeline.length-1)]?.invested||0),0));
  const datasets1=[
    {label:'Portfolio Value',data:aggVal.map(v=>+v.toFixed(2)),borderColor:'#1b4f8c',fill:false,borderWidth:2,tension:.2},
    {label:'Capital Invested',data:aggInv.map(v=>+v.toFixed(2)),borderColor:'#9e9d97',borderDash:[4,3],fill:false,borderWidth:1.5,tension:.1},
  ];
  // Per-position stacked area
  results.forEach((r,i)=>{
    datasets1.push({label:`${r.pos.label} (${r.pos.ticker})`,data:r.timeline.map(p=>+p.value.toFixed(2)),borderColor:PALETTE[i+2]||'#999',borderDash:[3,2],fill:false,borderWidth:1,tension:.2});
  });
  const o1=lineOpts(true);
  o1.plugins.legend={display:true,labels:{boxWidth:10,font:{size:10},padding:10}};
  o1.plugins.tooltip.callbacks.label=ctx=>`${ctx.dataset.label}: ${num(ctx.parsed.y)}`;
  mkChart('chartDCA1',{type:'line',data:{labels,datasets:datasets1},options:o1});

  // DCA vs Lump sum
  const lumpAgg=labels.map((_,i)=>{
    return results.reduce((a,r)=>{
      const base=r.timeline[0].price;
      const lumpShares=r.totalInvested/base;
      const price=r.timeline[Math.min(i,r.timeline.length-1)]?.price||base;
      return a+lumpShares*price;
    },0);
  });
  const o2=lineOpts(true);
  o2.plugins.tooltip.callbacks.label=ctx=>`${ctx.dataset.label}: ${num(ctx.parsed.y)}`;
  mkChart('chartDCA2',{type:'line',data:{labels,datasets:[
    {label:'DCA Portfolio',data:aggVal.map(v=>+v.toFixed(2)),borderColor:'#1b4f8c',fill:false,borderWidth:2,tension:.2},
    {label:'Lump Sum',data:lumpAgg.map(v=>+v.toFixed(2)),borderColor:'#d97706',borderDash:[6,3],fill:false,borderWidth:1.5,tension:.2},
    {label:'Capital Invested',data:aggInv.map(v=>+v.toFixed(2)),borderColor:'#9e9d97',borderDash:[4,3],fill:false,borderWidth:1.2,tension:.1},
  ]},options:o2});
}

function renderPosBreakdown(results) {
  const totVal=results.reduce((a,r)=>a+r.finalValue,0);
  let h='<div style="overflow-x:auto"><table class="dt"><thead><tr><th>Position</th><th>Type</th><th>Envelope</th><th>Invested</th><th>Value</th><th>Gain</th><th>Return</th><th>CAGR</th><th>vs Lump</th><th>Weight</th></tr></thead><tbody>';
  results.forEach((r,i)=>{
    const env=r.pos.envelope||'standard';
    const weight=(r.finalValue/totVal*100).toFixed(1);
    const vsLump=r.finalValue-r.lumpFinal;
    h+=`<tr>
      <td>
        <div style="font-weight:700;font-family:monospace;font-size:12px">${r.pos.ticker}</div>
        <div style="font-size:10px;color:var(--muted);font-family:'Helvetica Neue',Arial,sans-serif">${r.pos.label}</div>
      </td>
      <td><span class="tag tag-${r.pos.type==='etf'?'etf':r.pos.type==='stock'?'stk':'sp'}">${r.pos.type.toUpperCase()}</span></td>
      <td><span class="${ENVELOPE_CSS[env]||'env-std'}">${ENVELOPE_LABELS[env]||env}</span>${r.envelopeHit?'<span style="font-size:9px;color:var(--warn-text);margin-left:4px">⚠ limit</span>':''}</td>
      <td style="font-family:'Helvetica Neue',Arial,sans-serif">${num(r.totalInvested)}</td>
      <td style="font-family:'Helvetica Neue',Arial,sans-serif;font-weight:600">${num(r.finalValue)}</td>
      <td class="${cls(r.gain)}">${sign(r.gain)}${num(Math.abs(r.gain))}</td>
      <td class="${cls(r.totalRet)}">${sign(r.totalRet)}${r.totalRet.toFixed(2)}%</td>
      <td class="${cls(r.cagr)}">${sign(r.cagr)}${r.cagr.toFixed(2)}%</td>
      <td class="${cls(vsLump)}">${sign(vsLump)}${num(Math.abs(vsLump))}</td>
      <td style="font-family:'Helvetica Neue',Arial,sans-serif">
        <div style="display:flex;align-items:center;gap:6px">
          <div style="background:${PALETTE[i]};height:6px;width:${weight}px;border-radius:3px;max-width:60px"></div>
          <span style="font-size:11px">${weight}%</span>
        </div>
      </td>
    </tr>`;
  });
  h+='</tbody></table></div>';
  document.getElementById('posBreakdown').innerHTML=h;
}

function renderDCAContrib(result) {
  if (!result) return;
  const sample=result.timeline.filter((_,i)=>i%13===0||i===result.timeline.length-1);
  let h='<div style="overflow-x:auto"><table class="dt"><thead><tr><th>Date</th><th>Price</th><th>Shares</th><th>Invested</th><th>Value</th><th>Gain</th></tr></thead><tbody>';
  sample.forEach(p=>{
    const gl=p.value-p.invested;
    h+=`<tr><td style="font-family:'Helvetica Neue',Arial,sans-serif">${p.date}</td><td style="font-family:monospace">${num(p.price)}</td><td style="font-family:'Helvetica Neue',Arial,sans-serif">${p.shares.toLocaleString()}</td><td>${num(p.invested)}</td><td style="font-weight:600">${num(p.value)}</td><td class="${cls(gl)}">${sign(gl)}${num(Math.abs(gl))}</td></tr>`;
  });
  h+='</tbody></table></div><div style="font-size:10px;color:var(--faint);font-family:\'Helvetica Neue\',Arial,sans-serif;margin-top:6px">Showing quarterly snapshots for first position. Fetch data for custom tickers via the Data Settings tab.</div>';
  document.getElementById('dcaContribTable').innerHTML=h;
}

// ╔══════════════════════════════════════════════════════════════╗
// ║  INIT                                                        ║
// ╚══════════════════════════════════════════════════════════════╝
window.addEventListener('DOMContentLoaded', async () => {
  // Restore API key from localStorage
  const savedKey = localStorage.getItem('avApiKey');
  if (savedKey) document.getElementById('apiKeyInput').value = savedKey;
  document.getElementById('apiKeyInput').addEventListener('change', e => {
    localStorage.setItem('avApiKey', e.target.value.trim());
  });

  // Pre-populate 3 default positions
  addPosition('index');
  addPosition('etf');
  addPosition('stock');

  // Load from IndexedDB cache first, then render
  await loadFromCache();
  renderOverview();
});
</script>
</body>
</html>
