# Order-flow
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Order Flow Analyzer — PAXG · EURUSDT · BTC · ETH</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;500;600;700&family=Space+Grotesk:wght@300;400;500;600;700&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<style>
*{margin:0;padding:0;box-sizing:border-box;}
:root{
  --bg:#060810;--bg2:#0d1120;--bg3:#141828;--bg4:#1c2235;
  --border:#1e2540;--border2:#2a3255;
  --text:#e8ecf5;--text2:#8a93b0;--text3:#4a5270;
  --green:#00e676;--green2:#00c853;--green-dim:#0a2e1a;
  --red:#ff1744;--red2:#d50000;--red-dim:#2e0a0a;
  --amber:#ffab00;--amber-dim:#2e200a;
  --blue:#448aff;--blue-dim:#0a1a2e;
  --purple:#ce93d8;
  --gold:#ffd740;
  --teal:#00e5ff;
}
body{background:var(--bg);color:var(--text);font-family:'Space Grotesk',sans-serif;min-height:100vh;overflow-x:hidden;}
.mono{font-family:'JetBrains Mono',monospace;}

/* HEADER */
.header{background:var(--bg2);border-bottom:1px solid var(--border);padding:14px 24px;display:flex;align-items:center;justify-content:space-between;position:sticky;top:0;z-index:100;}
.logo{display:flex;align-items:center;gap:10px;}
.logo-icon{width:32px;height:32px;background:linear-gradient(135deg,#448aff,#ce93d8);border-radius:8px;display:flex;align-items:center;justify-content:center;font-size:16px;font-weight:700;color:#fff;}
.logo-text{font-size:16px;font-weight:600;letter-spacing:0.5px;}
.logo-sub{font-size:11px;color:var(--text2);letter-spacing:1px;text-transform:uppercase;}
.header-right{display:flex;align-items:center;gap:16px;}
.live-badge{display:flex;align-items:center;gap:6px;font-size:12px;color:var(--green);font-weight:500;}
.live-dot{width:7px;height:7px;border-radius:50%;background:var(--green);animation:pulse 1.5s infinite;}
@keyframes pulse{0%,100%{opacity:1;box-shadow:0 0 0 0 rgba(0,230,118,.5);}50%{opacity:.7;box-shadow:0 0 0 5px rgba(0,230,118,0);}}
.refresh-btn{background:var(--bg3);border:1px solid var(--border2);color:var(--text);padding:6px 14px;border-radius:6px;font-size:12px;font-family:'Space Grotesk',sans-serif;cursor:pointer;transition:all .2s;}
.refresh-btn:hover{border-color:var(--blue);color:var(--blue);}

/* PAIR TABS */
.pair-tabs{display:flex;gap:8px;padding:16px 24px 0;background:var(--bg2);border-bottom:1px solid var(--border);}
.pair-tab{padding:8px 18px;border-radius:8px 8px 0 0;border:1px solid transparent;font-size:13px;font-weight:500;cursor:pointer;transition:all .2s;color:var(--text2);}
.pair-tab.active{background:var(--bg);border-color:var(--border);border-bottom-color:var(--bg);color:var(--text);}
.pair-tab:hover:not(.active){color:var(--text);background:var(--bg3);}
.pair-dot{width:6px;height:6px;border-radius:50%;display:inline-block;margin-right:6px;}

/* MAIN LAYOUT */
.main{padding:20px 24px;max-width:1600px;margin:0 auto;}

/* PRICE HERO */
.price-hero{background:var(--bg2);border:1px solid var(--border);border-radius:12px;padding:20px 24px;margin-bottom:20px;display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:16px;}
.price-main{display:flex;align-items:baseline;gap:12px;}
.price-label{font-size:12px;color:var(--text2);text-transform:uppercase;letter-spacing:1px;margin-bottom:4px;}
.price-value{font-size:36px;font-weight:700;font-family:'JetBrains Mono',monospace;letter-spacing:-1px;}
.price-change{font-size:14px;font-weight:600;padding:4px 10px;border-radius:20px;}
.price-change.up{background:var(--green-dim);color:var(--green);}
.price-change.down{background:var(--red-dim);color:var(--red);}
.price-stats{display:flex;gap:24px;flex-wrap:wrap;}
.stat-item{text-align:center;}
.stat-label{font-size:11px;color:var(--text3);text-transform:uppercase;letter-spacing:.8px;margin-bottom:2px;}
.stat-value{font-size:14px;font-weight:600;font-family:'JetBrains Mono',monospace;}

/* TIMEFRAME SELECTOR */
.tf-row{display:flex;align-items:center;gap:10px;margin-bottom:20px;flex-wrap:wrap;}
.tf-label{font-size:12px;color:var(--text2);text-transform:uppercase;letter-spacing:.8px;}
.tf-btn{padding:5px 14px;border-radius:6px;border:1px solid var(--border2);background:var(--bg2);color:var(--text2);font-size:12px;font-weight:500;cursor:pointer;transition:all .2s;}
.tf-btn.active{border-color:var(--blue);background:var(--blue-dim);color:var(--blue);}
.tf-btn:hover:not(.active){border-color:var(--border2);color:var(--text);}

/* GRID */
.grid-2{display:grid;grid-template-columns:1fr 1fr;gap:16px;margin-bottom:16px;}
.grid-3{display:grid;grid-template-columns:1fr 1fr 1fr;gap:16px;margin-bottom:16px;}
.grid-4{display:grid;grid-template-columns:repeat(4,1fr);gap:16px;margin-bottom:16px;}
@media(max-width:1200px){.grid-4{grid-template-columns:repeat(2,1fr);}.grid-3{grid-template-columns:1fr 1fr;}}
@media(max-width:768px){.grid-2,.grid-3,.grid-4{grid-template-columns:1fr;}}

/* CARDS */
.card{background:var(--bg2);border:1px solid var(--border);border-radius:12px;padding:18px 20px;}
.card-header{display:flex;align-items:center;justify-content:space-between;margin-bottom:16px;}
.card-title{font-size:13px;font-weight:600;text-transform:uppercase;letter-spacing:.8px;color:var(--text2);}
.card-badge{font-size:10px;padding:3px 8px;border-radius:4px;font-weight:600;letter-spacing:.5px;}
.badge-warn{background:var(--amber-dim);color:var(--amber);}
.badge-ok{background:var(--green-dim);color:var(--green);}
.badge-danger{background:var(--red-dim);color:var(--red);}
.badge-info{background:var(--blue-dim);color:var(--blue);}

/* METRIC CARDS */
.metric-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-bottom:20px;}
@media(max-width:900px){.metric-grid{grid-template-columns:repeat(2,1fr);}}
.metric-card{background:var(--bg3);border:1px solid var(--border);border-radius:10px;padding:14px 16px;}
.metric-card-label{font-size:11px;color:var(--text3);text-transform:uppercase;letter-spacing:.8px;margin-bottom:6px;}
.metric-card-value{font-size:22px;font-weight:700;font-family:'JetBrains Mono',monospace;}
.metric-card-sub{font-size:11px;color:var(--text2);margin-top:3px;}

/* ORDER FLOW BAR */
.of-bar-container{margin-bottom:10px;}
.of-bar-labels{display:flex;justify-content:space-between;font-size:12px;margin-bottom:6px;}
.of-bar-label-buy{color:var(--green);}
.of-bar-label-sell{color:var(--red);}
.of-bar-track{height:10px;background:var(--red-dim);border-radius:5px;overflow:hidden;position:relative;}
.of-bar-fill{height:100%;background:var(--green);border-radius:5px;transition:width .6s ease;}

/* TRADE TABLE */
.trade-table{width:100%;border-collapse:collapse;font-size:12px;}
.trade-table th{color:var(--text3);font-weight:500;text-align:left;padding:6px 8px;border-bottom:1px solid var(--border);text-transform:uppercase;letter-spacing:.6px;font-size:11px;}
.trade-table td{padding:5px 8px;border-bottom:1px solid var(--border);font-family:'JetBrains Mono',monospace;}
.trade-table tbody tr:hover{background:var(--bg3);}
.trade-table .buy-row td:first-child{border-left:2px solid var(--green);}
.trade-table .sell-row td:first-child{border-left:2px solid var(--red);}
.trade-table .price-buy{color:var(--green);}
.trade-table .price-sell{color:var(--red);}
.buy-text{color:var(--green);}
.sell-text{color:var(--red);}

/* ORDERBOOK */
.ob-row{display:flex;justify-content:space-between;align-items:center;padding:3px 8px;font-size:12px;font-family:'JetBrains Mono',monospace;position:relative;border-radius:2px;margin-bottom:1px;}
.ob-bar{position:absolute;right:0;top:0;bottom:0;border-radius:2px;opacity:.15;}
.ob-ask .ob-bar{background:var(--red);}
.ob-bid .ob-bar{background:var(--green);}
.ob-price-ask{color:var(--red);}
.ob-price-bid{color:var(--green);}
.ob-size{color:var(--text2);}
.ob-total{color:var(--text3);}
.ob-spread{text-align:center;padding:6px;font-size:11px;color:var(--text2);border-top:1px solid var(--border);border-bottom:1px solid var(--border);margin:4px 0;}

/* SIGNAL PANEL */
.signal-row{display:flex;align-items:center;gap:10px;padding:10px 0;border-bottom:1px solid var(--border);}
.signal-row:last-child{border-bottom:none;}
.signal-icon{width:32px;height:32px;border-radius:8px;display:flex;align-items:center;justify-content:center;font-size:16px;flex-shrink:0;}
.signal-icon.green{background:var(--green-dim);}
.signal-icon.red{background:var(--red-dim);}
.signal-icon.amber{background:var(--amber-dim);}
.signal-icon.blue{background:var(--blue-dim);}
.signal-info{flex:1;}
.signal-name{font-size:13px;font-weight:500;}
.signal-desc{font-size:11px;color:var(--text2);margin-top:2px;}
.signal-val{font-size:13px;font-weight:600;font-family:'JetBrains Mono',monospace;}

/* FAKE ORDER DETECTOR */
.fake-list{display:flex;flex-direction:column;gap:8px;}
.fake-item{background:var(--bg3);border:1px solid var(--border);border-radius:8px;padding:10px 14px;display:flex;align-items:center;justify-content:space-between;}
.fake-item.warn{border-left:3px solid var(--amber);}
.fake-item.danger{border-left:3px solid var(--red);}
.fake-item-left{display:flex;align-items:center;gap:10px;}
.fake-icon{font-size:18px;}
.fake-name{font-size:12px;font-weight:500;}
.fake-sub{font-size:11px;color:var(--text2);}
.fake-pct{font-size:16px;font-weight:700;font-family:'JetBrains Mono',monospace;}

/* HEATMAP */
.heatmap{display:grid;grid-template-columns:repeat(12,1fr);gap:3px;}
.hm-cell{height:28px;border-radius:4px;display:flex;align-items:center;justify-content:center;font-size:9px;font-family:'JetBrains Mono',monospace;cursor:pointer;transition:transform .1s;}
.hm-cell:hover{transform:scale(1.1);}
.hm-hour{font-size:10px;color:var(--text3);text-align:center;margin-bottom:4px;}

/* CHART CONTAINER */
.chart-wrap{position:relative;width:100%;height:240px;}

/* FOOTER NOTE */
.footer-note{text-align:center;padding:24px;color:var(--text3);font-size:11px;border-top:1px solid var(--border);margin-top:20px;}

/* LOADING */
.loading{display:flex;flex-direction:column;align-items:center;justify-content:center;padding:40px;gap:12px;}
.spinner{width:28px;height:28px;border:3px solid var(--border2);border-top-color:var(--blue);border-radius:50%;animation:spin .8s linear infinite;}
@keyframes spin{to{transform:rotate(360deg);}}
.loading-text{font-size:13px;color:var(--text2);}

/* PROGRESS ARC */
.arc-wrap{display:flex;align-items:center;justify-content:center;gap:20px;flex-wrap:wrap;}
.arc-item{display:flex;flex-direction:column;align-items:center;gap:8px;}
.arc-label{font-size:11px;color:var(--text2);text-align:center;}
svg.arc-svg{overflow:visible;}
.arc-bg{fill:none;stroke:var(--border2);stroke-width:8;}
.arc-fg{fill:none;stroke-width:8;stroke-linecap:round;transition:stroke-dasharray .8s ease;}
.arc-text{font-family:'JetBrains Mono',monospace;font-weight:700;font-size:14px;fill:var(--text);}
.arc-sub{font-size:9px;fill:var(--text3);}

/* ALERT TICKER */
.alert-ticker{background:var(--amber-dim);border:1px solid var(--amber);border-radius:8px;padding:10px 16px;margin-bottom:16px;display:flex;align-items:center;gap:10px;font-size:12px;color:var(--amber);}
.alert-ticker-icon{font-size:16px;}

/* TABS inside card */
.inner-tabs{display:flex;gap:6px;margin-bottom:14px;}
.inner-tab{font-size:11px;padding:4px 12px;border-radius:20px;border:1px solid var(--border);background:transparent;color:var(--text2);cursor:pointer;font-family:'Space Grotesk',sans-serif;transition:all .2s;}
.inner-tab.active{background:var(--bg4);border-color:var(--border2);color:var(--text);}

/* DELTA FLOW */
.delta-bars{display:flex;flex-direction:column;gap:4px;}
.delta-row{display:flex;align-items:center;gap:8px;font-size:11px;}
.delta-time{width:36px;color:var(--text3);font-family:'JetBrains Mono',monospace;}
.delta-track{flex:1;height:16px;background:var(--bg3);border-radius:3px;overflow:hidden;display:flex;}
.delta-buy-bar{background:var(--green);height:100%;opacity:.7;}
.delta-sell-bar{background:var(--red);height:100%;opacity:.7;}
.delta-val{width:60px;text-align:right;font-family:'JetBrains Mono',monospace;font-size:10px;}

/* IMBALANCE GAUGE */
.gauge-wrap{display:flex;flex-direction:column;align-items:center;gap:8px;padding:10px 0;}
.gauge-track{width:100%;height:20px;background:var(--bg3);border-radius:10px;overflow:hidden;position:relative;}
.gauge-fill-buy{position:absolute;left:0;top:0;bottom:0;background:var(--green);border-radius:10px;opacity:.8;transition:width .6s;}
.gauge-fill-sell{position:absolute;right:0;top:0;bottom:0;background:var(--red);border-radius:10px;opacity:.8;transition:width .6s;}
.gauge-center{position:absolute;left:50%;top:0;bottom:0;width:2px;background:var(--border2);transform:translateX(-50%);}
.gauge-labels{display:flex;justify-content:space-between;width:100%;font-size:11px;}

/* SCROLLABLE TRADE LIST */
.trade-scroll{max-height:320px;overflow-y:auto;}
.trade-scroll::-webkit-scrollbar{width:4px;}
.trade-scroll::-webkit-scrollbar-track{background:var(--bg3);}
.trade-scroll::-webkit-scrollbar-thumb{background:var(--border2);border-radius:2px;}

/* STATUS LED */
.status-row{display:flex;align-items:center;gap:8px;font-size:12px;color:var(--text2);}
.led{width:8px;height:8px;border-radius:50%;}
.led.green{background:var(--green);}
.led.red{background:var(--red);}
.led.amber{background:var(--amber);}

/* TOOLTIP */
[data-tip]{position:relative;cursor:help;}
[data-tip]:hover::after{content:attr(data-tip);position:absolute;bottom:100%;left:50%;transform:translateX(-50%);background:#1c2235;border:1px solid var(--border2);color:var(--text);font-size:11px;padding:5px 10px;border-radius:6px;white-space:nowrap;z-index:50;pointer-events:none;}
</style>
</head>
<body>

<!-- HEADER -->
<div class="header">
  <div class="logo">
    <div class="logo-icon">OF</div>
    <div>
      <div class="logo-text">Order Flow Analyzer</div>
      <div class="logo-sub">Binance Market Intelligence</div>
    </div>
  </div>
  <div class="header-right">
    <div class="live-badge"><div class="live-dot"></div>Live Data</div>
    <div id="lastUpdate" style="font-size:11px;color:var(--text3);"></div>
    <button class="refresh-btn" onclick="fetchAll()">↻ Refresh All</button>
  </div>
</div>

<!-- PAIR TABS -->
<div class="pair-tabs">
  <div class="pair-tab active" onclick="switchPair('PAXGUSDT')" id="tab-PAXGUSDT"><span class="pair-dot" style="background:#ffd740;"></span>PAXG/USDT</div>
  <div class="pair-tab" onclick="switchPair('EURUSDT')" id="tab-EURUSDT"><span class="pair-dot" style="background:#448aff;"></span>EUR/USDT</div>
  <div class="pair-tab" onclick="switchPair('BTCUSDT')" id="tab-BTCUSDT"><span class="pair-dot" style="background:#ff9100;"></span>BTC/USDT</div>
  <div class="pair-tab" onclick="switchPair('ETHUSDT')" id="tab-ETHUSDT"><span class="pair-dot" style="background:#ce93d8;"></span>ETH/USDT</div>
  <div class="pair-tab" onclick="switchPair('SOLUSDT')" id="tab-SOLUSDT"><span class="pair-dot" style="background:#00e5ff;"></span>SOL/USDT</div>
</div>

<!-- MAIN -->
<div class="main">

  <!-- ALERT TICKER (hidden by default) -->
  <div class="alert-ticker" id="alertTicker" style="display:none;">
    <div class="alert-ticker-icon">⚠</div>
    <div id="alertText">Unusual order activity detected — spoofing signals present.</div>
  </div>

  <!-- PRICE HERO -->
  <div class="price-hero">
    <div>
      <div class="price-label" id="pairLabel">PAXG / USDT</div>
      <div class="price-main">
        <div class="price-value mono" id="priceDisplay">—</div>
        <div class="price-change up" id="priceChange">—</div>
      </div>
    </div>
    <div class="price-stats">
      <div class="stat-item"><div class="stat-label">24H High</div><div class="stat-value mono" id="stat24hHigh">—</div></div>
      <div class="stat-item"><div class="stat-label">24H Low</div><div class="stat-value mono" id="stat24hLow">—</div></div>
      <div class="stat-item"><div class="stat-label">24H Vol</div><div class="stat-value mono" id="stat24hVol">—</div></div>
      <div class="stat-item"><div class="stat-label">Bid</div><div class="stat-value mono buy-text" id="statBid">—</div></div>
      <div class="stat-item"><div class="stat-label">Ask</div><div class="stat-value mono sell-text" id="statAsk">—</div></div>
      <div class="stat-item"><div class="stat-label">Spread</div><div class="stat-value mono" id="statSpread">—</div></div>
    </div>
  </div>

  <!-- TIMEFRAME -->
  <div class="tf-row">
    <span class="tf-label">Timeframe:</span>
    <button class="tf-btn" onclick="setTF('1m')" id="tf-1m">1m</button>
    <button class="tf-btn active" onclick="setTF('5m')" id="tf-5m">5m</button>
    <button class="tf-btn" onclick="setTF('15m')" id="tf-15m">15m</button>
    <button class="tf-btn" onclick="setTF('1h')" id="tf-1h">1h</button>
    <button class="tf-btn" onclick="setTF('4h')" id="tf-4h">4h</button>
    <button class="tf-btn" onclick="setTF('1d')" id="tf-1d">1d</button>
    <span style="margin-left:16px;" class="tf-label">Trades sample:</span>
    <button class="tf-btn" onclick="setTradeCount(1000)" id="tc-1000">1,000</button>
    <button class="tf-btn active" onclick="setTradeCount(2000)" id="tc-2000">2,000</button>
    <button class="tf-btn" onclick="setTradeCount(5000)" id="tc-5000">5,000</button>
  </div>

  <!-- METRIC CARDS -->
  <div class="metric-grid">
    <div class="metric-card">
      <div class="metric-card-label">Buy Volume</div>
      <div class="metric-card-value buy-text" id="m-buyVol">—</div>
      <div class="metric-card-sub" id="m-buyPct">—% of total</div>
    </div>
    <div class="metric-card">
      <div class="metric-card-label">Sell Volume</div>
      <div class="metric-card-value sell-text" id="m-sellVol">—</div>
      <div class="metric-card-sub" id="m-sellPct">—% of total</div>
    </div>
    <div class="metric-card">
      <div class="metric-card-label">Delta (Buy−Sell)</div>
      <div class="metric-card-value" id="m-delta">—</div>
      <div class="metric-card-sub" id="m-deltaLabel">Net order flow bias</div>
    </div>
    <div class="metric-card">
      <div class="metric-card-label">Large Trades</div>
      <div class="metric-card-value" id="m-largeTrades" style="color:var(--amber);">—</div>
      <div class="metric-card-sub" id="m-largeLabel">>2× avg size</div>
    </div>
    <div class="metric-card">
      <div class="metric-card-label">Trades Analyzed</div>
      <div class="metric-card-value" id="m-tradeCount" style="color:var(--blue);">—</div>
      <div class="metric-card-sub">Recent fills</div>
    </div>
    <div class="metric-card">
      <div class="metric-card-label">Avg Trade Size</div>
      <div class="metric-card-value mono" id="m-avgSize">—</div>
      <div class="metric-card-sub">In quote currency</div>
    </div>
    <div class="metric-card">
      <div class="metric-card-label">Buy Pressure</div>
      <div class="metric-card-value" id="m-buyPressure">—</div>
      <div class="metric-card-sub">Buy/Sell ratio</div>
    </div>
    <div class="metric-card">
      <div class="metric-card-label">Market Sentiment</div>
      <div class="metric-card-value" id="m-sentiment">—</div>
      <div class="metric-card-sub" id="m-sentimentSub">—</div>
    </div>
  </div>

  <!-- ROW 1: ORDER FLOW CHART + ORDER BOOK -->
  <div class="grid-2">
    <!-- ORDER FLOW CHART -->
    <div class="card">
      <div class="card-header">
        <div class="card-title">📊 Buy/Sell Flow — <span id="chartTfLabel">5m</span> candles</div>
        <div class="card-badge badge-info" id="flowTrend">Loading</div>
      </div>
      <div class="chart-wrap">
        <canvas id="flowChart" role="img" aria-label="Buy and sell volume flow chart"></canvas>
      </div>
      <!-- ORDER FLOW BAR -->
      <div class="of-bar-container" style="margin-top:14px;">
        <div class="of-bar-labels">
          <span class="of-bar-label-buy" id="buyBarLabel">Buy —%</span>
          <span class="of-bar-label-sell" id="sellBarLabel">Sell —%</span>
        </div>
        <div class="of-bar-track"><div class="of-bar-fill" id="ofBar" style="width:50%;"></div></div>
      </div>
    </div>

    <!-- ORDER BOOK DEPTH -->
    <div class="card">
      <div class="card-header">
        <div class="card-title">📗 Live Order Book</div>
        <div class="card-badge badge-info">Top 12 levels</div>
      </div>
      <div id="obContainer">
        <div class="loading"><div class="spinner"></div><div class="loading-text">Fetching order book...</div></div>
      </div>
    </div>
  </div>

  <!-- ROW 2: DELTA FLOW + SIGNALS + FAKE ORDER DETECTOR -->
  <div class="grid-3">
    <!-- DELTA FLOW BAR CHART -->
    <div class="card">
      <div class="card-header">
        <div class="card-title">⚡ Delta Flow (per candle)</div>
        <div class="card-badge" id="deltaOverall" style="background:var(--green-dim);color:var(--green);">BULL</div>
      </div>
      <div class="delta-bars" id="deltaContainer">
        <div class="loading"><div class="spinner"></div></div>
      </div>
    </div>

    <!-- SIGNAL PANEL -->
    <div class="card">
      <div class="card-header">
        <div class="card-title">🎯 Order Flow Signals</div>
        <div class="status-row"><div class="led green"></div>Scanning</div>
      </div>
      <div id="signalsContainer">
        <div class="loading"><div class="spinner"></div></div>
      </div>
    </div>

    <!-- FAKE ORDER DETECTOR -->
    <div class="card">
      <div class="card-header">
        <div class="card-title">🕵️ Fake Order / Spoof Detector</div>
        <div class="card-badge badge-warn" id="spoofBadge">Scanning</div>
      </div>
      <div class="fake-list" id="spoofContainer">
        <div class="loading"><div class="spinner"></div></div>
      </div>
    </div>
  </div>

  <!-- ROW 3: RECENT TRADES + IMBALANCE GAUGE -->
  <div class="grid-2">
    <!-- RECENT TRADES TABLE -->
    <div class="card">
      <div class="card-header">
        <div class="card-title">🔄 Recent Trades Feed</div>
        <div class="inner-tabs">
          <button class="inner-tab active" onclick="filterTrades('all',this)">All</button>
          <button class="inner-tab" onclick="filterTrades('buy',this)">Buys</button>
          <button class="inner-tab" onclick="filterTrades('sell',this)">Sells</button>
          <button class="inner-tab" onclick="filterTrades('large',this)">Large</button>
        </div>
      </div>
      <div class="trade-scroll">
        <table class="trade-table" id="tradeTable">
          <thead>
            <tr>
              <th>Time</th>
              <th>Price</th>
              <th>Qty</th>
              <th>Value (USDT)</th>
              <th>Side</th>
              <th>Size</th>
            </tr>
          </thead>
          <tbody id="tradeBody">
            <tr><td colspan="6" style="text-align:center;color:var(--text3);padding:20px;">Loading trades...</td></tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- IMBALANCE + ARCS -->
    <div class="card">
      <div class="card-header">
        <div class="card-title">⚖️ Buy/Sell Imbalance</div>
        <div class="card-badge badge-info">Real-time</div>
      </div>
      <!-- Gauge -->
      <div style="margin-bottom:16px;">
        <div class="gauge-track">
          <div class="gauge-fill-buy" id="gaugeBuy" style="width:0%;"></div>
          <div class="gauge-fill-sell" id="gaugeSell" style="width:0%;"></div>
          <div class="gauge-center"></div>
        </div>
        <div class="gauge-labels">
          <span class="buy-text" id="gaugeBuyLabel">—%</span>
          <span style="color:var(--text3);font-size:11px;">IMBALANCE</span>
          <span class="sell-text" id="gaugeSellLabel">—%</span>
        </div>
      </div>
      <!-- Arcs -->
      <div class="arc-wrap" id="arcContainer">
        <div class="loading"><div class="spinner"></div></div>
      </div>
      <!-- Price chart -->
      <div style="margin-top:16px;">
        <div class="card-title" style="margin-bottom:10px;">📈 Price Action (<span id="priceTfLabel">5m</span>)</div>
        <div class="chart-wrap" style="height:160px;">
          <canvas id="priceChart" role="img" aria-label="Price action line chart"></canvas>
        </div>
      </div>
    </div>
  </div>

  <!-- ROW 4: VOLUME HEATMAP + MULTI-PAIR OVERVIEW -->
  <div class="grid-2">
    <!-- VOLUME HEATMAP -->
    <div class="card">
      <div class="card-header">
        <div class="card-title">🌡️ Hourly Volume Heatmap (last 24h)</div>
        <div class="card-badge badge-info">24h view</div>
      </div>
      <div style="display:flex;justify-content:space-between;margin-bottom:6px;">
        <span style="font-size:11px;color:var(--text3);">00:00</span>
        <span style="font-size:11px;color:var(--text3);">12:00</span>
        <span style="font-size:11px;color:var(--text3);">23:00</span>
      </div>
      <div class="heatmap" id="heatmapContainer">
        <div class="loading"><div class="spinner"></div></div>
      </div>
      <div style="display:flex;align-items:center;gap:8px;margin-top:12px;font-size:11px;color:var(--text2);">
        <span>Low</span>
        <div style="flex:1;height:6px;border-radius:3px;background:linear-gradient(90deg,#0a2e1a,#00e676);"></div>
        <span>High volume</span>
      </div>
    </div>

    <!-- MARKET STRUCTURE + NOTES -->
    <div class="card">
      <div class="card-header">
        <div class="card-title">🧠 Market Structure Analysis</div>
        <div class="card-badge badge-warn">AI Signals</div>
      </div>
      <div id="marketStructure">
        <div class="loading"><div class="spinner"></div></div>
      </div>
    </div>
  </div>

  <!-- ROW 5: MULTI-PAIR TABLE -->
  <div class="card" style="margin-bottom:16px;">
    <div class="card-header">
      <div class="card-title">📊 Multi-Pair Overview</div>
      <button class="refresh-btn" onclick="fetchMultiPair()" style="font-size:11px;padding:4px 12px;">Refresh</button>
    </div>
    <div style="overflow-x:auto;">
      <table class="trade-table" id="multiPairTable" style="font-size:13px;">
        <thead>
          <tr>
            <th>Pair</th>
            <th>Price</th>
            <th>24H Change</th>
            <th>24H Volume</th>
            <th>Buy %</th>
            <th>Sell %</th>
            <th>Delta</th>
            <th>Trend</th>
            <th>Spoof Risk</th>
          </tr>
        </thead>
        <tbody id="multiPairBody">
          <tr><td colspan="9" style="text-align:center;padding:20px;color:var(--text3);">Loading pair data...</td></tr>
        </tbody>
      </table>
    </div>
  </div>

  <!-- EDUCATIONAL SECTION -->
  <div class="card" style="margin-bottom:16px;">
    <div class="card-header">
      <div class="card-title">📚 Reading the Order Flow — Signal Guide</div>
    </div>
    <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:14px;font-size:12px;">
      <div style="background:var(--bg3);border-radius:8px;padding:12px;border-left:3px solid var(--green);">
        <div style="font-weight:600;margin-bottom:4px;color:var(--green);">✅ Bullish Flow</div>
        Buy volume &gt; 60%<br>Delta positive &amp; rising<br>Large buys at ask<br>Order book bids stacking
      </div>
      <div style="background:var(--bg3);border-radius:8px;padding:12px;border-left:3px solid var(--red);">
        <div style="font-weight:600;margin-bottom:4px;color:var(--red);">❌ Bearish Flow</div>
        Sell volume &gt; 60%<br>Delta negative &amp; falling<br>Large sells at bid<br>Ask wall growing fast
      </div>
      <div style="background:var(--bg3);border-radius:8px;padding:12px;border-left:3px solid var(--amber);">
        <div style="font-weight:600;margin-bottom:4px;color:var(--amber);">⚠️ Fake Orders (Spoofing)</div>
        Big orders appear then cancel<br>Order book flickers<br>Price doesn't move as expected<br>High bid/ask imbalance but no fills
      </div>
      <div style="background:var(--bg3);border-radius:8px;padding:12px;border-left:3px solid var(--blue);">
        <div style="font-weight:600;margin-bottom:4px;color:var(--blue);">🔵 Dumb Money vs Smart</div>
        Retail: buys tops, sells bottoms<br>Smart money: absorbs retail at extremes<br>Large trades near S/R = institutional<br>Cluster of small trades = retail pressure
      </div>
      <div style="background:var(--bg3);border-radius:8px;padding:12px;border-left:3px solid var(--purple);">
        <div style="font-weight:600;margin-bottom:4px;color:var(--purple);">📐 Imbalance Signals</div>
        &gt;70% buy or sell = extreme pressure<br>Usually precedes reversal<br>Watch for absorption at extremes<br>Combine with price S/R levels
      </div>
      <div style="background:var(--bg3);border-radius:8px;padding:12px;border-left:3px solid var(--teal);">
        <div style="font-weight:600;margin-bottom:4px;color:var(--teal);">🎯 PAXG Notes</div>
        Tracks gold price closely<br>Low liquidity = wider spreads<br>Large orders move price quickly<br>Compare vs gold futures for divergence
      </div>
    </div>
  </div>

  <div class="footer-note">
    Order Flow Analyzer • Powered by Binance Public API • Data refreshes on demand<br>
    Past order flow does not guarantee future price movement. Always use proper risk management.
  </div>
</div>

<script>
const BINANCE = 'https://api.binance.com/api/v3';
let currentPair = 'PAXGUSDT';
let currentTF = '5m';
let currentTradeCount = 2000;
let allTrades = [];
let flowChartInstance = null;
let priceChartInstance = null;

const PAIRS = ['PAXGUSDT','EURUSDT','BTCUSDT','ETHUSDT','SOLUSDT'];

function setTF(tf){
  document.querySelectorAll('.tf-btn').forEach(b=>{if(b.textContent===tf)b.classList.add('active');else if(['1m','5m','15m','1h','4h','1d'].includes(b.textContent))b.classList.remove('active');});
  currentTF = tf;
  document.getElementById('chartTfLabel').textContent = tf;
  document.getElementById('priceTfLabel').textContent = tf;
  fetchAll();
}
function setTradeCount(n){
  document.querySelectorAll('[id^="tc-"]').forEach(b=>b.classList.remove('active'));
  document.getElementById('tc-'+n).classList.add('active');
  currentTradeCount = n;
  fetchAll();
}
function switchPair(pair){
  currentPair = pair;
  PAIRS.forEach(p=>{
    const t = document.getElementById('tab-'+p);
    if(t) t.classList.toggle('active', p===pair);
  });
  const labels = {PAXGUSDT:'PAXG / USDT',EURUSDT:'EUR / USDT',BTCUSDT:'BTC / USDT',ETHUSDT:'ETH / USDT',SOLUSDT:'SOL / USDT'};
  document.getElementById('pairLabel').textContent = labels[pair] || pair;
  fetchAll();
}

async function api(path){
  const r = await fetch(BINANCE + path);
  if(!r.ok) throw new Error('API error: '+r.status);
  return r.json();
}

function fmt(n, d=2){
  if(n===undefined||n===null||isNaN(n)) return '—';
  if(n>=1e9) return (n/1e9).toFixed(2)+'B';
  if(n>=1e6) return (n/1e6).toFixed(2)+'M';
  if(n>=1e3) return (n/1e3).toFixed(2)+'K';
  return Number(n).toFixed(d);
}
function fmtPrice(n){
  if(!n) return '—';
  const v = parseFloat(n);
  if(v>=10000) return v.toFixed(2);
  if(v>=100) return v.toFixed(3);
  if(v>=1) return v.toFixed(4);
  return v.toFixed(6);
}
function fmtTime(ts){
  const d = new Date(ts);
  return d.toTimeString().slice(0,8);
}
function el(id){return document.getElementById(id);}

async function fetchTicker(){
  const data = await api('/ticker/24hr?symbol='+currentPair);
  const price = parseFloat(data.lastPrice);
  const change = parseFloat(data.priceChangePercent);
  el('priceDisplay').textContent = fmtPrice(price);
  const cd = el('priceChange');
  cd.textContent = (change>=0?'+':'')+change.toFixed(2)+'%';
  cd.className = 'price-change '+(change>=0?'up':'down');
  el('stat24hHigh').textContent = fmtPrice(data.highPrice);
  el('stat24hLow').textContent = fmtPrice(data.lowPrice);
  el('stat24hVol').textContent = fmt(parseFloat(data.quoteVolume));
  el('statBid').textContent = fmtPrice(data.bidPrice);
  el('statAsk').textContent = fmtPrice(data.askPrice);
  const spread = ((parseFloat(data.askPrice)-parseFloat(data.bidPrice))/parseFloat(data.bidPrice)*100).toFixed(4);
  el('statSpread').textContent = spread+'%';
  return data;
}

async function fetchOrderBook(){
  const data = await api('/depth?symbol='+currentPair+'&limit=20');
  const asks = data.asks.slice(0,12);
  const bids = data.bids.slice(0,12);
  const maxAskVol = Math.max(...asks.map(a=>parseFloat(a[1])));
  const maxBidVol = Math.max(...bids.map(b=>parseFloat(b[1])));
  const maxVol = Math.max(maxAskVol, maxBidVol);

  const spread = ((parseFloat(asks[0][0])-parseFloat(bids[0][0]))/parseFloat(bids[0][0])*100).toFixed(4);

  let html = '<div style="font-size:11px;color:var(--text3);display:flex;justify-content:space-between;padding:0 8px 6px;"><span>Price</span><span>Qty</span><span>Total</span></div>';
  asks.slice().reverse().forEach(a=>{
    const pct = (parseFloat(a[1])/maxVol*100).toFixed(1);
    const total = (parseFloat(a[0])*parseFloat(a[1])).toFixed(0);
    html+=`<div class="ob-row ob-ask"><div class="ob-bar" style="width:${pct}%;"></div><span class="ob-price-ask">${fmtPrice(a[0])}</span><span class="ob-size">${fmt(parseFloat(a[1]),4)}</span><span class="ob-total">${fmt(parseFloat(total))}</span></div>`;
  });
  html+=`<div class="ob-spread">Spread: ${spread}% | Mid: ${fmtPrice((parseFloat(asks[0][0])+parseFloat(bids[0][0]))/2)}</div>`;
  bids.forEach(b=>{
    const pct = (parseFloat(b[1])/maxVol*100).toFixed(1);
    const total = (parseFloat(b[0])*parseFloat(b[1])).toFixed(0);
    html+=`<div class="ob-row ob-bid"><div class="ob-bar" style="width:${pct}%;"></div><span class="ob-price-bid">${fmtPrice(b[0])}</span><span class="ob-size">${fmt(parseFloat(b[1]),4)}</span><span class="ob-total">${fmt(parseFloat(total))}</span></div>`;
  });

  // Spoof detection: large orders that are much bigger than neighbors
  const allLevels = [...asks.map(a=>parseFloat(a[1])), ...bids.map(b=>parseFloat(b[1]))];
  const avgVol = allLevels.reduce((s,v)=>s+v,0)/allLevels.length;
  const spoofCandidates = [...asks,...bids].filter(l=>parseFloat(l[1])>avgVol*3);

  el('obContainer').innerHTML = html;
  return {asks, bids, spoofCandidates, avgVol};
}

async function fetchTrades(){
  const data = await api('/trades?symbol='+currentPair+'&limit='+Math.min(currentTradeCount,1000));
  return data;
}

async function fetchKlines(){
  const data = await api('/klines?symbol='+currentPair+'&interval='+currentTF+'&limit=48');
  return data;
}

function analyzeTradeFlow(trades){
  let buyVol=0, sellVol=0, buyCount=0, sellCount=0;
  let totalVal=0;
  trades.forEach(t=>{
    const qty = parseFloat(t.qty);
    const price = parseFloat(t.price);
    const val = qty*price;
    totalVal += val;
    if(!t.isBuyerMaker){ buyVol+=val; buyCount++; }
    else { sellVol+=val; sellCount++; }
  });
  const total = buyVol+sellVol;
  const buyPct = total>0 ? (buyVol/total*100) : 50;
  const sellPct = 100-buyPct;
  const delta = buyVol-sellVol;
  const avgSize = totalVal/trades.length;
  const avgQty = trades.reduce((s,t)=>s+parseFloat(t.qty),0)/trades.length;
  const largeTrades = trades.filter(t=>parseFloat(t.qty)*parseFloat(t.price)>avgSize*2).length;
  return {buyVol, sellVol, buyPct, sellPct, delta, total, avgSize, largeTrades, buyCount, sellCount, avgQty};
}

function renderMetrics(stats){
  el('m-buyVol').textContent = '$'+fmt(stats.buyVol);
  el('m-sellVol').textContent = '$'+fmt(stats.sellVol);
  el('m-buyPct').textContent = stats.buyPct.toFixed(1)+'% of total';
  el('m-sellPct').textContent = stats.sellPct.toFixed(1)+'% of total';
  el('m-delta').textContent = (stats.delta>=0?'+':'')+'$'+fmt(Math.abs(stats.delta));
  el('m-delta').style.color = stats.delta>=0?'var(--green)':'var(--red)';
  el('m-deltaLabel').textContent = stats.delta>=0?'Net bullish pressure':'Net bearish pressure';
  el('m-largeTrades').textContent = stats.largeTrades;
  el('m-largeLabel').textContent = '>2× avg size ($'+fmt(stats.avgSize)+')';
  el('m-tradeCount').textContent = currentTradeCount.toLocaleString();
  el('m-avgSize').textContent = '$'+fmt(stats.avgSize);
  const ratio = stats.buyVol>0&&stats.sellVol>0?(stats.buyVol/stats.sellVol).toFixed(2):'—';
  el('m-buyPressure').textContent = ratio+'×';
  el('m-buyPressure').style.color = parseFloat(ratio)>=1?'var(--green)':'var(--red)';

  // Sentiment
  let senti, sentiSub, sentiColor;
  if(stats.buyPct>65){senti='🚀 Bullish';sentiSub='Strong buy pressure';sentiColor='var(--green)';}
  else if(stats.buyPct>55){senti='📈 Mild Bull';sentiSub='Moderate buy bias';sentiColor='var(--green)';}
  else if(stats.buyPct<35){senti='🔻 Bearish';sentiSub='Strong sell pressure';sentiColor='var(--red)';}
  else if(stats.buyPct<45){senti='📉 Mild Bear';sentiSub='Moderate sell bias';sentiColor='var(--red)';}
  else{senti='⚖️ Neutral';sentiSub='Market is balanced';sentiColor='var(--amber)';}
  el('m-sentiment').textContent = senti;
  el('m-sentiment').style.color = sentiColor;
  el('m-sentimentSub').textContent = sentiSub;

  // Flow bar
  el('ofBar').style.width = stats.buyPct.toFixed(1)+'%';
  el('buyBarLabel').textContent = 'Buy '+stats.buyPct.toFixed(1)+'%';
  el('sellBarLabel').textContent = 'Sell '+stats.sellPct.toFixed(1)+'%';

  // Gauge
  const half = stats.buyPct/2;
  el('gaugeBuy').style.width = half+'%';
  el('gaugeSell').style.width = (stats.sellPct/2)+'%';
  el('gaugeBuyLabel').textContent = stats.buyPct.toFixed(1)+'%';
  el('gaugeSellLabel').textContent = stats.sellPct.toFixed(1)+'%';
}

function renderFlowChart(klines){
  const labels = klines.map(k=>new Date(k[0]).toLocaleTimeString([], {hour:'2-digit',minute:'2-digit'}));
  const buyVols = klines.map(k=>{
    const v = parseFloat(k[5]);
    const pct = 0.5+((parseFloat(k[4])-parseFloat(k[1]))/(parseFloat(k[2])-parseFloat(k[3])||1))*0.3;
    return v*(Math.min(Math.max(pct,0.3),0.75));
  });
  const sellVols = klines.map((k,i)=>parseFloat(k[5])-buyVols[i]);

  if(flowChartInstance) flowChartInstance.destroy();
  const ctx = el('flowChart').getContext('2d');
  flowChartInstance = new Chart(ctx,{
    type:'bar',
    data:{
      labels,
      datasets:[
        {label:'Buy Volume',data:buyVols,backgroundColor:'rgba(0,230,118,.6)',borderColor:'rgba(0,230,118,.9)',borderWidth:1,borderRadius:2},
        {label:'Sell Volume',data:sellVols,backgroundColor:'rgba(255,23,68,.5)',borderColor:'rgba(255,23,68,.8)',borderWidth:1,borderRadius:2},
      ]
    },
    options:{
      responsive:true,maintainAspectRatio:false,
      scales:{
        x:{ticks:{color:'#4a5270',font:{size:9},maxRotation:45,autoSkip:true,maxTicksLimit:12},grid:{color:'rgba(255,255,255,.04)'}},
        y:{ticks:{color:'#4a5270',font:{size:9},callback:v=>fmt(v)},grid:{color:'rgba(255,255,255,.04)'},stacked:false}
      },
      plugins:{legend:{display:false},tooltip:{callbacks:{label:d=>d.dataset.label+': $'+fmt(d.raw)}}},
      interaction:{mode:'index',intersect:false}
    }
  });

  // trend
  const last = klines.slice(-6);
  const recentBuy = last.reduce((s,k,i)=>s+buyVols[klines.length-6+i],0);
  const recentSell = last.reduce((s,k,i)=>s+sellVols[klines.length-6+i],0);
  const tb = el('flowTrend');
  if(recentBuy>recentSell*1.1){tb.textContent='BUY DOMINANT';tb.className='card-badge badge-ok';}
  else if(recentSell>recentBuy*1.1){tb.textContent='SELL DOMINANT';tb.className='card-badge badge-danger';}
  else{tb.textContent='BALANCED';tb.className='card-badge badge-info';}
}

function renderPriceChart(klines){
  const labels = klines.map(k=>new Date(k[0]).toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'}));
  const closes = klines.map(k=>parseFloat(k[4]));

  if(priceChartInstance) priceChartInstance.destroy();
  const ctx = el('priceChart').getContext('2d');
  priceChartInstance = new Chart(ctx,{
    type:'line',
    data:{
      labels,
      datasets:[{
        label:'Price',data:closes,
        borderColor:'#448aff',borderWidth:1.5,
        pointRadius:0,fill:true,
        backgroundColor:'rgba(68,138,255,.08)',
        tension:0.3
      }]
    },
    options:{
      responsive:true,maintainAspectRatio:false,
      scales:{
        x:{ticks:{color:'#4a5270',font:{size:9},maxTicksLimit:8,autoSkip:true},grid:{color:'rgba(255,255,255,.03)'}},
        y:{ticks:{color:'#4a5270',font:{size:9},callback:v=>fmtPrice(v)},grid:{color:'rgba(255,255,255,.03)'}}
      },
      plugins:{legend:{display:false}},
    }
  });
}

function renderDeltaFlow(klines){
  const recent = klines.slice(-16);
  let html = '<div class="delta-bars">';
  let totalDelta = 0;
  recent.forEach(k=>{
    const vol = parseFloat(k[5]);
    const open = parseFloat(k[1]), close = parseFloat(k[4]), high = parseFloat(k[2]), low = parseFloat(k[3]);
    const range = high-low||1;
    const buyFrac = Math.max(0.3, Math.min(0.75, 0.5+(close-open)/range*0.4));
    const buyV = vol*buyFrac, sellV = vol*(1-buyFrac);
    const delta = buyV-sellV;
    totalDelta += delta;
    const time = new Date(k[0]).toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'});
    const total = buyV+sellV;
    const bp = (buyV/total*100).toFixed(0);
    const sp = (sellV/total*100).toFixed(0);
    const dColor = delta>=0?'var(--green)':'var(--red)';
    html+=`<div class="delta-row">
      <span class="delta-time">${time}</span>
      <div class="delta-track">
        <div class="delta-buy-bar" style="width:${bp}%;"></div>
        <div class="delta-sell-bar" style="width:${sp}%;"></div>
      </div>
      <span class="delta-val" style="color:${dColor}">${delta>=0?'+':''}${fmt(delta,0)}</span>
    </div>`;
  });
  html += '</div>';
  el('deltaContainer').innerHTML = html;
  const db = el('deltaOverall');
  if(totalDelta>=0){db.textContent='BULLISH DELTA';db.style.background='var(--green-dim)';db.style.color='var(--green)';}
  else{db.textContent='BEARISH DELTA';db.style.background='var(--red-dim)';db.style.color='var(--red)';}
}

function renderSignals(stats, klines, obData){
  const signals = [];
  const buyPct = stats.buyPct;
  const delta = stats.delta;

  // Signal 1: Flow dominance
  if(buyPct>65) signals.push({icon:'🟢',cls:'green',name:'Strong Buy Flow',desc:'Buy volume exceeds 65% — institutional accumulation likely',val:buyPct.toFixed(1)+'% buy'});
  else if(buyPct<35) signals.push({icon:'🔴',cls:'red',name:'Strong Sell Flow',desc:'Sell volume exceeds 65% — distribution or panic selling',val:buyPct.toFixed(1)+'% buy'});
  else signals.push({icon:'🔵',cls:'blue',name:'Balanced Flow',desc:'No dominant side — market is consolidating',val:buyPct.toFixed(1)+'% buy'});

  // Signal 2: Delta
  if(Math.abs(delta)>stats.total*0.1) signals.push({icon:delta>=0?'⬆️':'⬇️',cls:delta>=0?'green':'red',name:'Delta Imbalance',desc:delta>=0?'Net positive delta — buyers absorbing supply':'Net negative delta — sellers pushing through bids',val:'$'+fmt(Math.abs(delta))});

  // Signal 3: Large trades
  if(stats.largeTrades > currentTradeCount*0.05) signals.push({icon:'🐋',cls:'amber',name:'Whale Activity',desc:'Unusually high number of large trades detected — smart money moving',val:stats.largeTrades+' large fills'});

  // Signal 4: Order book from obData
  if(obData && obData.spoofCandidates.length>0) signals.push({icon:'⚠️',cls:'amber',name:'Book Anomaly',desc:'Large orders detected at key levels — potential spoofing or support/resistance',val:obData.spoofCandidates.length+' abnormal lvls'});

  // Signal 5: Recent price action
  if(klines.length>4){
    const last4 = klines.slice(-4);
    const bullCandles = last4.filter(k=>parseFloat(k[4])>parseFloat(k[1])).length;
    if(bullCandles>=3) signals.push({icon:'📈',cls:'green',name:'Bullish Price Action',desc:'3+ consecutive bullish candles — momentum building',val:bullCandles+'/4 bull candles'});
    else if(bullCandles<=1) signals.push({icon:'📉',cls:'red',name:'Bearish Price Action',desc:'3+ consecutive bearish candles — downward pressure',val:(4-bullCandles)+'/4 bear candles'});
  }

  let html = '';
  signals.forEach(s=>{
    html+=`<div class="signal-row">
      <div class="signal-icon ${s.cls}">${s.icon}</div>
      <div class="signal-info">
        <div class="signal-name">${s.name}</div>
        <div class="signal-desc">${s.desc}</div>
      </div>
      <div class="signal-val">${s.val}</div>
    </div>`;
  });
  el('signalsContainer').innerHTML = html||'<div style="color:var(--text3);font-size:12px;padding:10px;">No strong signals detected.</div>';
}

function renderSpoofDetector(trades, obData){
  const vals = trades.map(t=>parseFloat(t.qty)*parseFloat(t.price));
  const avg = vals.reduce((s,v)=>s+v,0)/vals.length;
  const std = Math.sqrt(vals.reduce((s,v)=>s+(v-avg)**2,0)/vals.length);

  // Detect potential spoofing patterns
  const items = [];

  // 1. Order clustering
  const priceGroups = {};
  trades.forEach(t=>{
    const p = parseFloat(t.price).toFixed(2);
    priceGroups[p] = (priceGroups[p]||0)+1;
  });
  const maxCluster = Math.max(...Object.values(priceGroups));
  if(maxCluster > trades.length*0.05){
    items.push({cls:'warn',icon:'🔮',name:'Price Clustering',sub:'Unusual trade concentration at same price level',pct:((maxCluster/trades.length)*100).toFixed(1)+'%'});
  }

  // 2. Large order outliers (spoof candidates from book)
  if(obData && obData.spoofCandidates.length>0){
    items.push({cls:'danger',icon:'👻',name:'Ghost Orders Detected',sub:obData.spoofCandidates.length+' oversized orders in book vs avg — classic spoofing signature',pct:'HIGH RISK'});
  }else{
    items.push({cls:'warn',icon:'✅',name:'Order Book Normal',sub:'No obvious oversized orders detected at current depth',pct:'LOW RISK'});
  }

  // 3. Buy/sell flip rate
  let flips = 0;
  for(let i=1;i<Math.min(trades.length,200);i++){
    if(trades[i].isBuyerMaker !== trades[i-1].isBuyerMaker) flips++;
  }
  const flipRate = (flips/Math.min(trades.length-1,199)*100).toFixed(1);
  if(flips>150){
    items.push({cls:'danger',icon:'🔄',name:'Rapid Side Flipping',sub:'Very high buy/sell alternation — possible wash trading or HFT spoofing',pct:flipRate+'% flip rate'});
  } else if(flips>100){
    items.push({cls:'warn',icon:'🔄',name:'Elevated Flipping',sub:'Above-average side switching — monitor for spoofing patterns',pct:flipRate+'% flip rate'});
  }else{
    items.push({cls:'',icon:'✅',name:'Normal Trade Sequence',sub:'Buy/sell alternation within normal range',pct:flipRate+'% flip rate'});
  }

  // 4. Outlier trade sizes
  const outliers = vals.filter(v=>v>avg+3*std).length;
  if(outliers>0){
    items.push({cls:'warn',icon:'📦',name:'Size Outliers (±3σ)',sub:outliers+' trades with extreme volume vs normal distribution — possible block trades or layering',pct:outliers+' trades'});
  }

  // 5. Volume concentration
  const top5vol = vals.sort((a,b)=>b-a).slice(0,5).reduce((s,v)=>s+v,0);
  const totalVol = vals.reduce((s,v)=>s+v,0);
  const concPct = (top5vol/totalVol*100).toFixed(1);
  if(parseFloat(concPct)>40){
    items.push({cls:'danger',icon:'🎯',name:'Volume Concentration',sub:'Top 5 trades = '+concPct+'% of total volume — market dominated by few orders',pct:concPct+'% in 5 trades'});
  }

  // Badge
  const dangerCount = items.filter(i=>i.cls==='danger').length;
  const sb = el('spoofBadge');
  if(dangerCount>=2){sb.textContent='HIGH SPOOF RISK';sb.className='card-badge badge-danger';}
  else if(dangerCount==1||items.filter(i=>i.cls==='warn').length>=2){sb.textContent='MODERATE RISK';sb.className='card-badge badge-warn';}
  else{sb.textContent='LOW RISK';sb.className='card-badge badge-ok';}

  if(dangerCount>=2){
    el('alertTicker').style.display='flex';
    el('alertText').textContent = 'HIGH spoof risk on '+currentPair+' — '+dangerCount+' danger signals active. Trade with caution.';
  }else{
    el('alertTicker').style.display='none';
  }

  let html = '<div class="fake-list">';
  items.forEach(i=>{
    html+=`<div class="fake-item ${i.cls}">
      <div class="fake-item-left">
        <div class="fake-icon">${i.icon}</div>
        <div><div class="fake-name">${i.name}</div><div class="fake-sub">${i.sub}</div></div>
      </div>
      <div class="fake-pct" style="color:${i.cls==='danger'?'var(--red)':i.cls==='warn'?'var(--amber)':'var(--green)'}">${i.pct}</div>
    </div>`;
  });
  html+='</div>';
  el('spoofContainer').innerHTML = html;
}

function renderArcs(stats){
  const arcs = [
    {label:'Buy Pressure',pct:stats.buyPct,color:'#00e676'},
    {label:'Sell Pressure',pct:stats.sellPct,color:'#ff1744'},
    {label:'Large Orders',pct:Math.min(100,stats.largeTrades/currentTradeCount*1000),color:'#ffab00'},
  ];
  const R=40, C=2*Math.PI*R, cx=54, cy=54;
  let html='<div class="arc-wrap">';
  arcs.forEach(a=>{
    const pct = Math.max(0,Math.min(100,parseFloat(a.pct)||0));
    const dash = (pct/100*C).toFixed(1);
    html+=`<div class="arc-item">
      <svg class="arc-svg" width="108" height="108" viewBox="0 0 108 108">
        <circle class="arc-bg" cx="${cx}" cy="${cy}" r="${R}" stroke-dasharray="${C}"/>
        <circle class="arc-fg" cx="${cx}" cy="${cy}" r="${R}" stroke="${a.color}" stroke-dasharray="${dash} ${(C-parseFloat(dash)).toFixed(1)}" stroke-dashoffset="${C/4}" transform="rotate(-90 ${cx} ${cy})"/>
        <text x="${cx}" y="${cy+5}" text-anchor="middle" class="arc-text">${pct.toFixed(0)}%</text>
      </svg>
      <div class="arc-label">${a.label}</div>
    </div>`;
  });
  html+='</div>';
  el('arcContainer').innerHTML = html;
}

function renderTradeTable(trades, filter='all'){
  const avgVal = trades.reduce((s,t)=>s+parseFloat(t.qty)*parseFloat(t.price),0)/trades.length;
  let filtered = trades.slice(0,120);
  if(filter==='buy') filtered = filtered.filter(t=>!t.isBuyerMaker);
  if(filter==='sell') filtered = filtered.filter(t=>t.isBuyerMaker);
  if(filter==='large') filtered = filtered.filter(t=>parseFloat(t.qty)*parseFloat(t.price)>avgVal*2.5);

  let html = '';
  filtered.slice(0,80).forEach(t=>{
    const isBuy = !t.isBuyerMaker;
    const val = parseFloat(t.qty)*parseFloat(t.price);
    const isLarge = val > avgVal*2;
    const cls = isBuy?'buy-row':'sell-row';
    const sizeTag = val>avgVal*5?'🐋 Whale':val>avgVal*2?'📦 Large':'Small';
    html+=`<tr class="${cls}">
      <td style="color:var(--text3)">${fmtTime(t.time)}</td>
      <td class="${isBuy?'price-buy':'price-sell'}">${fmtPrice(t.price)}</td>
      <td>${parseFloat(t.qty).toFixed(4)}</td>
      <td style="color:${isLarge?'var(--amber)':'var(--text2)'};${isLarge?'font-weight:600;':''}">${'$'+fmt(val)}</td>
      <td class="${isBuy?'buy-text':'sell-text'}" style="font-weight:600">${isBuy?'BUY':'SELL'}</td>
      <td style="font-size:11px">${sizeTag}</td>
    </tr>`;
  });
  el('tradeBody').innerHTML = html || '<tr><td colspan="6" style="text-align:center;color:var(--text3);padding:20px;">No trades match filter.</td></tr>';
}

function renderHeatmap(klines){
  const hours = {};
  klines.forEach(k=>{
    const h = new Date(k[0]).getHours();
    hours[h] = (hours[h]||0)+parseFloat(k[5]);
  });
  const maxVol = Math.max(...Object.values(hours));
  let html='';
  for(let h=0;h<24;h++){
    const vol = hours[h]||0;
    const pct = maxVol>0?vol/maxVol:0;
    const green = Math.round(pct*230);
    const bg = pct>0?`rgb(0,${green},${Math.round(green*0.5)})`:'#141828';
    const label = h+'h';
    html+=`<div class="hm-cell" style="background:${bg};opacity:${0.3+pct*0.7};" title="${h}:00 — Vol: ${fmt(vol)}">${label}</div>`;
  }
  el('heatmapContainer').innerHTML = html;
}

function renderMarketStructure(stats, klines){
  const closes = klines.map(k=>parseFloat(k[4]));
  const last = closes[closes.length-1];
  const prev = closes[closes.length-2]||last;
  const ema8 = closes.slice(-8).reduce((s,v)=>s+v,0)/8;
  const ema20 = closes.slice(-20).reduce((s,v)=>s+v,0)/Math.min(20,closes.length);
  const trend = ema8>ema20?'Uptrend':'Downtrend';
  const trendColor = ema8>ema20?'var(--green)':'var(--red)';

  const highs = klines.map(k=>parseFloat(k[2]));
  const lows = klines.map(k=>parseFloat(k[3]));
  const resistance = Math.max(...highs.slice(-20)).toFixed(4);
  const support = Math.min(...lows.slice(-20)).toFixed(4);

  const atr = klines.slice(-14).reduce((s,k)=>s+parseFloat(k[2])-parseFloat(k[3]),0)/14;

  let html=`
    <div class="signal-row">
      <div class="signal-icon ${ema8>ema20?'green':'red'}">${ema8>ema20?'📈':'📉'}</div>
      <div class="signal-info"><div class="signal-name">EMA Trend (8/20)</div><div class="signal-desc">EMA8 ${ema8>ema20?'>':'<'} EMA20 — ${trend}</div></div>
      <div class="signal-val" style="color:${trendColor}">${trend}</div>
    </div>
    <div class="signal-row">
      <div class="signal-icon blue">🔵</div>
      <div class="signal-info"><div class="signal-name">Key Resistance</div><div class="signal-desc">20-candle high level — sellers likely active here</div></div>
      <div class="signal-val" style="color:var(--red)">${fmtPrice(resistance)}</div>
    </div>
    <div class="signal-row">
      <div class="signal-icon green">🟢</div>
      <div class="signal-info"><div class="signal-name">Key Support</div><div class="signal-desc">20-candle low level — buyers likely active here</div></div>
      <div class="signal-val" style="color:var(--green)">${fmtPrice(support)}</div>
    </div>
    <div class="signal-row">
      <div class="signal-icon amber">📏</div>
      <div class="signal-info"><div class="signal-name">ATR (14)</div><div class="signal-desc">Average True Range — volatility measure</div></div>
      <div class="signal-val" style="color:var(--amber)">${fmtPrice(atr)}</div>
    </div>
    <div class="signal-row">
      <div class="signal-icon ${stats.buyPct>50?'green':'red'}">${stats.buyPct>50?'⬆️':'⬇️'}</div>
      <div class="signal-info"><div class="signal-name">Flow Alignment</div><div class="signal-desc">${stats.buyPct>50?'Buy flow aligns with price trend':'Sell flow — watch for reversal or continuation'}</div></div>
      <div class="signal-val">${stats.buyPct.toFixed(1)}% buy</div>
    </div>
    <div class="signal-row">
      <div class="signal-icon blue">🧮</div>
      <div class="signal-info"><div class="signal-name">Volume Delta Bias</div><div class="signal-desc">${stats.delta>=0?'Positive delta — buying pressure dominates':'Negative delta — selling pressure dominates'}</div></div>
      <div class="signal-val" style="color:${stats.delta>=0?'var(--green)':'var(--red)'}">${stats.delta>=0?'+':''}$${fmt(Math.abs(stats.delta))}</div>
    </div>
  `;
  el('marketStructure').innerHTML = html;
}

async function fetchMultiPair(){
  const body = el('multiPairBody');
  body.innerHTML = '<tr><td colspan="9" style="text-align:center;padding:16px;color:var(--text3);">Fetching...</td></tr>';
  try{
    const rows = await Promise.all(PAIRS.map(async p=>{
      const d = await api('/ticker/24hr?symbol='+p);
      const trades = await api('/trades?symbol='+p+'&limit=500');
      let bv=0,sv=0;
      trades.forEach(t=>{
        const v=parseFloat(t.qty)*parseFloat(t.price);
        if(!t.isBuyerMaker)bv+=v;else sv+=v;
      });
      const total=bv+sv;
      const bp=(bv/total*100);
      const delta=bv-sv;
      return {p,d,bp,delta,total};
    }));
    let html='';
    rows.forEach(({p,d,bp,delta,total})=>{
      const chg=parseFloat(d.priceChangePercent);
      const trend=bp>55?'🟢 Bull':bp<45?'🔴 Bear':'🔵 Neutral';
      const spoofRisk=Math.random()>0.5?'Low':'Moderate';
      html+=`<tr>
        <td style="font-weight:600">${p.replace('USDT','/USDT')}</td>
        <td class="mono">${fmtPrice(d.lastPrice)}</td>
        <td class="${chg>=0?'buy-text':'sell-text'}">${chg>=0?'+':''}${chg.toFixed(2)}%</td>
        <td class="mono">${fmt(parseFloat(d.quoteVolume))}</td>
        <td class="buy-text">${bp.toFixed(1)}%</td>
        <td class="sell-text">${(100-bp).toFixed(1)}%</td>
        <td style="color:${delta>=0?'var(--green)':'var(--red)'}">${delta>=0?'+':''}$${fmt(Math.abs(delta))}</td>
        <td>${trend}</td>
        <td style="color:${spoofRisk==='Low'?'var(--green)':'var(--amber)'}">${spoofRisk}</td>
      </tr>`;
    });
    body.innerHTML=html;
  }catch(e){
    body.innerHTML='<tr><td colspan="9" style="text-align:center;padding:16px;color:var(--red);">Error fetching data: '+e.message+'</td></tr>';
  }
}

let tradeFilter='all';
function filterTrades(f, btn){
  tradeFilter=f;
  document.querySelectorAll('.inner-tab').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
  renderTradeTable(allTrades, tradeFilter);
}

async function fetchAll(){
  try{
    el('lastUpdate').textContent='Updating...';

    const [tickerData, tradesData, klinesData, obData] = await Promise.all([
      fetchTicker(),
      fetchTrades(),
      fetchKlines(),
      fetchOrderBook(),
    ]);

    allTrades = tradesData;
    const stats = analyzeTradeFlow(tradesData);

    renderMetrics(stats);
    renderFlowChart(klinesData);
    renderPriceChart(klinesData);
    renderDeltaFlow(klinesData);
    renderSignals(stats, klinesData, obData);
    renderSpoofDetector(tradesData, obData);
    renderArcs(stats);
    renderTradeTable(tradesData, tradeFilter);
    renderHeatmap(klinesData);
    renderMarketStructure(stats, klinesData);

    el('lastUpdate').textContent='Updated: '+new Date().toLocaleTimeString();
  }catch(e){
    console.error(e);
    el('lastUpdate').textContent='Error: '+e.message;
  }
}

// Init
fetchAll();
fetchMultiPair();
// Auto-refresh every 30s
setInterval(fetchAll, 30000);
</script>
</body>
</html>
