<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Yodge Perfume — Inventory System</title>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=Jost:wght@200;300;400;500&display=swap" rel="stylesheet"/>
<style>
:root {
  --cream:#f5f0e8;--gold:#b89a5e;--gold-l:#d4b97a;--gold-d:#8a6f3a;
  --dark:#1a1814;--dark2:#252219;--dark3:#2e2a22;--muted:#8a7f6e;
  --border:rgba(184,154,94,.2);--border2:rgba(184,154,94,.08);
  --ok:#5a9e6f;--ok-bg:rgba(90,158,111,.12);--ok-b:rgba(90,158,111,.28);
  --low:#c9883a;--low-bg:rgba(201,136,58,.12);--low-b:rgba(201,136,58,.28);
  --crit:#c0402e;--crit-bg:rgba(192,64,46,.12);--crit-b:rgba(192,64,46,.28);
  --rose:#a0566a;--rose-bg:rgba(160,86,106,.1);--rose-b:rgba(160,86,106,.25);
  --blue:#3a6b8a;--blue-bg:rgba(58,107,138,.1);--blue-b:rgba(58,107,138,.25);
}
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
body{font-family:'Jost',sans-serif;background:var(--dark);color:var(--cream);min-height:100vh;display:flex;overflow-x:hidden;font-size:14px}
.sb{width:224px;min-height:100vh;background:var(--dark2);border-right:1px solid var(--border);display:flex;flex-direction:column;flex-shrink:0;position:sticky;top:0;height:100vh;overflow-y:auto}
.sb-brand{padding:28px 22px 22px;border-bottom:1px solid var(--border)}
.sb-brand .n{font-family:'Cormorant Garamond',serif;font-size:22px;font-weight:300;letter-spacing:4px;text-transform:uppercase;color:var(--gold-l);line-height:1}
.sb-brand .s{font-size:9px;letter-spacing:4px;text-transform:uppercase;color:var(--muted);margin-top:5px;font-weight:200}
.nav-lbl{font-size:8px;letter-spacing:2.5px;text-transform:uppercase;color:var(--muted);padding:14px 22px 5px;opacity:.55}
.nv{display:flex;align-items:center;gap:10px;padding:11px 22px;font-size:12px;font-weight:300;letter-spacing:.7px;color:var(--muted);cursor:pointer;border-left:2px solid transparent;transition:all .18s;user-select:none}
.nv:hover{color:var(--gold-l);background:rgba(184,154,94,.04);border-left-color:rgba(184,154,94,.3)}
.nv.act{color:var(--gold-l);background:rgba(184,154,94,.07);border-left-color:var(--gold)}
.nv svg{width:14px;height:14px;flex-shrink:0}
.sb-ft{padding:16px 22px;border-top:1px solid var(--border);font-size:9.5px;color:var(--muted);font-weight:200;line-height:1.7;margin-top:auto}
.main{flex:1;display:flex;flex-direction:column;min-width:0;overflow:hidden}
.topbar{background:var(--dark2);border-bottom:1px solid var(--border);height:54px;display:flex;align-items:center;justify-content:space-between;padding:0 28px;position:sticky;top:0;z-index:50;flex-shrink:0}
.tb-l{font-family:'Cormorant Garamond',serif;font-size:17px;font-weight:300;letter-spacing:.8px}
.tb-l em{color:var(--gold);font-style:italic}
.tb-r{display:flex;align-items:center;gap:14px}
.tb-date{font-size:10px;color:var(--muted);letter-spacing:.3px;font-weight:200}
.av{width:30px;height:30px;border-radius:50%;background:linear-gradient(135deg,var(--gold-d),var(--gold-l));display:flex;align-items:center;justify-content:center;font-size:11px;font-weight:500;color:var(--dark);flex-shrink:0}
.content{padding:24px 28px;flex:1;overflow-y:auto}
.page{display:none;animation:fu .26s ease}
.page.act{display:block}
@keyframes fu{from{opacity:0;transform:translateY(6px)}to{opacity:1;transform:translateY(0)}}
.ph{margin-bottom:20px}
.ph h1{font-family:'Cormorant Garamond',serif;font-size:26px;font-weight:300;letter-spacing:.8px;margin-bottom:2px}
.ph h1 em{color:var(--gold);font-style:italic}
.ph p{font-size:11px;color:var(--muted);font-weight:200}
.gr{width:32px;height:1px;background:linear-gradient(90deg,var(--gold),transparent);margin:8px 0 14px}
.stats{display:grid;grid-template-columns:repeat(5,1fr);gap:12px;margin-bottom:20px}
.sc{background:var(--dark3);border:1px solid var(--border);padding:16px;position:relative;overflow:hidden;transition:border-color .2s}
.sc::after{content:'';position:absolute;top:0;left:0;right:0;height:1px;background:linear-gradient(90deg,var(--gold-d),var(--gold-l),transparent);opacity:0;transition:opacity .2s}
.sc:hover{border-color:rgba(184,154,94,.4)}.sc:hover::after{opacity:1}
.sc-lbl{font-size:8px;letter-spacing:2px;text-transform:uppercase;color:var(--muted);margin-bottom:8px}
.sc-val{font-family:'Cormorant Garamond',serif;font-size:30px;font-weight:300;line-height:1;margin-bottom:4px}
.sc-sub{font-size:10px;color:var(--muted);font-weight:200}
.sc-b{display:inline-flex;align-items:center;padding:2px 7px;border-radius:20px;font-size:9px;margin-top:6px;font-weight:400}
.b-ok{background:var(--ok-bg);color:var(--ok);border:1px solid var(--ok-b)}
.b-low{background:var(--low-bg);color:var(--low);border:1px solid var(--low-b)}
.b-crit{background:var(--crit-bg);color:var(--crit);border:1px solid var(--crit-b)}
.b-info{background:rgba(184,154,94,.1);color:var(--gold-l);border:1px solid var(--border)}
.b-rose{background:var(--rose-bg);color:#c4899a;border:1px solid var(--rose-b)}
.b-blue{background:var(--blue-bg);color:#6fa3c0;border:1px solid var(--blue-b)}
.panel{background:var(--dark3);border:1px solid var(--border);margin-bottom:18px}
.panel:last-child{margin-bottom:0}
.ph2{padding:14px 18px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between;gap:10px;flex-wrap:wrap}
.pt{font-family:'Cormorant Garamond',serif;font-size:16px;font-weight:300;letter-spacing:.4px}
.ph2-r{display:flex;align-items:center;gap:8px;flex-wrap:wrap}
.tw{overflow-x:auto}
table{width:100%;border-collapse:collapse}
th{padding:9px 14px;text-align:left;font-size:8px;letter-spacing:2px;text-transform:uppercase;color:var(--muted);border-bottom:1px solid var(--border);font-weight:400;white-space:nowrap;background:rgba(0,0,0,.1)}
td{padding:11px 14px;font-size:12.5px;font-weight:300;border-bottom:1px solid var(--border2);vertical-align:middle}
tr:last-child td{border-bottom:none}
tr:hover td{background:rgba(184,154,94,.025)}
.pn{font-family:'Cormorant Garamond',serif;font-size:14px;font-weight:400}
.ps{font-size:9px;color:var(--muted);letter-spacing:.7px;margin-top:1px}
.stk{display:inline-flex;align-items:center;padding:3px 8px;font-size:9.5px;letter-spacing:.3px;font-weight:400}
.stk-ok{background:var(--ok-bg);color:var(--ok);border:1px solid var(--ok-b)}
.stk-low{background:var(--low-bg);color:var(--low);border:1px solid var(--low-b)}
.stk-crit{background:var(--crit-bg);color:var(--crit);border:1px solid var(--crit-b)}
.stk-out{background:rgba(100,80,80,.12);color:#c49a9a;border:1px solid rgba(150,100,100,.25)}
.pb-w{width:70px;height:3px;background:rgba(255,255,255,.07);border-radius:2px;overflow:hidden}
.pb{height:100%;border-radius:2px}
.pb-ok{background:linear-gradient(90deg,var(--ok),#7aca90)}
.pb-low{background:linear-gradient(90deg,var(--low),#e8a43c)}
.pb-crit{background:linear-gradient(90deg,var(--crit),#e05d4e)}
.btn{display:inline-flex;align-items:center;gap:6px;padding:7px 15px;font-family:'Jost',sans-serif;font-size:10.5px;letter-spacing:1px;text-transform:uppercase;font-weight:400;border:none;cursor:pointer;transition:all .18s}
.btn-gold{background:linear-gradient(135deg,var(--gold-d),var(--gold));color:var(--dark)}
.btn-gold:hover{background:linear-gradient(135deg,var(--gold),var(--gold-l));transform:translateY(-1px);box-shadow:0 4px 12px rgba(184,154,94,.22)}
.btn-ghost{background:transparent;color:var(--gold-l);border:1px solid var(--border)}
.btn-ghost:hover{border-color:var(--gold);background:rgba(184,154,94,.04)}
.btn-sm{padding:5px 11px;font-size:9.5px}
.ic-btn{padding:6px;background:transparent;border:1px solid var(--border);color:var(--muted);cursor:pointer;transition:all .18s;display:inline-flex;align-items:center;justify-content:center}
.ic-btn:hover{border-color:var(--gold);color:var(--gold-l)}
.ic-btn svg,.btn svg{width:12px;height:12px}
.ic-btn.del{color:var(--crit);border-color:var(--crit-b)}.ic-btn.del:hover{background:var(--crit-bg)}
.fg{display:flex;flex-direction:column;gap:6px}
.fg label{font-size:8.5px;letter-spacing:1.5px;text-transform:uppercase;color:var(--muted);font-weight:400}
.fc{background:rgba(0,0,0,.22);border:1px solid var(--border);color:var(--cream);padding:8px 11px;font-family:'Jost',sans-serif;font-size:12.5px;font-weight:300;outline:none;transition:border-color .18s;width:100%}
.fc:focus{border-color:var(--gold);background:rgba(184,154,94,.03)}
.fc option{background:var(--dark2)}
.fg2{display:grid;grid-template-columns:1fr 1fr;gap:14px}
.mo{position:fixed;inset:0;background:rgba(10,9,7,.82);z-index:200;display:none;align-items:center;justify-content:center;backdrop-filter:blur(3px)}
.mo.open{display:flex}
.modal{background:var(--dark2);border:1px solid var(--border);width:520px;max-width:95vw;max-height:90vh;overflow-y:auto;animation:mIn .22s ease}
@keyframes mIn{from{opacity:0;transform:scale(.97) translateY(8px)}to{opacity:1;transform:scale(1) translateY(0)}}
.mo-hd{padding:22px 22px 16px;border-bottom:1px solid var(--border);display:flex;align-items:flex-start;justify-content:space-between}
.mo-t{font-family:'Cormorant Garamond',serif;font-size:19px;font-weight:300}
.mo-x{background:none;border:none;color:var(--muted);cursor:pointer;font-size:17px;line-height:1;padding:2px;transition:color .15s}
.mo-x:hover{color:var(--cream)}
.mo-b{padding:20px 22px}
.mo-ft{padding:14px 22px;border-top:1px solid var(--border);display:flex;gap:9px;justify-content:flex-end}
.tc{position:fixed;top:64px;right:18px;z-index:300;display:flex;flex-direction:column;gap:8px}
.toast{background:var(--dark2);border:1px solid var(--border);padding:11px 15px;min-width:240px;display:flex;align-items:center;gap:9px;font-size:12px;font-weight:300;box-shadow:0 6px 20px rgba(0,0,0,.4);animation:tIn .22s ease}
.toast.out{animation:tOut .22s ease forwards}
@keyframes tIn{from{opacity:0;transform:translateX(12px)}to{opacity:1;transform:translateX(0)}}
@keyframes tOut{to{opacity:0;transform:translateX(12px)}}
.td{width:6px;height:6px;border-radius:50%;flex-shrink:0}
.t-ok .td{background:var(--ok)}.t-err .td{background:var(--crit)}.t-info .td{background:var(--gold)}
.fr{display:flex;gap:8px;align-items:center;flex-wrap:wrap}
.sr{position:relative;flex:1;min-width:140px}
.sr .fc{padding-left:30px}
.si{position:absolute;left:9px;top:50%;transform:translateY(-50%);color:var(--muted);width:13px;height:13px;pointer-events:none}
.ai{padding:10px 18px;display:flex;align-items:flex-start;gap:11px;border-bottom:1px solid var(--border2)}
.ai:last-child{border-bottom:none}
.ad{width:7px;height:7px;border-radius:50%;margin-top:4px;flex-shrink:0}
.at{font-size:12px;font-weight:300;margin-bottom:2px}
.am{font-size:10px;color:var(--muted);font-weight:200}
.tc2{display:grid;grid-template-columns:1fr 280px;gap:18px}
.mch{display:flex;align-items:flex-end;gap:3px;height:38px;padding:0 18px 14px}
.mb{flex:1;background:rgba(184,154,94,.18);min-width:7px;transition:background .2s;cursor:pointer}
.mb:hover,.mb.hi{background:rgba(184,154,94,.5)}
.empty{text-align:center;padding:40px 16px;color:var(--muted)}
.empty-icon{font-size:28px;opacity:.25;margin-bottom:8px}
.empty p{font-size:12px;font-weight:200}
/* GENDER TAG */
.gtag{display:inline-flex;align-items:center;padding:2px 7px;font-size:9.5px;font-weight:400;border-radius:2px;letter-spacing:.5px}
.gtag-w{background:var(--rose-bg);color:#c4899a;border:1px solid var(--rose-b)}
.gtag-m{background:var(--blue-bg);color:#6fa3c0;border:1px solid var(--blue-b)}
/* BRAND TAG */
.tag{display:inline-flex;align-items:center;padding:2px 7px;font-size:9.5px;font-weight:300;background:rgba(184,154,94,.08);color:var(--gold-l);border:1px solid var(--border)}
.mv-in{color:#6aaa7e;font-size:10.5px}.mv-out{color:#e05d4e;font-size:10.5px}.mv-adj{color:var(--gold-l);font-size:10.5px}
::-webkit-scrollbar{width:3px;height:3px}
::-webkit-scrollbar-track{background:transparent}
::-webkit-scrollbar-thumb{background:var(--border);border-radius:2px}
::-webkit-scrollbar-thumb:hover{background:var(--gold-d)}
/* TABS */
.tabs{display:flex;gap:0;border-bottom:1px solid var(--border);margin-bottom:0}
.tab{padding:10px 20px;font-size:11px;font-weight:300;letter-spacing:.8px;color:var(--muted);cursor:pointer;border-bottom:2px solid transparent;margin-bottom:-1px;transition:all .18s}
.tab:hover{color:var(--gold-l)}
.tab.act{color:var(--gold-l);border-bottom-color:var(--gold)}
@media(max-width:1060px){.stats{grid-template-columns:repeat(3,1fr)}.tc2{grid-template-columns:1fr}}
@media(max-width:700px){.stats{grid-template-columns:repeat(2,1fr)}}
</style>
</head>
<body>

<!-- SIDEBAR -->
<nav class="sb">
  <div class="sb-brand">
    <div class="n">Yodge</div>
    <div class="s">Perfume House</div>
  </div>
  <div>
    <div class="nav-lbl">Overview</div>
    <div class="nv act" data-page="dash">
      <svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M4 6a2 2 0 012-2h4a2 2 0 012 2v4a2 2 0 01-2 2H6a2 2 0 01-2-2V6zm10 0a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2V6zM4 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2v-2zm10-2a2 2 0 00-2 2v4a2 2 0 002 2h4a2 2 0 002-2v-4a2 2 0 00-2-2h-4z"/></svg>Dashboard
    </div>
    <div class="nav-lbl">Inventory</div>
    <div class="nv" data-page="products">
      <svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10"/></svg>Products
    </div>
    <div class="nv" data-page="stock">
      <svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"/></svg>Stock Monitor
    </div>
    <div class="nv" data-page="movements">
      <svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M8 7h12m0 0l-4-4m4 4l-4 4m0 6H4m0 0l4 4m-4-4l4-4"/></svg>Movements
    </div>
    <div class="nav-lbl">Management</div>
    <div class="nv" data-page="suppliers">
      <svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-2 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"/></svg>Suppliers
    </div>
  </div>
  <div class="sb-ft">Yodge Perfume House &copy; 2025<br>Inventory System v1.0</div>
</nav>

<!-- MAIN -->
<div class="main">
  <div class="topbar">
    <div class="tb-l">Yodge <em>Inventory</em></div>
    <div class="tb-r">
      <span class="tb-date" id="topdate"></span>
      <div class="av">YP</div>
    </div>
  </div>
  <div class="content">

    <!-- DASHBOARD -->
    <div class="page act" id="p-dash">
      <div class="ph"><h1>Good morning, <em>Admin</em></h1><div class="gr"></div><p>Inventory snapshot — Yodge Perfume House · 53 SKUs across Women's &amp; Men's</p></div>
      <div class="stats" id="d-stats"></div>
      <div class="tc2">
        <div>
          <div class="panel">
            <div class="ph2"><span class="pt">Low Stock Alerts</span><span class="sc-b b-low" id="d-lowbadge"></span></div>
            <div class="tw"><table><thead><tr><th>Product</th><th>Gender</th><th>Brand</th><th>Qty</th><th>Min</th><th>Status</th></tr></thead><tbody id="d-alerts"></tbody></table></div>
          </div>
          <div class="panel">
            <div class="ph2"><span class="pt">Recent Movements</span></div>
            <div id="d-mv"></div>
          </div>
        </div>
        <div>
          <div class="panel">
            <div class="ph2"><span class="pt">Gender Split</span></div>
            <div id="d-gender" style="padding:14px 18px"></div>
          </div>
          <div class="panel">
            <div class="ph2"><span class="pt">Weekly Activity</span></div>
            <div id="d-chart" class="mch"></div>
            <div style="font-size:9.5px;color:var(--muted);padding:0 18px 12px;font-weight:200">Units moved last 7 days</div>
          </div>
          <div class="panel">
            <div class="ph2"><span class="pt">Top Brands</span></div>
            <div id="d-brands" style="padding:14px 18px"></div>
          </div>
        </div>
      </div>
    </div>

    <!-- PRODUCTS -->
    <div class="page" id="p-products">
      <div class="ph"><h1>Product <em>Catalogue</em></h1><div class="gr"></div><p>All 53 fragrance SKUs — Women's and Men's collections</p></div>
      <div class="panel">
        <div class="ph2">
          <span class="pt">All Products <span style="font-size:12px;color:var(--muted);font-family:'Jost',sans-serif;font-weight:200" id="p-count"></span></span>
          <div class="ph2-r">
            <div class="sr"><svg class="si" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-4.35-4.35M17 11A6 6 0 115 11a6 6 0 0112 0z"/></svg><input class="fc" id="p-search" placeholder="Search name, SKU, brand…" oninput="rProds()" style="padding-left:28px;width:200px"/></div>
            <select class="fc" id="p-gfilter" onchange="rProds()" style="width:110px"><option value="">All Genders</option><option value="W">Women's</option><option value="M">Men's</option></select>
            <select class="fc" id="p-sfilter" onchange="rProds()" style="width:120px"><option value="">All Statuses</option><option value="ok">In Stock</option><option value="low">Low Stock</option><option value="crit">Critical</option><option value="out">Out of Stock</option></select>
            <button class="btn btn-gold" onclick="openM('addProd')"><svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M12 4v16m8-8H4"/></svg>Add</button>
          </div>
        </div>
        <div class="tw"><table>
          <thead><tr><th>SKU</th><th>Product Name</th><th>Brand</th><th>Gender</th><th>Qty</th><th>Status</th><th>Actions</th></tr></thead>
          <tbody id="p-tbody"></tbody>
        </table></div>
      </div>
    </div>

    <!-- STOCK MONITOR -->
    <div class="page" id="p-stock">
      <div class="ph"><h1>Stock <em>Monitor</em></h1><div class="gr"></div><p>Real-time stock levels — Women's &amp; Men's collections</p></div>
      <!-- Tabs -->
      <div class="panel">
        <div class="ph2">
          <div class="tabs" style="border:none;margin:0">
            <div class="tab act" id="stab-all" onclick="setStockTab('all')">All (53)</div>
            <div class="tab" id="stab-W" onclick="setStockTab('W')">Women's (29)</div>
            <div class="tab" id="stab-M" onclick="setStockTab('M')">Men's (24)</div>
          </div>
          <div class="ph2-r">
            <select class="fc" id="s-filter" onchange="rStock()" style="width:130px"><option value="">All Statuses</option><option value="ok">In Stock</option><option value="low">Low Stock</option><option value="crit">Critical</option><option value="out">Out of Stock</option></select>
            <button class="btn btn-ghost btn-sm" onclick="openM('adjStock')">Adjust Stock</button>
          </div>
        </div>
        <div class="tw"><table>
          <thead><tr><th>SKU</th><th>Product</th><th>Brand</th><th>Gender</th><th>Qty</th><th>Min Qty</th><th>Level</th><th>Status</th></tr></thead>
          <tbody id="s-tbody"></tbody>
        </table></div>
      </div>
    </div>

    <!-- MOVEMENTS -->
    <div class="page" id="p-movements">
      <div class="ph"><h1>Stock <em>Movements</em></h1><div class="gr"></div><p>Complete log of all inbound, outbound and adjustments</p></div>
      <div class="panel">
        <div class="ph2">
          <span class="pt">Movement Log</span>
          <div class="ph2-r">
            <select class="fc" id="mv-filter" onchange="rMv()" style="width:120px"><option value="">All Types</option><option value="IN">Stock In</option><option value="OUT">Stock Out</option><option value="ADJ">Adjustment</option></select>
            <button class="btn btn-gold" onclick="openM('addMv')"><svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M12 4v16m8-8H4"/></svg>Record</button>
          </div>
        </div>
        <div class="tw"><table>
          <thead><tr><th>Date</th><th>Type</th><th>SKU</th><th>Product</th><th>Gender</th><th>Qty</th><th>Notes</th><th>By</th></tr></thead>
          <tbody id="mv-tbody"></tbody>
        </table></div>
      </div>
    </div>

    <!-- SUPPLIERS -->
    <div class="page" id="p-suppliers">
      <div class="ph"><h1>Supplier <em>Directory</em></h1><div class="gr"></div><p>Fragrance and materials suppliers</p></div>
      <div class="panel">
        <div class="ph2"><span class="pt">Suppliers</span><button class="btn btn-gold" onclick="openM('addSup')"><svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M12 4v16m8-8H4"/></svg>Add Supplier</button></div>
        <div class="tw"><table><thead><tr><th>Name</th><th>Contact</th><th>Email</th><th>Phone</th><th>Category</th><th>Actions</th></tr></thead><tbody id="sup-tbody"></tbody></table></div>
      </div>
    </div>

  </div>
</div>

<!-- MODALS -->
<div class="mo" id="mo-addProd">
  <div class="modal">
    <div class="mo-hd"><span class="mo-t">Add New Product</span><button class="mo-x" onclick="closeM('addProd')">✕</button></div>
    <div class="mo-b">
      <div class="fg2" style="gap:14px">
        <div class="fg"><label>SKU</label><input class="fc" id="ap-sku" placeholder="e.g. CRAT"/></div>
        <div class="fg"><label>Product Name</label><input class="fc" id="ap-name" placeholder="e.g. Baccarat Rouge 540"/></div>
        <div class="fg"><label>Brand</label><input class="fc" id="ap-brand" placeholder="e.g. Maison Francis Kurkdjian"/></div>
        <div class="fg"><label>Gender</label><select class="fc" id="ap-gender"><option value="W">Women's</option><option value="M">Men's</option></select></div>
        <div class="fg"><label>Cost Price (₱)</label><input class="fc" id="ap-cost" type="number" placeholder="0"/></div>
        <div class="fg"><label>Retail Price (₱)</label><input class="fc" id="ap-price" type="number" placeholder="0"/></div>
        <div class="fg"><label>Min Stock (Threshold)</label><input class="fc" id="ap-thresh" type="number" placeholder="10"/></div>
        <div class="fg"><label>Initial Stock Qty</label><input class="fc" id="ap-qty" type="number" placeholder="0"/></div>
      </div>
    </div>
    <div class="mo-ft"><button class="btn btn-ghost" onclick="closeM('addProd')">Cancel</button><button class="btn btn-gold" onclick="addProd()">Save Product</button></div>
  </div>
</div>

<div class="mo" id="mo-editProd">
  <div class="modal">
    <div class="mo-hd"><span class="mo-t">Edit Product</span><button class="mo-x" onclick="closeM('editProd')">✕</button></div>
    <div class="mo-b">
      <input type="hidden" id="ep-id"/>
      <div class="fg2" style="gap:14px">
        <div class="fg"><label>SKU</label><input class="fc" id="ep-sku"/></div>
        <div class="fg"><label>Product Name</label><input class="fc" id="ep-name"/></div>
        <div class="fg"><label>Brand</label><input class="fc" id="ep-brand"/></div>
        <div class="fg"><label>Gender</label><select class="fc" id="ep-gender"><option value="W">Women's</option><option value="M">Men's</option></select></div>
        <div class="fg"><label>Cost Price (₱)</label><input class="fc" id="ep-cost" type="number"/></div>
        <div class="fg"><label>Retail Price (₱)</label><input class="fc" id="ep-price" type="number"/></div>
        <div class="fg"><label>Min Stock (Threshold)</label><input class="fc" id="ep-thresh" type="number"/></div>
        <div class="fg"><label>Current Stock Qty</label><input class="fc" id="ep-qty" type="number"/></div>
      </div>
    </div>
    <div class="mo-ft"><button class="btn btn-ghost" onclick="closeM('editProd')">Cancel</button><button class="btn btn-gold" onclick="saveEditProd()">Save Changes</button></div>
  </div>
</div>

<div class="mo" id="mo-adjStock">
  <div class="modal">
    <div class="mo-hd"><span class="mo-t">Adjust Stock</span><button class="mo-x" onclick="closeM('adjStock')">✕</button></div>
    <div class="mo-b">
      <div style="display:grid;gap:14px">
        <div class="fg"><label>Product</label><select class="fc" id="adj-prod"></select></div>
        <div class="fg2" style="gap:14px">
          <div class="fg"><label>Type</label><select class="fc" id="adj-type"><option value="IN">Stock In (Add)</option><option value="OUT">Stock Out (Deduct)</option><option value="ADJ">Set Exact Qty</option></select></div>
          <div class="fg"><label>Quantity</label><input class="fc" id="adj-qty" type="number" placeholder="0"/></div>
        </div>
        <div class="fg"><label>Notes / Reason</label><input class="fc" id="adj-note" placeholder="e.g. Restocked from supplier"/></div>
      </div>
    </div>
    <div class="mo-ft"><button class="btn btn-ghost" onclick="closeM('adjStock')">Cancel</button><button class="btn btn-gold" onclick="doAdj()">Apply</button></div>
  </div>
</div>

<div class="mo" id="mo-addMv">
  <div class="modal">
    <div class="mo-hd"><span class="mo-t">Record Movement</span><button class="mo-x" onclick="closeM('addMv')">✕</button></div>
    <div class="mo-b">
      <div style="display:grid;gap:14px">
        <div class="fg"><label>Product</label><select class="fc" id="mv-prod"></select></div>
        <div class="fg2" style="gap:14px">
          <div class="fg"><label>Type</label><select class="fc" id="mv-type"><option value="IN">Stock In</option><option value="OUT">Stock Out</option><option value="ADJ">Adjustment</option></select></div>
          <div class="fg"><label>Quantity</label><input class="fc" id="mv-qty" type="number" placeholder="0"/></div>
        </div>
        <div class="fg"><label>Notes</label><input class="fc" id="mv-note" placeholder="Optional…"/></div>
      </div>
    </div>
    <div class="mo-ft"><button class="btn btn-ghost" onclick="closeM('addMv')">Cancel</button><button class="btn btn-gold" onclick="addMv()">Record</button></div>
  </div>
</div>

<div class="mo" id="mo-addSup">
  <div class="modal">
    <div class="mo-hd"><span class="mo-t">Add Supplier</span><button class="mo-x" onclick="closeM('addSup')">✕</button></div>
    <div class="mo-b">
      <div class="fg2" style="gap:14px">
        <div class="fg" style="grid-column:span 2"><label>Supplier Name</label><input class="fc" id="sup-name" placeholder="Supplier company name"/></div>
        <div class="fg"><label>Contact Person</label><input class="fc" id="sup-contact" placeholder="Full name"/></div>
        <div class="fg"><label>Category</label><select class="fc" id="sup-cat"><option>Fragrance Oils</option><option>Bottles & Packaging</option><option>Raw Materials</option><option>Mixed Supplier</option></select></div>
        <div class="fg"><label>Email</label><input class="fc" id="sup-email" placeholder="email@example.com"/></div>
        <div class="fg"><label>Phone</label><input class="fc" id="sup-phone" placeholder="+63 9XX XXX XXXX"/></div>
      </div>
    </div>
    <div class="mo-ft"><button class="btn btn-ghost" onclick="closeM('addSup')">Cancel</button><button class="btn btn-gold" onclick="addSup()">Save Supplier</button></div>
  </div>
</div>

<div class="tc" id="toasts"></div>

<script>
// ═══════════════════════════════════════
//  REAL PRODUCT DATA FROM YODGE CATALOGUE
// ═══════════════════════════════════════
let products = [
  // ── WOMEN'S (29) ──────────────────────────────────────────
  {id:1, sku:'CRAT', name:"Baccarat Rouge 540",       brand:"Maison Francis Kurkdjian", gender:'W', cost:620, price:1800, thresh:8,  qty:15},
  {id:2, sku:'BDEL', name:"Be Delicious",              brand:"DNKY",                     gender:'W', cost:380, price:980,  thresh:10, qty:22},
  {id:3, sku:'SHEL', name:"Bombshell",                 brand:"Victoria's Secret",         gender:'W', cost:290, price:750,  thresh:10, qty:8},
  {id:4, sku:'BRYT', name:"Bright Crystal",            brand:"Versace",                   gender:'W', cost:420, price:1100, thresh:8,  qty:5},
  {id:5, sku:'CHNZ', name:"Chance",                    brand:"Chanel",                    gender:'W', cost:580, price:1600, thresh:8,  qty:18},
  {id:6, sku:'CLWD', name:"Cloud",                     brand:"Ariana Grande",             gender:'W', cost:320, price:850,  thresh:10, qty:30},
  {id:7, sku:'CBER', name:"Cucumber Melon",            brand:"Bath & Body Works",         gender:'W', cost:210, price:550,  thresh:12, qty:40},
  {id:8, sku:'ECLT', name:"Eclat D'Arpege",            brand:"Lanvin",                    gender:'W', cost:350, price:920,  thresh:8,  qty:12},
  {id:9, sku:'PEAR', name:"English Pear & Freesia",    brand:"Jo Malone",                 gender:'W', cost:680, price:1900, thresh:6,  qty:7},
  {id:10,sku:'ENVI', name:"Envy Me",                   brand:"Gucci",                     gender:'W', cost:460, price:1250, thresh:8,  qty:3},
  {id:11,sku:'FTSY', name:"Fantasy",                   brand:"Britney Spears",            gender:'W', cost:280, price:720,  thresh:10, qty:25},
  {id:12,sku:'GRIN', name:"Green Tea",                 brand:"Elizabeth Arden",           gender:'W', cost:300, price:780,  thresh:10, qty:19},
  {id:13,sku:'RUSH', name:"Gucci Rush",                brand:"Gucci",                     gender:'W', cost:450, price:1200, thresh:8,  qty:11},
  {id:14,sku:'HPWN', name:"Happy",                     brand:"Guess",                     gender:'W', cost:310, price:800,  thresh:10, qty:28},
  {id:15,sku:'ICAN', name:"Incanto Shine",             brand:"Clinique",                  gender:'W', cost:390, price:1000, thresh:8,  qty:14},
  {id:16,sku:'LBWN', name:"Light Blue",                brand:"Salvatore Ferragamo",       gender:'W', cost:480, price:1300, thresh:8,  qty:9},
  {id:17,sku:'MIAW', name:"Meow",                      brand:"Dolce & Gabbana",           gender:'W', cost:420, price:1100, thresh:8,  qty:6},
  {id:18,sku:'DYOR', name:"Miss Dior",                 brand:"Katy Perry",                gender:'W', cost:390, price:1000, thresh:8,  qty:20},
  {id:19,sku:'MOON', name:"Moonsparkle",               brand:"Escada",                    gender:'W', cost:340, price:880,  thresh:10, qty:16},
  {id:20,sku:'NECT', name:"Nectarine Blossom & Honey", brand:"Jo Malone",                 gender:'W', cost:680, price:1900, thresh:6,  qty:4},
  {id:21,sku:'OMNI', name:"Omnia Amethyste",           brand:"Bvlgari",                   gender:'W', cost:450, price:1200, thresh:8,  qty:10},
  {id:22,sku:'PRIS', name:"Paris Hilton",              brand:"Paris Hilton",              gender:'W', cost:270, price:700,  thresh:10, qty:33},
  {id:23,sku:'PURE', name:"Pure Seduction",            brand:"Victoria's Secret",         gender:'W', cost:290, price:750,  thresh:10, qty:27},
  {id:24,sku:'GMEZ', name:"Selena Gomez",              brand:"Selena Gomez",              gender:'W', cost:360, price:950,  thresh:8,  qty:0},
  {id:25,sku:'SWIT', name:"Sweet Pea",                 brand:"Bath & Body Works",         gender:'W', cost:210, price:550,  thresh:12, qty:45},
  {id:26,sku:'TGRL', name:"Tommy Girl",                brand:"Tommy Hilfiger",            gender:'W', cost:380, price:980,  thresh:8,  qty:13},
  {id:27,sku:'TPNK', name:"Touch of Pink",             brand:"Lacoste",                   gender:'W', cost:400, price:1050, thresh:8,  qty:2},
  {id:28,sku:'LACE', name:"Vanilla Lace",              brand:"Victoria's Secret",         gender:'W', cost:290, price:750,  thresh:10, qty:38},
  {id:29,sku:'WKWN', name:"Weekend",                   brand:"Burberry",                  gender:'W', cost:420, price:1100, thresh:8,  qty:17},
  // ── MEN'S (24) ────────────────────────────────────────────
  {id:30,sku:'AGIO', name:"Aqua Di Gio",               brand:"Giorgio Armani",            gender:'M', cost:480, price:1300, thresh:8,  qty:20},
  {id:31,sku:'ATUS', name:"Aventus",                   brand:"Creed",                     gender:'M', cost:780, price:2200, thresh:6,  qty:5},
  {id:32,sku:'COLD', name:"Benetton Cold",             brand:"Benetton",                  gender:'M', cost:250, price:650,  thresh:10, qty:32},
  {id:33,sku:'AMAR', name:"Bvlgari Aqua Amara",        brand:"Bvlgari",                   gender:'M', cost:460, price:1250, thresh:8,  qty:11},
  {id:34,sku:'XTRM', name:"Bvlgari Extreme",           brand:"Bvlgari",                   gender:'M', cost:450, price:1200, thresh:8,  qty:7},
  {id:35,sku:'CKAY', name:"Ck One",                    brand:"Calvin Klein",              gender:'M', cost:350, price:900,  thresh:10, qty:24},
  {id:36,sku:'COOL', name:"Coolwater",                 brand:"Davidoff",                  gender:'M', cost:320, price:820,  thresh:10, qty:18},
  {id:37,sku:'DKAR', name:"Drakkar Intense",           brand:"Guy Laroche",               gender:'M', cost:290, price:750,  thresh:10, qty:9},
  {id:38,sku:'ROSS', name:"Eros",                      brand:"Versace",                   gender:'M', cost:480, price:1300, thresh:8,  qty:14},
  {id:39,sku:'ETER', name:"Eternity Aqua",             brand:"Calvin Klein",              gender:'M', cost:380, price:980,  thresh:8,  qty:3},
  {id:40,sku:'FRHT', name:"Fahrenheit",                brand:"Christian Dior",            gender:'M', cost:500, price:1380, thresh:8,  qty:16},
  {id:41,sku:'HPMN', name:"Happy",                     brand:"Clinique",                  gender:'M', cost:390, price:1000, thresh:8,  qty:22},
  {id:42,sku:'HBOS', name:"Hugo Boss",                 brand:"Hugo Boss",                 gender:'M', cost:410, price:1080, thresh:8,  qty:19},
  {id:43,sku:'ISMK', name:"Issey Miyake",              brand:"Issey Miyake",              gender:'M', cost:460, price:1250, thresh:8,  qty:8},
  {id:44,sku:'LBLK', name:"Lacoste Black",             brand:"Lacoste",                   gender:'M', cost:400, price:1050, thresh:8,  qty:0},
  {id:45,sku:'LRED', name:"Lacoste Red",               brand:"Lacoste",                   gender:'M', cost:400, price:1050, thresh:8,  qty:6},
  {id:46,sku:'LWHT', name:"Lacoste White",             brand:"Lacoste",                   gender:'M', cost:400, price:1050, thresh:8,  qty:10},
  {id:47,sku:'LGND', name:"Legend",                    brand:"Mont Blanc",                gender:'M', cost:430, price:1150, thresh:8,  qty:15},
  {id:48,sku:'LBMN', name:"Light Blue",                brand:"Dolce & Gabbana",           gender:'M', cost:480, price:1300, thresh:8,  qty:12},
  {id:49,sku:'PERI', name:"Perry Ellis",               brand:"Perry Ellis",               gender:'M', cost:300, price:780,  thresh:10, qty:26},
  {id:50,sku:'PBLU', name:"Polo Blue",                 brand:"Ralph Lauren",              gender:'M', cost:420, price:1100, thresh:8,  qty:4},
  {id:51,sku:'SPRT', name:"Polo Sport",                brand:"Ralph Lauren",              gender:'M', cost:380, price:980,  thresh:8,  qty:28},
  {id:52,sku:'SWIS', name:"Swiss Army",                brand:"Victorinox",                gender:'M', cost:280, price:720,  thresh:10, qty:35},
  {id:53,sku:'WKMN', name:"Weekend",                   brand:"Burberry",                  gender:'M', cost:420, price:1100, thresh:8,  qty:1},
];

let suppliers = [
  {id:1,name:'Premium Scents PH',contact:'Ana Reyes',email:'ana@premiumscents.ph',phone:'+63 917 555 0011',cat:'Fragrance Oils'},
  {id:2,name:'Luxe Bottles & Co.',contact:'Marco Santos',email:'marco@luxebottles.ph',phone:'+63 918 555 0022',cat:'Bottles & Packaging'},
  {id:3,name:'Scent Essence PH',contact:'Diego Cruz',email:'diego@scentessence.ph',phone:'+63 919 555 0033',cat:'Raw Materials'},
];

let movements = [
  {id:1, date:'2025-03-10', type:'IN',  prodId:1,  qty:10, note:'Restocked — supplier delivery', by:'Admin'},
  {id:2, date:'2025-03-10', type:'OUT', prodId:24, qty:3,  note:'Sold — walk-in customer',        by:'Admin'},
  {id:3, date:'2025-03-09', type:'IN',  prodId:30, qty:15, note:'Delivery from Premium Scents',  by:'Admin'},
  {id:4, date:'2025-03-08', type:'OUT', prodId:10, qty:2,  note:'Online order fulfillment',       by:'Admin'},
  {id:5, date:'2025-03-07', type:'ADJ', prodId:27, qty:2,  note:'Inventory count correction',    by:'Admin'},
  {id:6, date:'2025-03-07', type:'IN',  prodId:31, qty:5,  note:'New shipment — Aventus',        by:'Admin'},
  {id:7, date:'2025-03-06', type:'OUT', prodId:4,  qty:3,  note:'Event / pop-up sale',           by:'Admin'},
  {id:8, date:'2025-03-06', type:'OUT', prodId:44, qty:2,  note:'Last stock sold',               by:'Admin'},
];

let npId=54, nsId=4, nmId=9;
let stockTabFilter = 'all';

// ── UTILS ─────────────────────────────────────────────────────
const fmt = n => '₱'+Number(n).toLocaleString('en-PH',{minimumFractionDigits:2});
const today = () => new Date().toISOString().slice(0,10);

function sStatus(p) {
  if(p.qty===0) return {cls:'stk-out', lbl:'Out of Stock'};
  if(p.qty<=Math.floor(p.thresh*.5)) return {cls:'stk-crit', lbl:'Critical'};
  if(p.qty<=p.thresh) return {cls:'stk-low', lbl:'Low Stock'};
  return {cls:'stk-ok', lbl:'In Stock'};
}
function pbCls(p){const s=sStatus(p);return s.lbl==='In Stock'?'pb-ok':s.lbl==='Low Stock'?'pb-low':'pb-crit';}
function pbPct(p){const mx=Math.max(p.thresh*3,p.qty,1);return Math.min(100,Math.round(p.qty/mx*100));}
function gTag(g){return g==='W'?'<span class="gtag gtag-w">♀ Women\'s</span>':'<span class="gtag gtag-m">♂ Men\'s</span>';}

function toast(msg,type='info'){
  const w=document.getElementById('toasts');
  const el=document.createElement('div');
  el.className=`toast t-${type}`;
  el.innerHTML=`<div class="td"></div><span>${msg}</span>`;
  w.appendChild(el);
  setTimeout(()=>{el.classList.add('out');setTimeout(()=>el.remove(),280);},3000);
}

function openM(id){
  if(id==='adjStock'||id==='addMv') fillProdSel(id==='adjStock'?'adj-prod':'mv-prod');
  document.getElementById('mo-'+id).classList.add('open');
}
function closeM(id){document.getElementById('mo-'+id).classList.remove('open');}
document.querySelectorAll('.mo').forEach(m=>m.addEventListener('click',e=>{if(e.target===m)m.classList.remove('open');}));

function fillProdSel(id){
  document.getElementById(id).innerHTML=products.map(p=>`<option value="${p.id}">[${p.sku}] ${p.name} (${p.gender==='W'?"W":"M"})</option>`).join('');
}

function applyStFilter(list){
  const f=document.getElementById('s-filter').value;
  if(!f) return list;
  return list.filter(p=>{
    const s=sStatus(p);
    if(f==='ok') return s.lbl==='In Stock';
    if(f==='low') return s.lbl==='Low Stock';
    if(f==='crit') return s.lbl==='Critical';
    if(f==='out') return s.lbl==='Out of Stock';
    return true;
  });
}

// ── NAVIGATION ────────────────────────────────────────────────
document.querySelectorAll('.nv').forEach(a=>{
  a.addEventListener('click',()=>{
    document.querySelectorAll('.nv').forEach(x=>x.classList.remove('act'));
    document.querySelectorAll('.page').forEach(x=>x.classList.remove('act'));
    a.classList.add('act');
    const pg=a.dataset.page;
    document.getElementById('p-'+pg).classList.add('act');
    ({dash:rDash,products:rProds,stock:rStock,movements:rMv,suppliers:rSup})[pg]?.();
  });
});

// ── DASHBOARD ─────────────────────────────────────────────────
function rDash(){
  const total=products.length;
  const wCount=products.filter(p=>p.gender==='W').length;
  const mCount=products.filter(p=>p.gender==='M').length;
  const outC=products.filter(p=>p.qty===0).length;
  const lowC=products.filter(p=>p.qty>0&&p.qty<=p.thresh).length;
  const totalQ=products.reduce((a,p)=>a+p.qty,0);

  document.getElementById('d-stats').innerHTML=`
    <div class="sc"><div class="sc-lbl">Total SKUs</div><div class="sc-val">${total}</div><div class="sc-sub">All fragrances</div><span class="sc-b b-info">${wCount}W · ${mCount}M</span></div>
    <div class="sc"><div class="sc-lbl">Women's</div><div class="sc-val" style="color:#c4899a">${wCount}</div><div class="sc-sub">Feminine collection</div><span class="sc-b b-rose">${products.filter(p=>p.gender==='W'&&p.qty>0).length} in stock</span></div>
    <div class="sc"><div class="sc-lbl">Men's</div><div class="sc-val" style="color:#6fa3c0">${mCount}</div><div class="sc-sub">Masculine collection</div><span class="sc-b b-blue">${products.filter(p=>p.gender==='M'&&p.qty>0).length} in stock</span></div>
    <div class="sc"><div class="sc-lbl">Low Stock</div><div class="sc-val" style="color:var(--low)">${lowC}</div><div class="sc-sub">Need restocking</div>${lowC?'<span class="sc-b b-low">Action needed</span>':''}</div>
    <div class="sc"><div class="sc-lbl">Out of Stock</div><div class="sc-val" style="color:var(--crit)">${outC}</div><div class="sc-sub">Zero inventory</div>${outC?'<span class="sc-b b-crit">Urgent</span>':''}</div>
  `;

  const alerts=products.filter(p=>p.qty<=p.thresh).sort((a,b)=>a.qty-b.qty);
  document.getElementById('d-lowbadge').textContent=alerts.length+' items';
  const ab=document.getElementById('d-alerts');
  ab.innerHTML=alerts.length?alerts.map(p=>{
    const s=sStatus(p);
    return `<tr><td><div class="pn">${p.name}</div><div class="ps">${p.sku}</div></td><td>${gTag(p.gender)}</td><td style="font-size:10.5px;color:var(--muted)">${p.brand}</td><td style="font-family:'Cormorant Garamond',serif;font-size:18px">${p.qty}</td><td style="color:var(--muted)">${p.thresh}</td><td><span class="stk ${s.cls}">${s.lbl}</span></td></tr>`;
  }).join(''):'<tr><td colspan="6" style="text-align:center;padding:20px;color:var(--muted);font-size:11px">All stock levels are healthy ✓</td></tr>';

  document.getElementById('d-mv').innerHTML=movements.slice(-5).reverse().map(m=>{
    const p=products.find(x=>x.id===m.prodId);
    const dot=m.type==='IN'?'#5a9e6f':m.type==='OUT'?'#c0402e':'#b89a5e';
    return `<div class="ai"><div class="ad" style="background:${dot}"></div><div><div class="at">${m.type==='IN'?'+':m.type==='OUT'?'-':'±'}${m.qty} × ${p?p.name:'Unknown'}</div><div class="am">${m.date} · ${m.note}</div></div></div>`;
  }).join('');

  // Gender split
  const wQty=products.filter(p=>p.gender==='W').reduce((a,p)=>a+p.qty,0);
  const mQty=products.filter(p=>p.gender==='M').reduce((a,p)=>a+p.qty,0);
  const totalQ2=wQty+mQty||1;
  document.getElementById('d-gender').innerHTML=`
    <div style="margin-bottom:14px">
      <div style="display:flex;justify-content:space-between;margin-bottom:4px"><span style="font-size:11px;color:#c4899a">♀ Women's</span><span style="font-size:10px;color:var(--muted)">${wQty} units · ${wCount} SKUs</span></div>
      <div style="height:4px;background:rgba(255,255,255,.06);border-radius:2px"><div style="height:100%;width:${Math.round(wQty/totalQ2*100)}%;background:linear-gradient(90deg,var(--rose),#c4899a);border-radius:2px"></div></div>
    </div>
    <div>
      <div style="display:flex;justify-content:space-between;margin-bottom:4px"><span style="font-size:11px;color:#6fa3c0">♂ Men's</span><span style="font-size:10px;color:var(--muted)">${mQty} units · ${mCount} SKUs</span></div>
      <div style="height:4px;background:rgba(255,255,255,.06);border-radius:2px"><div style="height:100%;width:${Math.round(mQty/totalQ2*100)}%;background:linear-gradient(90deg,var(--blue),#6fa3c0);border-radius:2px"></div></div>
    </div>
    <div style="margin-top:12px;padding-top:12px;border-top:1px solid var(--border2);display:flex;justify-content:space-between">
      <span style="font-size:10px;color:var(--muted)">Total in warehouse</span>
      <span style="font-family:'Cormorant Garamond',serif;font-size:16px">${wQty+mQty} units</span>
    </div>`;

  // Weekly chart
  const wd=[18,12,25,20,8,22,30];
  const wm=Math.max(...wd);
  document.getElementById('d-chart').innerHTML=wd.map((v,i)=>`<div class="mb ${i===6?'hi':''}" style="height:${Math.round(v/wm*100)}%" title="${v} units moved"></div>`).join('');

  // Top brands
  const brandCounts={};
  products.forEach(p=>{brandCounts[p.brand]=(brandCounts[p.brand]||0)+p.qty;});
  const topBrands=Object.entries(brandCounts).sort((a,b)=>b[1]-a[1]).slice(0,5);
  const maxB=topBrands[0]?.[1]||1;
  document.getElementById('d-brands').innerHTML=topBrands.map(([b,q])=>`
    <div style="margin-bottom:10px">
      <div style="display:flex;justify-content:space-between;margin-bottom:3px"><span style="font-size:10.5px;font-weight:300">${b}</span><span style="font-size:9.5px;color:var(--muted)">${q}</span></div>
      <div style="height:2px;background:rgba(255,255,255,.06);border-radius:2px"><div style="height:100%;width:${Math.round(q/maxB*100)}%;background:linear-gradient(90deg,var(--gold-d),var(--gold-l));border-radius:2px"></div></div>
    </div>`).join('');
}

// ── PRODUCTS ──────────────────────────────────────────────────
function rProds(){
  const q=(document.getElementById('p-search').value||'').toLowerCase();
  const gf=document.getElementById('p-gfilter').value;
  const sf=document.getElementById('p-sfilter').value;
  let list=products.filter(p=>{
    const mq=!q||p.name.toLowerCase().includes(q)||p.sku.toLowerCase().includes(q)||p.brand.toLowerCase().includes(q);
    const mg=!gf||p.gender===gf;
    const s=sStatus(p);
    const ms=!sf||(sf==='ok'&&s.lbl==='In Stock')||(sf==='low'&&s.lbl==='Low Stock')||(sf==='crit'&&s.lbl==='Critical')||(sf==='out'&&s.lbl==='Out of Stock');
    return mq&&mg&&ms;
  });
  document.getElementById('p-count').textContent=`— ${list.length} shown`;
  const tb=document.getElementById('p-tbody');
  if(!list.length){tb.innerHTML=`<tr><td colspan="7"><div class="empty"><div class="empty-icon">◈</div><p>No products match your filters</p></div></td></tr>`;return;}
  tb.innerHTML=list.map(p=>{
    const s=sStatus(p);
    return `<tr>
      <td style="font-size:9px;letter-spacing:.8px;color:var(--muted);white-space:nowrap">${p.sku}</td>
      <td><div class="pn">${p.name}</div></td>
      <td style="font-size:11px;color:var(--muted);white-space:nowrap">${p.brand}</td>
      <td>${gTag(p.gender)}</td>
      <td style="font-family:'Cormorant Garamond',serif;font-size:18px;font-weight:300">${p.qty}</td>
      <td><span class="stk ${s.cls}">${s.lbl}</span></td>
      <td><div style="display:flex;gap:5px">
        <button class="ic-btn" onclick="openEdit(${p.id})" title="Edit"><svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"/></svg></button>
        <button class="ic-btn del" onclick="delProd(${p.id})" title="Delete"><svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"/></svg></button>
      </div></td>
    </tr>`;
  }).join('');
}

function addProd(){
  const sku=document.getElementById('ap-sku').value.trim().toUpperCase();
  const name=document.getElementById('ap-name').value.trim();
  if(!sku||!name){toast('SKU and Name are required','err');return;}
  if(products.find(p=>p.sku===sku)){toast('SKU already exists','err');return;}
  const p={id:npId++,sku,name,brand:document.getElementById('ap-brand').value.trim()||'—',gender:document.getElementById('ap-gender').value,cost:+document.getElementById('ap-cost').value||0,price:+document.getElementById('ap-price').value||0,thresh:+document.getElementById('ap-thresh').value||10,qty:+document.getElementById('ap-qty').value||0};
  products.push(p);
  if(p.qty>0) movements.push({id:nmId++,date:today(),type:'IN',prodId:p.id,qty:p.qty,note:'Initial stock entry',by:'Admin'});
  closeM('addProd');
  ['ap-sku','ap-name','ap-brand','ap-cost','ap-price','ap-thresh','ap-qty'].forEach(id=>document.getElementById(id).value='');
  rProds();toast(`${p.name} added`,'ok');
}

function openEdit(id){
  const p=products.find(x=>x.id===id);if(!p)return;
  document.getElementById('ep-id').value=p.id;
  document.getElementById('ep-sku').value=p.sku;
  document.getElementById('ep-name').value=p.name;
  document.getElementById('ep-brand').value=p.brand;
  document.getElementById('ep-gender').value=p.gender;
  document.getElementById('ep-cost').value=p.cost;
  document.getElementById('ep-price').value=p.price;
  document.getElementById('ep-thresh').value=p.thresh;
  document.getElementById('ep-qty').value=p.qty;
  openM('editProd');
}
function saveEditProd(){
  const id=+document.getElementById('ep-id').value;
  const p=products.find(x=>x.id===id);if(!p)return;
  const prevQty=p.qty;
  p.sku=document.getElementById('ep-sku').value.toUpperCase();
  p.name=document.getElementById('ep-name').value;
  p.brand=document.getElementById('ep-brand').value;
  p.gender=document.getElementById('ep-gender').value;
  p.cost=+document.getElementById('ep-cost').value;
  p.price=+document.getElementById('ep-price').value;
  p.thresh=+document.getElementById('ep-thresh').value;
  p.qty=+document.getElementById('ep-qty').value;
  if(p.qty!==prevQty) movements.push({id:nmId++,date:today(),type:'ADJ',prodId:p.id,qty:p.qty,note:'Manual edit adjustment',by:'Admin'});
  closeM('editProd');rProds();toast(`${p.name} updated`,'ok');
}
function delProd(id){
  const p=products.find(x=>x.id===id);
  if(!confirm(`Delete "${p?.name}"? This cannot be undone.`))return;
  products=products.filter(x=>x.id!==id);
  rProds();toast('Product removed','info');
}

// ── STOCK MONITOR ─────────────────────────────────────────────
function setStockTab(t){
  stockTabFilter=t;
  ['all','W','M'].forEach(x=>{document.getElementById('stab-'+x)?.classList.toggle('act',x===t);});
  rStock();
}
function rStock(){
  let list=[...products];
  if(stockTabFilter!=='all') list=list.filter(p=>p.gender===stockTabFilter);
  list=applyStFilter(list);
  list.sort((a,b)=>{
    const r=p=>{const s=sStatus(p);return{Out:0,Critical:1,Low:2,In:3}[s.lbl.split(' ')[0]]??3};
    return r(a)-r(b);
  });
  const lastDate=p=>{const mv=movements.filter(m=>m.prodId===p.id);return mv.length?mv[mv.length-1].date:'—';};
  const tb=document.getElementById('s-tbody');
  tb.innerHTML=list.length?list.map(p=>{
    const s=sStatus(p);
    return `<tr>
      <td style="font-size:9px;letter-spacing:.8px;color:var(--muted)">${p.sku}</td>
      <td><div class="pn">${p.name}</div></td>
      <td style="font-size:10.5px;color:var(--muted);white-space:nowrap">${p.brand}</td>
      <td>${gTag(p.gender)}</td>
      <td style="font-family:'Cormorant Garamond',serif;font-size:22px;font-weight:300">${p.qty}</td>
      <td style="color:var(--muted)">${p.thresh}</td>
      <td><div class="pb-w"><div class="pb ${pbCls(p)}" style="width:${pbPct(p)}%"></div></div></td>
      <td><span class="stk ${s.cls}">${s.lbl}</span></td>
    </tr>`;
  }).join(''):`<tr><td colspan="8"><div class="empty"><div class="empty-icon">◈</div><p>No products match filter</p></div></td></tr>`;
}

function doAdj(){
  const pId=+document.getElementById('adj-prod').value;
  const type=document.getElementById('adj-type').value;
  const qty=+document.getElementById('adj-qty').value;
  const note=document.getElementById('adj-note').value||'—';
  if(!qty){toast('Enter a quantity','err');return;}
  const p=products.find(x=>x.id===pId);if(!p)return;
  const prev=p.qty;
  if(type==='IN')p.qty+=qty;
  else if(type==='OUT')p.qty=Math.max(0,p.qty-qty);
  else p.qty=qty;
  movements.push({id:nmId++,date:today(),type,prodId:pId,qty,note,by:'Admin'});
  closeM('adjStock');
  document.getElementById('adj-qty').value='';
  document.getElementById('adj-note').value='';
  rStock();toast(`${p.name}: ${prev} → ${p.qty} units`,'ok');
}

// ── MOVEMENTS ─────────────────────────────────────────────────
function rMv(){
  const f=document.getElementById('mv-filter').value;
  let list=[...movements].reverse();
  if(f) list=list.filter(m=>m.type===f);
  const tb=document.getElementById('mv-tbody');
  tb.innerHTML=list.length?list.map(m=>{
    const p=products.find(x=>x.id===m.prodId);
    const tl=m.type==='IN'?`<span class="mv-in">▲ Stock In</span>`:m.type==='OUT'?`<span class="mv-out">▼ Stock Out</span>`:`<span class="mv-adj">◆ Adj.</span>`;
    return `<tr>
      <td style="font-size:10px;color:var(--muted);white-space:nowrap">${m.date}</td>
      <td>${tl}</td>
      <td style="font-size:9px;letter-spacing:.7px;color:var(--muted)">${p?p.sku:'—'}</td>
      <td><span style="font-family:'Cormorant Garamond',serif;font-size:14px">${p?p.name:'Unknown'}</span></td>
      <td>${p?gTag(p.gender):''}</td>
      <td style="font-family:'Cormorant Garamond',serif;font-size:18px">${m.qty}</td>
      <td style="font-size:10.5px;color:var(--muted)">${m.note}</td>
      <td style="font-size:10.5px;color:var(--muted)">${m.by}</td>
    </tr>`;
  }).join(''):`<tr><td colspan="8"><div class="empty"><div class="empty-icon">◈</div><p>No movements found</p></div></td></tr>`;
}

function addMv(){
  const pId=+document.getElementById('mv-prod').value;
  const type=document.getElementById('mv-type').value;
  const qty=+document.getElementById('mv-qty').value;
  const note=document.getElementById('mv-note').value||'—';
  if(!qty){toast('Enter a quantity','err');return;}
  const p=products.find(x=>x.id===pId);if(!p)return;
  if(type==='IN')p.qty+=qty;
  else if(type==='OUT')p.qty=Math.max(0,p.qty-qty);
  else p.qty=qty;
  movements.push({id:nmId++,date:today(),type,prodId:pId,qty,note,by:'Admin'});
  closeM('addMv');
  document.getElementById('mv-qty').value='';
  document.getElementById('mv-note').value='';
  rMv();toast('Movement recorded','ok');
}

// ── SUPPLIERS ─────────────────────────────────────────────────
function rSup(){
  const tb=document.getElementById('sup-tbody');
  tb.innerHTML=suppliers.length?suppliers.map(s=>`<tr>
    <td style="font-family:'Cormorant Garamond',serif;font-size:15px">${s.name}</td>
    <td style="font-size:12px">${s.contact}</td>
    <td style="font-size:10.5px;color:var(--muted)">${s.email}</td>
    <td style="font-size:10.5px;color:var(--muted)">${s.phone}</td>
    <td><span class="tag">${s.cat}</span></td>
    <td><button class="ic-btn del" onclick="delSup(${s.id})"><svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"/></svg></button></td>
  </tr>`).join(''):`<tr><td colspan="6"><div class="empty"><div class="empty-icon">◈</div><p>No suppliers added</p></div></td></tr>`;
}
function addSup(){
  const name=document.getElementById('sup-name').value.trim();
  if(!name){toast('Supplier name required','err');return;}
  suppliers.push({id:nsId++,name,contact:document.getElementById('sup-contact').value,email:document.getElementById('sup-email').value,phone:document.getElementById('sup-phone').value,cat:document.getElementById('sup-cat').value});
  closeM('addSup');
  ['sup-name','sup-contact','sup-email','sup-phone'].forEach(id=>document.getElementById(id).value='');
  rSup();toast(`${name} added`,'ok');
}
function delSup(id){
  const s=suppliers.find(x=>x.id===id);
  if(!confirm(`Remove "${s?.name}"?`))return;
  suppliers=suppliers.filter(x=>x.id!==id);rSup();toast('Supplier removed','info');
}

// ── INIT ──────────────────────────────────────────────────────
(function(){
  const d=new Date();
  document.getElementById('topdate').textContent=d.toLocaleDateString('en-PH',{weekday:'short',year:'numeric',month:'short',day:'numeric'});
  rDash();
})();
</script>
</body>
</html>
