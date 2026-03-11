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
}
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
body{font-family:'Jost',sans-serif;background:var(--dark);color:var(--cream);min-height:100vh;display:flex;overflow-x:hidden;font-size:14px}
/* SIDEBAR */
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
/* MAIN */
.main{flex:1;display:flex;flex-direction:column;min-width:0;overflow:hidden}
.topbar{background:var(--dark2);border-bottom:1px solid var(--border);height:54px;display:flex;align-items:center;justify-content:space-between;padding:0 28px;position:sticky;top:0;z-index:50;flex-shrink:0}
.tb-l{font-family:'Cormorant Garamond',serif;font-size:17px;font-weight:300;letter-spacing:.8px}
.tb-l em{color:var(--gold);font-style:italic}
.tb-r{display:flex;align-items:center;gap:14px}
.tb-date{font-size:10px;color:var(--muted);letter-spacing:.3px;font-weight:200}
.av{width:30px;height:30px;border-radius:50%;background:linear-gradient(135deg,var(--gold-d),var(--gold-l));display:flex;align-items:center;justify-content:center;font-size:11px;font-weight:500;color:var(--dark);flex-shrink:0}
.content{padding:24px 28px;flex:1;overflow-y:auto}
/* PAGES */
.page{display:none;animation:fu .26s ease}
.page.act{display:block}
@keyframes fu{from{opacity:0;transform:translateY(6px)}to{opacity:1;transform:translateY(0)}}
/* PAGE HEADER */
.ph{margin-bottom:20px}
.ph h1{font-family:'Cormorant Garamond',serif;font-size:26px;font-weight:300;letter-spacing:.8px;margin-bottom:2px}
.ph h1 em{color:var(--gold);font-style:italic}
.ph p{font-size:11px;color:var(--muted);font-weight:200}
.gr{width:32px;height:1px;background:linear-gradient(90deg,var(--gold),transparent);margin:8px 0 14px}
/* STATS */
.stats{display:grid;grid-template-columns:repeat(4,1fr);gap:14px;margin-bottom:20px}
.sc{background:var(--dark3);border:1px solid var(--border);padding:18px;position:relative;overflow:hidden;transition:border-color .2s}
.sc::after{content:'';position:absolute;top:0;left:0;right:0;height:1px;background:linear-gradient(90deg,var(--gold-d),var(--gold-l),transparent);opacity:0;transition:opacity .2s}
.sc:hover{border-color:rgba(184,154,94,.4)}.sc:hover::after{opacity:1}
.sc-lbl{font-size:8.5px;letter-spacing:2px;text-transform:uppercase;color:var(--muted);margin-bottom:9px}
.sc-val{font-family:'Cormorant Garamond',serif;font-size:32px;font-weight:300;line-height:1;margin-bottom:5px}
.sc-sub{font-size:10px;color:var(--muted);font-weight:200}
.sc-b{display:inline-flex;align-items:center;padding:2px 7px;border-radius:20px;font-size:9.5px;margin-top:7px;font-weight:400}
.b-ok{background:var(--ok-bg);color:var(--ok);border:1px solid var(--ok-b)}
.b-low{background:var(--low-bg);color:var(--low);border:1px solid var(--low-b)}
.b-crit{background:var(--crit-bg);color:var(--crit);border:1px solid var(--crit-b)}
.b-info{background:rgba(184,154,94,.1);color:var(--gold-l);border:1px solid var(--border)}
/* PANEL */
.panel{background:var(--dark3);border:1px solid var(--border);margin-bottom:18px}
.panel:last-child{margin-bottom:0}
.ph2{padding:14px 18px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between;gap:10px;flex-wrap:wrap}
.pt{font-family:'Cormorant Garamond',serif;font-size:16px;font-weight:300;letter-spacing:.4px}
.ph2-r{display:flex;align-items:center;gap:8px;flex-wrap:wrap}
/* TABLE */
.tw{overflow-x:auto}
table{width:100%;border-collapse:collapse}
th{padding:9px 15px;text-align:left;font-size:8px;letter-spacing:2px;text-transform:uppercase;color:var(--muted);border-bottom:1px solid var(--border);font-weight:400;white-space:nowrap;background:rgba(0,0,0,.1)}
td{padding:12px 15px;font-size:12.5px;font-weight:300;border-bottom:1px solid var(--border2);vertical-align:middle}
tr:last-child td{border-bottom:none}
tr:hover td{background:rgba(184,154,94,.025)}
.pn{font-family:'Cormorant Garamond',serif;font-size:15px;font-weight:400}
.ps{font-size:9px;color:var(--muted);letter-spacing:.7px;margin-top:1px}
/* STATUS */
.stk{display:inline-flex;align-items:center;padding:3px 8px;font-size:9.5px;letter-spacing:.3px;font-weight:400}
.stk-ok{background:var(--ok-bg);color:var(--ok);border:1px solid var(--ok-b)}
.stk-low{background:var(--low-bg);color:var(--low);border:1px solid var(--low-b)}
.stk-crit{background:var(--crit-bg);color:var(--crit);border:1px solid var(--crit-b)}
.stk-out{background:rgba(100,80,80,.12);color:#c49a9a;border:1px solid rgba(150,100,100,.25)}
/* PROGRESS */
.pb-w{width:80px;height:3px;background:rgba(255,255,255,.07);border-radius:2px;overflow:hidden}
.pb{height:100%;border-radius:2px}
.pb-ok{background:linear-gradient(90deg,var(--ok),#7aca90)}
.pb-low{background:linear-gradient(90deg,var(--low),#e8a43c)}
.pb-crit{background:linear-gradient(90deg,var(--crit),#e05d4e)}
/* BUTTONS */
.btn{display:inline-flex;align-items:center;gap:6px;padding:7px 16px;font-family:'Jost',sans-serif;font-size:10.5px;letter-spacing:1px;text-transform:uppercase;font-weight:400;border:none;cursor:pointer;transition:all .18s}
.btn-gold{background:linear-gradient(135deg,var(--gold-d),var(--gold));color:var(--dark)}
.btn-gold:hover{background:linear-gradient(135deg,var(--gold),var(--gold-l));transform:translateY(-1px);box-shadow:0 4px 12px rgba(184,154,94,.22)}
.btn-ghost{background:transparent;color:var(--gold-l);border:1px solid var(--border)}
.btn-ghost:hover{border-color:var(--gold);background:rgba(184,154,94,.04)}
.btn-sm{padding:5px 11px;font-size:9.5px}
.ic-btn{padding:6px;background:transparent;border:1px solid var(--border);color:var(--muted);cursor:pointer;transition:all .18s;display:inline-flex;align-items:center;justify-content:center}
.ic-btn:hover{border-color:var(--gold);color:var(--gold-l)}
.ic-btn svg,.btn svg{width:12px;height:12px}
.ic-btn.del{color:var(--crit);border-color:var(--crit-b)}
.ic-btn.del:hover{background:var(--crit-bg)}
/* FORMS */
.fg{display:flex;flex-direction:column;gap:6px}
.fg label{font-size:8.5px;letter-spacing:1.5px;text-transform:uppercase;color:var(--muted);font-weight:400}
.fc{background:rgba(0,0,0,.22);border:1px solid var(--border);color:var(--cream);padding:8px 11px;font-family:'Jost',sans-serif;font-size:12.5px;font-weight:300;outline:none;transition:border-color .18s;width:100%}
.fc:focus{border-color:var(--gold);background:rgba(184,154,94,.03)}
.fc option{background:var(--dark2)}
.fg2{display:grid;grid-template-columns:1fr 1fr;gap:14px}
.fg3{display:grid;grid-template-columns:1fr 1fr 1fr;gap:14px}
/* MODAL */
.mo{position:fixed;inset:0;background:rgba(10,9,7,.82);z-index:200;display:none;align-items:center;justify-content:center;backdrop-filter:blur(3px)}
.mo.open{display:flex}
.modal{background:var(--dark2);border:1px solid var(--border);width:500px;max-width:95vw;max-height:90vh;overflow-y:auto;animation:mIn .22s ease}
@keyframes mIn{from{opacity:0;transform:scale(.97) translateY(8px)}to{opacity:1;transform:scale(1) translateY(0)}}
.mo-hd{padding:22px 22px 16px;border-bottom:1px solid var(--border);display:flex;align-items:flex-start;justify-content:space-between}
.mo-t{font-family:'Cormorant Garamond',serif;font-size:19px;font-weight:300}
.mo-x{background:none;border:none;color:var(--muted);cursor:pointer;font-size:17px;line-height:1;padding:2px;transition:color .15s}
.mo-x:hover{color:var(--cream)}
.mo-b{padding:20px 22px}
.mo-ft{padding:14px 22px;border-top:1px solid var(--border);display:flex;gap:9px;justify-content:flex-end}
/* TOAST */
.tc{position:fixed;top:64px;right:18px;z-index:300;display:flex;flex-direction:column;gap:8px}
.toast{background:var(--dark2);border:1px solid var(--border);padding:11px 15px;min-width:240px;display:flex;align-items:center;gap:9px;font-size:12px;font-weight:300;box-shadow:0 6px 20px rgba(0,0,0,.4);animation:tIn .22s ease}
.toast.out{animation:tOut .22s ease forwards}
@keyframes tIn{from{opacity:0;transform:translateX(12px)}to{opacity:1;transform:translateX(0)}}
@keyframes tOut{to{opacity:0;transform:translateX(12px)}}
.td{width:6px;height:6px;border-radius:50%;flex-shrink:0}
.t-ok .td{background:var(--ok)}.t-err .td{background:var(--crit)}.t-info .td{background:var(--gold)}
/* FILTER ROW */
.fr{display:flex;gap:8px;align-items:center;flex-wrap:wrap}
.sr{position:relative;flex:1;min-width:140px}
.sr .fc{padding-left:30px}
.si{position:absolute;left:9px;top:50%;transform:translateY(-50%);color:var(--muted);width:13px;height:13px;pointer-events:none}
/* ACTIVITY */
.ai{padding:11px 18px;display:flex;align-items:flex-start;gap:11px;border-bottom:1px solid var(--border2)}
.ai:last-child{border-bottom:none}
.ad{width:7px;height:7px;border-radius:50%;margin-top:4px;flex-shrink:0}
.at{font-size:12px;font-weight:300;margin-bottom:2px}
.am{font-size:10px;color:var(--muted);font-weight:200}
/* 2-COL */
.tc2{display:grid;grid-template-columns:1fr 300px;gap:18px}
/* MINI CHART */
.mch{display:flex;align-items:flex-end;gap:3px;height:38px;padding:0 18px 14px}
.mb{flex:1;background:rgba(184,154,94,.18);min-width:8px;transition:background .2s;cursor:pointer}
.mb:hover,.mb.hi{background:rgba(184,154,94,.5)}
/* EMPTY */
.empty{text-align:center;padding:40px 16px;color:var(--muted)}
.empty-icon{font-size:28px;opacity:.25;margin-bottom:8px}
.empty p{font-size:12px;font-weight:200}
/* TAG */
.tag{display:inline-flex;align-items:center;padding:2px 7px;font-size:10px;font-weight:300;background:rgba(184,154,94,.08);color:var(--gold-l);border:1px solid var(--border)}
/* MV TYPES */
.mv-in{color:#6aaa7e;font-size:10.5px}.mv-out{color:#e05d4e;font-size:10.5px}.mv-adj{color:var(--gold-l);font-size:10.5px}
::-webkit-scrollbar{width:3px;height:3px}
::-webkit-scrollbar-track{background:transparent}
::-webkit-scrollbar-thumb{background:var(--border);border-radius:2px}
::-webkit-scrollbar-thumb:hover{background:var(--gold-d)}
@media(max-width:1060px){.stats{grid-template-columns:repeat(2,1fr)}.tc2{grid-template-columns:1fr}}
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
      <div class="ph"><h1>Good morning, <em>Admin</em></h1><div class="gr"></div><p>Inventory snapshot — Yodge Perfume House</p></div>
      <div class="stats" id="d-stats"></div>
      <div class="tc2">
        <div>
          <div class="panel">
            <div class="ph2"><span class="pt">Low Stock Alerts</span><span class="sc-b b-low" id="d-lowbadge"></span></div>
            <div class="tw"><table><thead><tr><th>Product</th><th>Category</th><th>Qty</th><th>Min</th><th>Status</th></tr></thead><tbody id="d-alerts"></tbody></table></div>
          </div>
          <div class="panel">
            <div class="ph2"><span class="pt">Recent Movements</span></div>
            <div id="d-mv"></div>
          </div>
        </div>
        <div>
          <div class="panel">
            <div class="ph2"><span class="pt">By Category</span></div>
            <div id="d-cats" style="padding:14px 18px"></div>
          </div>
          <div class="panel">
            <div class="ph2"><span class="pt">Weekly Activity</span></div>
            <div id="d-chart" class="mch"></div>
            <div style="font-size:9.5px;color:var(--muted);padding:0 18px 12px;font-weight:200">Units moved last 7 days</div>
          </div>
        </div>
      </div>
    </div>

    <!-- PRODUCTS -->
    <div class="page" id="p-products">
      <div class="ph"><h1>Product <em>Catalogue</em></h1><div class="gr"></div><p>All fragrance SKUs and pricing</p></div>
      <div class="panel">
        <div class="ph2">
          <span class="pt">All Products</span>
          <div class="ph2-r">
            <div class="sr"><svg class="si" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-4.35-4.35M17 11A6 6 0 115 11a6 6 0 0112 0z"/></svg><input class="fc" id="p-search" placeholder="Search…" oninput="rProds()" style="padding-left:28px;width:170px"/></div>
            <select class="fc" id="p-cfilter" onchange="rProds()" style="width:130px"><option value="">All Categories</option><option>Eau de Parfum</option><option>Eau de Toilette</option><option>Cologne</option><option>Body Mist</option><option>Attar / Oil</option></select>
            <button class="btn btn-gold" onclick="openM('addProd')"><svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M12 4v16m8-8H4"/></svg>Add Product</button>
          </div>
        </div>
        <div class="tw"><table><thead><tr><th>SKU</th><th>Product Name</th><th>Category</th><th>Vol</th><th>Cost</th><th>Retail</th><th>Supplier</th><th>Min Qty</th><th>Actions</th></tr></thead><tbody id="p-tbody"></tbody></table></div>
      </div>
    </div>

    <!-- STOCK MONITOR -->
    <div class="page" id="p-stock">
      <div class="ph"><h1>Stock <em>Monitor</em></h1><div class="gr"></div><p>Real-time inventory levels across all products</p></div>
      <div class="panel">
        <div class="ph2">
          <span class="pt">Current Stock Levels</span>
          <div class="ph2-r">
            <select class="fc" id="s-filter" onchange="rStock()" style="width:130px"><option value="">All Statuses</option><option value="ok">In Stock</option><option value="low">Low Stock</option><option value="crit">Critical</option><option value="out">Out of Stock</option></select>
            <button class="btn btn-ghost btn-sm" onclick="openM('adjStock')">Adjust Stock</button>
          </div>
        </div>
        <div class="tw"><table><thead><tr><th>Product</th><th>Category</th><th>Qty</th><th>Min Qty</th><th>Level</th><th>Status</th><th>Last Updated</th></tr></thead><tbody id="s-tbody"></tbody></table></div>
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
        <div class="tw"><table><thead><tr><th>Date</th><th>Type</th><th>Product</th><th>Qty</th><th>Notes</th><th>By</th></tr></thead><tbody id="mv-tbody"></tbody></table></div>
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
<!-- Add Product -->
<div class="mo" id="mo-addProd">
  <div class="modal">
    <div class="mo-hd"><span class="mo-t">Add New Product</span><button class="mo-x" onclick="closeM('addProd')">✕</button></div>
    <div class="mo-b">
      <div class="fg2" style="gap:14px">
        <div class="fg"><label>SKU</label><input class="fc" id="ap-sku" placeholder="YP-001"/></div>
        <div class="fg"><label>Product Name</label><input class="fc" id="ap-name" placeholder="Rose Noir Elixir"/></div>
        <div class="fg"><label>Category</label><select class="fc" id="ap-cat"><option>Eau de Parfum</option><option>Eau de Toilette</option><option>Cologne</option><option>Body Mist</option><option>Attar / Oil</option></select></div>
        <div class="fg"><label>Volume (ml)</label><input class="fc" id="ap-vol" type="number" placeholder="50"/></div>
        <div class="fg"><label>Cost Price (₱)</label><input class="fc" id="ap-cost" type="number" placeholder="0"/></div>
        <div class="fg"><label>Retail Price (₱)</label><input class="fc" id="ap-price" type="number" placeholder="0"/></div>
        <div class="fg"><label>Supplier</label><select class="fc" id="ap-sup"></select></div>
        <div class="fg"><label>Min Stock (Threshold)</label><input class="fc" id="ap-thresh" type="number" placeholder="10"/></div>
        <div class="fg" style="grid-column:span 2"><label>Initial Stock Qty</label><input class="fc" id="ap-qty" type="number" placeholder="0"/></div>
      </div>
    </div>
    <div class="mo-ft"><button class="btn btn-ghost" onclick="closeM('addProd')">Cancel</button><button class="btn btn-gold" onclick="addProd()">Save Product</button></div>
  </div>
</div>

<!-- Edit Product -->
<div class="mo" id="mo-editProd">
  <div class="modal">
    <div class="mo-hd"><span class="mo-t">Edit Product</span><button class="mo-x" onclick="closeM('editProd')">✕</button></div>
    <div class="mo-b">
      <input type="hidden" id="ep-id"/>
      <div class="fg2" style="gap:14px">
        <div class="fg"><label>SKU</label><input class="fc" id="ep-sku"/></div>
        <div class="fg"><label>Product Name</label><input class="fc" id="ep-name"/></div>
        <div class="fg"><label>Category</label><select class="fc" id="ep-cat"><option>Eau de Parfum</option><option>Eau de Toilette</option><option>Cologne</option><option>Body Mist</option><option>Attar / Oil</option></select></div>
        <div class="fg"><label>Volume (ml)</label><input class="fc" id="ep-vol" type="number"/></div>
        <div class="fg"><label>Cost Price (₱)</label><input class="fc" id="ep-cost" type="number"/></div>
        <div class="fg"><label>Retail Price (₱)</label><input class="fc" id="ep-price" type="number"/></div>
        <div class="fg"><label>Supplier</label><select class="fc" id="ep-sup"></select></div>
        <div class="fg"><label>Min Stock (Threshold)</label><input class="fc" id="ep-thresh" type="number"/></div>
      </div>
    </div>
    <div class="mo-ft"><button class="btn btn-ghost" onclick="closeM('editProd')">Cancel</button><button class="btn btn-gold" onclick="saveEditProd()">Save Changes</button></div>
  </div>
</div>

<!-- Adjust Stock -->
<div class="mo" id="mo-adjStock">
  <div class="modal">
    <div class="mo-hd"><span class="mo-t">Adjust Stock</span><button class="mo-x" onclick="closeM('adjStock')">✕</button></div>
    <div class="mo-b">
      <div style="display:grid;gap:14px">
        <div class="fg"><label>Product</label><select class="fc" id="adj-prod"></select></div>
        <div class="fg2" style="gap:14px">
          <div class="fg"><label>Adjustment Type</label><select class="fc" id="adj-type"><option value="IN">Stock In (Add)</option><option value="OUT">Stock Out (Deduct)</option><option value="ADJ">Set Exact Qty</option></select></div>
          <div class="fg"><label>Quantity</label><input class="fc" id="adj-qty" type="number" placeholder="0"/></div>
        </div>
        <div class="fg"><label>Notes / Reason</label><input class="fc" id="adj-note" placeholder="e.g. Restocked from supplier"/></div>
      </div>
    </div>
    <div class="mo-ft"><button class="btn btn-ghost" onclick="closeM('adjStock')">Cancel</button><button class="btn btn-gold" onclick="doAdj()">Apply</button></div>
  </div>
</div>

<!-- Record Movement -->
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

<!-- Add Supplier -->
<div class="mo" id="mo-addSup">
  <div class="modal">
    <div class="mo-hd"><span class="mo-t">Add Supplier</span><button class="mo-x" onclick="closeM('addSup')">✕</button></div>
    <div class="mo-b">
      <div class="fg2" style="gap:14px">
        <div class="fg" style="grid-column:span 2"><label>Supplier Name</label><input class="fc" id="sup-name" placeholder="Arabian Oud Trading"/></div>
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
// ── DATA ──────────────────────────────────────────────────────
let suppliers=[
  {id:1,name:'Arabian Oud Trading',contact:'Hassan Al-Maktoum',email:'hassan@arabioud.ph',phone:'+63 917 555 0011',cat:'Fragrance Oils'},
  {id:2,name:'Luxe Bottles & Co.',contact:'Maria Santos',email:'maria@luxebottles.ph',phone:'+63 918 555 0022',cat:'Bottles & Packaging'},
  {id:3,name:'Scent Essence PH',contact:'Diego Reyes',email:'diego@scentessence.ph',phone:'+63 919 555 0033',cat:'Raw Materials'},
];
let products=[
  {id:1,sku:'YP-001',name:'Rose Noir Elixir',cat:'Eau de Parfum',vol:50,cost:420,price:1200,supId:1,thresh:10,qty:38},
  {id:2,sku:'YP-002',name:'Oud Royale',cat:'Eau de Parfum',vol:100,cost:680,price:2200,supId:1,thresh:8,qty:6},
  {id:3,sku:'YP-003',name:'Amber Soir',cat:'Eau de Toilette',vol:75,cost:350,price:980,supId:3,thresh:12,qty:0},
  {id:4,sku:'YP-004',name:'Jasmine Nuit',cat:'Eau de Toilette',vol:50,cost:280,price:750,supId:3,thresh:15,qty:22},
  {id:5,sku:'YP-005',name:'Vetiver Storm',cat:'Cologne',vol:100,cost:310,price:850,supId:1,thresh:10,qty:4},
  {id:6,sku:'YP-006',name:'Blanche Lumière',cat:'Body Mist',vol:200,cost:180,price:450,supId:2,thresh:20,qty:45},
  {id:7,sku:'YP-007',name:'Saffron Mystique',cat:'Attar / Oil',vol:12,cost:550,price:1600,supId:1,thresh:5,qty:3},
  {id:8,sku:'YP-008',name:'Cedar & Smoke',cat:'Cologne',vol:75,cost:290,price:820,supId:3,thresh:10,qty:17},
];
let movements=[
  {id:1,date:'2025-03-10',type:'IN',prodId:1,qty:20,note:'Restocked from Arabian Oud',by:'Admin'},
  {id:2,date:'2025-03-10',type:'OUT',prodId:3,qty:5,note:'Sold — walk-in customer',by:'Admin'},
  {id:3,date:'2025-03-09',type:'IN',prodId:6,qty:30,note:'Delivery from Luxe Bottles',by:'Admin'},
  {id:4,date:'2025-03-08',type:'OUT',prodId:2,qty:2,note:'Online order fulfillment',by:'Admin'},
  {id:5,date:'2025-03-07',type:'ADJ',prodId:5,qty:4,note:'Inventory count correction',by:'Admin'},
  {id:6,date:'2025-03-07',type:'IN',prodId:7,qty:8,note:'New shipment arrived',by:'Admin'},
  {id:7,date:'2025-03-06',type:'OUT',prodId:4,qty:3,note:'Event / pop-up sale',by:'Admin'},
];
let npId=9,nsId=4,nmId=8;

// ── UTILS ─────────────────────────────────────────────────────
const fmt=n=>'₱'+Number(n).toLocaleString('en-PH',{minimumFractionDigits:2});
const today=()=>new Date().toISOString().slice(0,10);
const supName=id=>{const s=suppliers.find(x=>x.id===id);return s?s.name:'—';};

function sStatus(p){
  if(p.qty===0)return{cls:'stk-out',lbl:'Out of Stock',dcls:'b-crit',dot:'#7a6060'};
  if(p.qty<=Math.floor(p.thresh*.5))return{cls:'stk-crit',lbl:'Critical',dcls:'b-crit',dot:'#c0402e'};
  if(p.qty<=p.thresh)return{cls:'stk-low',lbl:'Low Stock',dcls:'b-low',dot:'#c9883a'};
  return{cls:'stk-ok',lbl:'In Stock',dcls:'b-ok',dot:'#5a9e6f'};
}
function pbCls(p){const s=sStatus(p);return s.lbl==='In Stock'?'pb-ok':s.lbl==='Low Stock'?'pb-low':'pb-crit';}
function pbPct(p){const mx=Math.max(p.thresh*3,p.qty,1);return Math.min(100,Math.round(p.qty/mx*100));}

function toast(msg,type='info'){
  const w=document.getElementById('toasts');
  const el=document.createElement('div');
  el.className=`toast t-${type}`;
  el.innerHTML=`<div class="td"></div><span>${msg}</span>`;
  w.appendChild(el);
  setTimeout(()=>{el.classList.add('out');setTimeout(()=>el.remove(),280);},3000);
}

function openM(id){
  if(id==='addProd')fillSupSel('ap-sup');
  if(id==='adjStock')fillProdSel('adj-prod');
  if(id==='addMv')fillProdSel('mv-prod');
  if(id==='editProd')fillSupSel('ep-sup');
  document.getElementById('mo-'+id).classList.add('open');
}
function closeM(id){document.getElementById('mo-'+id).classList.remove('open');}
document.querySelectorAll('.mo').forEach(m=>m.addEventListener('click',e=>{if(e.target===m)m.classList.remove('open');}));

function fillSupSel(id){
  document.getElementById(id).innerHTML=suppliers.map(s=>`<option value="${s.id}">${s.name}</option>`).join('');
}
function fillProdSel(id){
  document.getElementById(id).innerHTML=products.map(p=>`<option value="${p.id}">${p.sku} — ${p.name}</option>`).join('');
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
  const outC=products.filter(p=>p.qty===0).length;
  const lowC=products.filter(p=>p.qty>0&&p.qty<=p.thresh).length;
  const totalQ=products.reduce((a,p)=>a+p.qty,0);

  document.getElementById('d-stats').innerHTML=`
    <div class="sc"><div class="sc-lbl">Total Products</div><div class="sc-val">${total}</div><div class="sc-sub">Active SKUs</div><span class="sc-b b-info">${products.filter(p=>p.cat==='Eau de Parfum').length} EDP</span></div>
    <div class="sc"><div class="sc-lbl">Total Units</div><div class="sc-val">${totalQ}</div><div class="sc-sub">In warehouse</div></div>
    <div class="sc"><div class="sc-lbl">Low Stock</div><div class="sc-val" style="color:var(--low)">${lowC}</div><div class="sc-sub">Need restocking</div>${lowC?'<span class="sc-b b-low">Action needed</span>':''}</div>
    <div class="sc"><div class="sc-lbl">Out of Stock</div><div class="sc-val" style="color:var(--crit)">${outC}</div><div class="sc-sub">Zero inventory</div>${outC?'<span class="sc-b b-crit">Urgent</span>':''}</div>
  `;

  const alerts=products.filter(p=>p.qty<=p.thresh);
  document.getElementById('d-lowbadge').textContent=alerts.length+' items';
  const ab=document.getElementById('d-alerts');
  ab.innerHTML=alerts.length?alerts.map(p=>{
    const s=sStatus(p);
    return `<tr><td><div class="pn">${p.name}</div><div class="ps">${p.sku}</div></td><td><span class="tag">${p.cat}</span></td><td>${p.qty}</td><td>${p.thresh}</td><td><span class="stk ${s.cls}">${s.lbl}</span></td></tr>`;
  }).join(''):'<tr><td colspan="5" style="text-align:center;padding:20px;color:var(--muted);font-size:11px">All stock levels are healthy ✓</td></tr>';

  document.getElementById('d-mv').innerHTML=movements.slice(-5).reverse().map(m=>{
    const p=products.find(x=>x.id===m.prodId);
    const dot=m.type==='IN'?'#5a9e6f':m.type==='OUT'?'#c0402e':'#b89a5e';
    return `<div class="ai"><div class="ad" style="background:${dot}"></div><div><div class="at">${m.type==='IN'?'+':m.type==='OUT'?'-':'±'}${m.qty} × ${p?p.name:'Unknown'}</div><div class="am">${m.date} · ${m.note}</div></div></div>`;
  }).join('');

  const cats=[...new Set(products.map(p=>p.cat))];
  const catQtys=cats.map(c=>products.filter(p=>p.cat===c).reduce((a,p)=>a+p.qty,0));
  const maxQ=Math.max(...catQtys,1);
  document.getElementById('d-cats').innerHTML=cats.map((c,i)=>`
    <div style="margin-bottom:12px">
      <div style="display:flex;justify-content:space-between;margin-bottom:4px">
        <span style="font-size:11px;font-weight:300">${c}</span>
        <span style="font-size:10px;color:var(--muted)">${catQtys[i]}</span>
      </div>
      <div style="height:3px;background:rgba(255,255,255,.06);border-radius:2px">
        <div style="height:100%;width:${Math.round(catQtys[i]/maxQ*100)}%;background:linear-gradient(90deg,var(--gold-d),var(--gold-l));border-radius:2px"></div>
      </div>
    </div>`).join('');

  const wd=[12,8,20,15,6,18,24];
  const wm=Math.max(...wd);
  document.getElementById('d-chart').innerHTML=wd.map((v,i)=>
    `<div class="mb ${i===6?'hi':''}" style="height:${Math.round(v/wm*100)}%" title="${v} units"></div>`
  ).join('');
}

// ── PRODUCTS ──────────────────────────────────────────────────
function rProds(){
  const q=(document.getElementById('p-search').value||'').toLowerCase();
  const cat=document.getElementById('p-cfilter').value;
  const list=products.filter(p=>(!q||p.name.toLowerCase().includes(q)||p.sku.toLowerCase().includes(q))&&(!cat||p.cat===cat));
  const tb=document.getElementById('p-tbody');
  if(!list.length){tb.innerHTML=`<tr><td colspan="9"><div class="empty"><div class="empty-icon">◈</div><p>No products found</p></div></td></tr>`;return;}
  tb.innerHTML=list.map(p=>`<tr>
    <td style="font-size:9.5px;letter-spacing:.8px;color:var(--muted)">${p.sku}</td>
    <td><div class="pn">${p.name}</div></td>
    <td><span class="tag">${p.cat}</span></td>
    <td style="color:var(--muted);font-size:11px">${p.vol}ml</td>
    <td>${fmt(p.cost)}</td>
    <td>${fmt(p.price)}</td>
    <td style="font-size:10.5px;color:var(--muted)">${supName(p.supId)}</td>
    <td style="color:var(--muted)">${p.thresh}</td>
    <td><div style="display:flex;gap:5px">
      <button class="ic-btn" onclick="openEdit(${p.id})" title="Edit"><svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"/></svg></button>
      <button class="ic-btn del" onclick="delProd(${p.id})" title="Delete"><svg fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"/></svg></button>
    </div></td>
  </tr>`).join('');
}

function addProd(){
  const sku=document.getElementById('ap-sku').value.trim();
  const name=document.getElementById('ap-name').value.trim();
  if(!sku||!name){toast('SKU and Name are required','err');return;}
  const p={id:npId++,sku,name,cat:document.getElementById('ap-cat').value,vol:+document.getElementById('ap-vol').value||0,cost:+document.getElementById('ap-cost').value||0,price:+document.getElementById('ap-price').value||0,supId:+document.getElementById('ap-sup').value,thresh:+document.getElementById('ap-thresh').value||10,qty:+document.getElementById('ap-qty').value||0};
  products.push(p);
  if(p.qty>0)movements.push({id:nmId++,date:today(),type:'IN',prodId:p.id,qty:p.qty,note:'Initial stock entry',by:'Admin'});
  closeM('addProd');
  ['ap-sku','ap-name','ap-vol','ap-cost','ap-price','ap-thresh','ap-qty'].forEach(id=>document.getElementById(id).value='');
  rProds();toast(`${p.name} added`,'ok');
}

function openEdit(id){
  const p=products.find(x=>x.id===id);if(!p)return;
  fillSupSel('ep-sup');
  ['id','sku','name','vol','cost','price','thresh'].forEach(k=>document.getElementById('ep-'+k).value=p[k]);
  document.getElementById('ep-cat').value=p.cat;
  document.getElementById('ep-sup').value=p.supId;
  openM('editProd');
}
function saveEditProd(){
  const id=+document.getElementById('ep-id').value;
  const p=products.find(x=>x.id===id);if(!p)return;
  p.sku=document.getElementById('ep-sku').value;
  p.name=document.getElementById('ep-name').value;
  p.cat=document.getElementById('ep-cat').value;
  p.vol=+document.getElementById('ep-vol').value;
  p.cost=+document.getElementById('ep-cost').value;
  p.price=+document.getElementById('ep-price').value;
  p.supId=+document.getElementById('ep-sup').value;
  p.thresh=+document.getElementById('ep-thresh').value;
  closeM('editProd');rProds();toast(`${p.name} updated`,'ok');
}
function delProd(id){
  const p=products.find(x=>x.id===id);
  if(!confirm(`Delete "${p?.name}"? This cannot be undone.`))return;
  products=products.filter(x=>x.id!==id);
  rProds();toast(`Product removed`,'info');
}

// ── STOCK MONITOR ─────────────────────────────────────────────
function rStock(){
  const f=document.getElementById('s-filter').value;
  let list=[...products].sort((a,b)=>{
    const r=p=>{const s=sStatus(p);return{Out:0,Critical:1,Low:2,In:3}[s.lbl.split(' ')[0]]??3};
    return r(a)-r(b);
  });
  if(f)list=list.filter(p=>{
    const s=sStatus(p);
    return f==='ok'?s.lbl==='In Stock':f==='low'?s.lbl==='Low Stock':f==='crit'?s.lbl==='Critical':s.lbl==='Out of Stock';
  });
  const lastDate=p=>{const mv=movements.filter(m=>m.prodId===p.id);return mv.length?mv[mv.length-1].date:'—';};
  const tb=document.getElementById('s-tbody');
  tb.innerHTML=list.length?list.map(p=>{
    const s=sStatus(p);
    return `<tr>
      <td><div class="pn">${p.name}</div><div class="ps">${p.sku}</div></td>
      <td><span class="tag">${p.cat}</span></td>
      <td style="font-family:'Cormorant Garamond',serif;font-size:22px;font-weight:300">${p.qty}</td>
      <td style="color:var(--muted)">${p.thresh}</td>
      <td><div class="pb-w"><div class="pb ${pbCls(p)}" style="width:${pbPct(p)}%"></div></div></td>
      <td><span class="stk ${s.cls}">${s.lbl}</span></td>
      <td style="font-size:10.5px;color:var(--muted)">${lastDate(p)}</td>
    </tr>`;
  }).join(''):`<tr><td colspan="7"><div class="empty"><div class="empty-icon">◈</div><p>No products match filter</p></div></td></tr>`;
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
  if(f)list=list.filter(m=>m.type===f);
  const tb=document.getElementById('mv-tbody');
  tb.innerHTML=list.length?list.map(m=>{
    const p=products.find(x=>x.id===m.prodId);
    const tl=m.type==='IN'?`<span class="mv-in">▲ Stock In</span>`:m.type==='OUT'?`<span class="mv-out">▼ Stock Out</span>`:`<span class="mv-adj">◆ Adjustment</span>`;
    return `<tr><td style="font-size:10.5px;color:var(--muted)">${m.date}</td><td>${tl}</td><td><span style="font-family:'Cormorant Garamond',serif;font-size:14px">${p?p.name:'—'}</span></td><td style="font-family:'Cormorant Garamond',serif;font-size:18px">${m.qty}</td><td style="font-size:10.5px;color:var(--muted)">${m.note}</td><td style="font-size:10.5px;color:var(--muted)">${m.by}</td></tr>`;
  }).join(''):`<tr><td colspan="6"><div class="empty"><div class="empty-icon">◈</div><p>No movements found</p></div></td></tr>`;
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
  suppliers=suppliers.filter(x=>x.id!==id);
  rSup();toast('Supplier removed','info');
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
