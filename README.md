<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Gerenciador de Projetos · Dengo</title>
<link href="https://fonts.googleapis.com/css2?family=Work+Sans:wght@300;400;500;600&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<!-- Firebase (compat SDK — funciona sem bundler) -->
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-database-compat.js"></script>
<script>
// ===== Firebase config (Realtime Database) =====
const firebaseConfig = {
  apiKey: "AIzaSyCIeJtAcHJkoSiY_MEpc3K8lefg8W3xvIw",
  authDomain: "gannt-efeed.firebaseapp.com",
  databaseURL: "https://gannt-efeed-default-rtdb.firebaseio.com",
  projectId: "gannt-efeed",
  storageBucket: "gannt-efeed.firebasestorage.app",
  messagingSenderId: "424184613544",
  appId: "1:424184613544:web:b99f6c1063df120f8a909d"
};
let fbDB = null, fbReady = false;
try {
  firebase.initializeApp(firebaseConfig);
  fbDB = firebase.database();
  fbReady = true;
  console.log('[Firebase] conectado ao Realtime Database');
} catch(e){
  console.warn('[Firebase] indisponível, usando localStorage:', e?.message||e);
}
</script>
<style>
/* ===== TOKENS ===== */
:root{
  /* Fundos creme — sequência suave, sem ferir os olhos */
  --bg:#FAF4E6;          /* creme principal (papel) */
  --s1:#F2EAD5;          /* tom levemente mais quente para header de grupo */
  --s2:#EADEC0;          /* linha de cabeçalho da tabela */
  --s3:#DFCFA7;          /* faixa de meses, mais saturada para contraste */

  /* Bordas e texto */
  --border:rgba(72,49,27,.10);
  --border2:rgba(72,49,27,.18);
  --text:#3A2812;        /* marrom escuro Dengo, levemente mais profundo */
  --muted:#7A5C35;
  --dim:#B89A6E;

  /* Cores de marca Dengo (preservadas) */
  --accent:#FFA600;      /* laranja Dengo (em andamento, destaque) */
  --accent2:#F4C500;     /* amarelo Dengo levemente dessaturado (era FFD200) */
  --pink:#F1A6B5;
  --green:#286B37;
  --red-d:#E95C24;
  --brown:#48311B;

  /* Aliases semânticos */
  --done:#286B37;
  --prog:#E89400;        /* laranja levemente mais quente que o accent puro */
  --wait:#B89A6E;
  --blok:#E95C24;

  --font:'Work Sans',sans-serif;
  --logo-dengo:url("data:image/png;base64,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");
}
*{box-sizing:border-box;margin:0;padding:0;}
body{font-family:var(--font);font-weight:300;background:var(--bg);color:var(--text);font-size:13px;}
button,input,select,textarea{font-family:var(--font);}
button{cursor:pointer;}

/* ===== BUTTONS ===== */
.btn{font-size:10px;font-weight:500;border-radius:6px;padding:4px 10px;border:none;transition:opacity .12s;}
.btn:hover{opacity:.82;}
.btn-primary{background:var(--accent2);color:var(--brown);}
.btn-ghost{background:transparent;border:1.5px solid var(--border2);color:var(--text);}
.btn-success{background:var(--done);color:#fff;}
.btn-danger{background:var(--red-d);color:#fff;}
.btn-warn{background:var(--accent);color:var(--brown);}
.btn-brown{background:var(--brown);color:#fff;}

/* ===== HUB ===== */
#hub{min-height:100vh;display:flex;flex-direction:column;align-items:center;justify-content:center;padding:40px 20px;}
.hub-logo{font-size:56px;margin-bottom:8px;}
.hub-title{font-size:28px;font-weight:600;margin-bottom:4px;}
.hub-sub{font-size:13px;color:var(--muted);margin-bottom:32px;}
.hub-projects{display:flex;flex-wrap:wrap;gap:14px;justify-content:center;max-width:900px;margin-bottom:28px;}
.proj-card{background:#fff;border:1.5px solid var(--border2);border-radius:10px;padding:16px;width:220px;position:relative;transition:box-shadow .15s;}
.proj-card:hover{box-shadow:0 4px 16px rgba(72,49,27,.12);}
.proj-card-top{display:flex;align-items:center;gap:8px;margin-bottom:8px;}
.proj-dot{width:10px;height:10px;border-radius:50%;flex-shrink:0;}
.proj-name{font-weight:500;font-size:13px;flex:1;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;}
.proj-badge{position:absolute;top:10px;right:10px;background:var(--s2);border-radius:4px;font-size:10px;font-weight:500;padding:2px 6px;color:var(--muted);}
.proj-meta{font-size:11px;color:var(--muted);margin-bottom:4px;}
.proj-id{font-size:10px;color:var(--dim);font-family:monospace;margin-bottom:12px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;}
.proj-btns{display:flex;gap:6px;}
.btn-new{background:transparent;border:2px dashed var(--accent2);border-radius:10px;padding:14px 20px;color:var(--brown);font-weight:500;font-size:13px;width:220px;text-align:center;transition:background .15s;}
.btn-new:hover{background:rgba(244,197,0,.12);}
.hub-access{display:flex;gap:8px;}
.hub-access input{padding:7px 12px;border:1.5px solid var(--border2);border-radius:6px;background:var(--s1);font-size:12px;color:var(--text);width:280px;}
.hub-footer{font-size:11px;color:var(--dim);text-align:center;margin-top:16px;}

/* ===== APP SHELL ===== */
#app{display:none;flex-direction:column;height:100vh;overflow:hidden;}

/* ===== TOPBAR ===== */
#topbar{display:flex;align-items:center;height:52px;background:var(--bg);border-bottom:2px solid var(--accent2);position:sticky;top:0;z-index:100;padding:0 14px;backdrop-filter:blur(8px);flex-shrink:0;}
.tb-logo{height:30px;display:flex;align-items:center;justify-content:center;flex-shrink:0;margin-right:8px;}
.tb-proj{font-weight:500;font-size:13px;margin-right:4px;}
.tb-id{font-size:10px;color:var(--dim);font-family:monospace;}
.tb-sep{width:1px;background:var(--border2);height:30px;margin:0 10px;flex-shrink:0;}
.tb-group{display:flex;align-items:center;gap:5px;padding:0 8px;}
.tb-right{margin-left:auto;display:flex;align-items:center;}
.chip{display:flex;align-items:center;gap:5px;padding:3px 8px;border-radius:6px;font-size:10px;font-weight:500;background:var(--s1);border:1px solid var(--border);}
.sync-dot{width:7px;height:7px;border-radius:50%;background:#286B37;flex-shrink:0;}
@keyframes pulse{0%,100%{opacity:1;}50%{opacity:.4;}}
.sync-dot.pulse{animation:pulse 1.5s infinite;}

/* ===== FILTER BAR ===== */
#filterbar{background:var(--s1);padding:6px 14px;display:flex;flex-wrap:wrap;align-items:center;gap:6px;border-bottom:1px solid var(--border);flex-shrink:0;}
.filter-btn{font-size:10px;font-weight:500;border-radius:5px;padding:3px 9px;border:1px solid var(--border2);background:var(--s2);color:var(--muted);cursor:pointer;transition:all .12s;}
.filter-sep{width:1px;background:var(--border2);height:20px;margin:0 4px;}

/* ===== KPI STRIP ===== */
#kpi-strip{display:flex;gap:1px;background:var(--border);flex-shrink:0;}
.kpi-card{flex:1;background:var(--s1);padding:8px 12px;}
.kpi-label{font-size:9px;font-weight:500;text-transform:uppercase;letter-spacing:1.2px;color:var(--muted);margin-bottom:3px;}
.kpi-val{font-size:16px;font-weight:600;line-height:1;}
.kpi-sub{font-size:9px;color:var(--muted);margin-top:2px;}
.kpi-bar{height:3px;border-radius:2px;margin-top:5px;background:var(--border2);}
.kpi-bar-fill{height:100%;border-radius:2px;transition:width .3s;}

/* ===== MAIN SCROLL ===== */
#main-scroll{flex:1;overflow-y:auto;overflow-x:hidden;padding:12px 14px;}

/* ===== GROUP ===== */
.group-block{border:1.5px solid var(--border);border-radius:8px;margin-bottom:14px;overflow:hidden;}
.group-head{display:flex;align-items:center;gap:8px;padding:8px 12px;background:var(--s1);}
.group-color-dot{width:9px;height:9px;border-radius:50%;cursor:pointer;flex-shrink:0;}
.group-name-inp{border:none;background:transparent;font-weight:600;font-size:13px;outline:none;border-bottom:1px dashed transparent;flex:1;min-width:60px;}
.group-name-inp:focus{border-bottom-color:var(--accent);}
.group-meta{font-size:11px;color:var(--muted);white-space:nowrap;}
.group-mini-bar{width:48px;height:4px;border-radius:2px;background:var(--s3);overflow:hidden;flex-shrink:0;}
.group-mini-fill{height:100%;border-radius:2px;}
.group-pct{font-size:11px;font-weight:500;min-width:32px;text-align:right;}
.group-btn{font-size:10px;font-weight:500;padding:3px 8px;border-radius:5px;border:none;cursor:pointer;}
.group-add-btn{background:rgba(244,197,0,.18);border:1px solid rgba(244,197,0,.5);color:var(--brown);}
.group-del-btn{background:rgba(233,92,36,.10);border:1px solid rgba(233,92,36,.25);color:var(--red-d);}
.collapse-btn{background:none;border:none;cursor:pointer;font-size:12px;padding:2px 5px;color:var(--muted);transition:transform .2s;}
.collapsed .collapse-btn{transform:rotate(-90deg);}
.collapsed .group-body,.collapsed .group-footer{display:none;}
.group-body{overflow-x:auto;}
.group-footer{padding:6px 12px;background:var(--s1);border-top:1px solid var(--border);}
.quick-add{width:100%;border:1px dashed var(--dim);border-radius:4px;background:transparent;padding:5px 10px;font-size:11px;color:var(--muted);outline:none;}
.quick-add:focus{border-color:var(--accent);color:var(--text);}

/* ===== GANTT TABLE ===== */
.gantt-table{border-collapse:collapse;table-layout:fixed;width:auto;min-width:100%;font-size:11px;}
.gantt-table th,.gantt-table td{border:1px solid var(--border);padding:0;white-space:nowrap;}
.row-month th{background:var(--s3);font-size:9px;font-weight:500;text-transform:uppercase;letter-spacing:.8px;padding:3px 6px;text-align:center;color:var(--muted);}
.row-fixed th{background:var(--s2);font-size:8px;font-weight:500;text-transform:uppercase;padding:4px 6px;color:var(--muted);position:relative;}
/* column widths (defaults; overridable per cell via inline width) */
.col-task{width:180px;min-width:60px;}
.col-prio{width:26px;min-width:22px;}
.col-status{width:112px;min-width:60px;}
.col-date{width:52px;min-width:40px;}
.col-days{width:32px;min-width:26px;}
.col-pct{width:88px;min-width:60px;}
.col-resp{width:124px;min-width:60px;border-left:2px solid rgba(244,197,0,.4)!important;}
.col-obs{width:170px;min-width:60px;border-left:2px solid rgba(40,107,55,.35)!important;}
.col-alert{width:90px;min-width:60px;border-left:2px solid rgba(233,92,36,.35)!important;}
.col-delayresp{width:110px;min-width:70px;border-left:2px solid rgba(233,92,36,.20)!important;}
.col-del{width:28px;min-width:24px;}
.th-resp{color:#5C3A1A!important;}
.th-obs{color:var(--done)!important;}

/* Column resize handles */
.col-resizer{position:absolute;right:-3px;top:0;width:6px;height:100%;cursor:col-resize;z-index:5;user-select:none;}
.col-resizer:hover,.col-resizer.dragging{background:linear-gradient(90deg,transparent 0%,var(--accent) 30%,var(--accent) 70%,transparent 100%);}
body.col-resizing{cursor:col-resize!important;user-select:none!important;}
body.col-resizing *{cursor:col-resize!important;user-select:none!important;}
.gantt-table th[data-col-key]{position:relative;}
/* cells */
td.cell-task{padding:5px 8px;position:relative;vertical-align:middle;}
.task-name-inp{font-size:11px;border:none;background:transparent;outline:none;width:100%;border-bottom:1px dashed transparent;word-break:break-word;display:block;line-height:1.3;}
.task-name-inp:focus{border-bottom-color:var(--accent);}
.badge-late{display:inline-block;margin-top:3px;background:var(--red-d);color:#fff;font-size:8px;font-weight:500;padding:1px 5px;border-radius:3px;}
td.cell-prio{text-align:center;padding:4px;vertical-align:middle;}
.prio-dot{width:10px;height:10px;border-radius:50%;display:inline-block;cursor:pointer;}
td.cell-status{padding:2px 4px;vertical-align:middle;}
.status-sel{border:none;background:transparent;font-size:10px;font-weight:500;outline:none;width:100%;padding:3px 6px;border-radius:4px;cursor:pointer;-webkit-appearance:none;appearance:none;text-align:center;}
.s-done{background:rgba(40,107,55,.14);color:var(--done);}
.s-prog{background:rgba(232,148,0,.18);color:#8A5A00;}
.s-wait{background:rgba(184,154,110,.20);color:var(--muted);}
.s-blok{background:rgba(233,92,36,.14);color:var(--red-d);}
td.cell-date{padding:2px 4px;text-align:center;vertical-align:middle;cursor:pointer;}
.date-display{font-size:10px;color:var(--text);display:flex;align-items:center;justify-content:center;gap:3px;padding:2px 0;border-bottom:1px dashed transparent;line-height:1.2;}
.date-display:hover{border-bottom-color:var(--accent);}
.date-display.conc-filled{color:var(--done);font-weight:500;}
.date-display.empty{color:var(--dim);}
.cal-icon{font-size:9px;opacity:.6;}
td.cell-days{padding:2px 4px;vertical-align:middle;}
.days-inp{border:none;background:transparent;font-size:10px;outline:none;width:100%;text-align:center;color:var(--text);}
.days-inp:focus{border-bottom:1px solid var(--accent);}
td.cell-pct{padding:4px 6px;vertical-align:middle;}
.pct-bar{height:4px;border-radius:2px;background:var(--s3);margin-bottom:3px;}
.pct-fill{height:100%;border-radius:2px;}
.pct-inp{border:none;background:transparent;font-size:10px;outline:none;width:100%;text-align:center;}
.pct-inp:focus{border-bottom:1px solid var(--accent);}
td.cell-resp{padding:4px 8px;border-left:2px solid rgba(244,197,0,.4)!important;cursor:pointer;vertical-align:middle;}
td.cell-obs{padding:4px 8px;border-left:2px solid rgba(40,107,55,.35)!important;cursor:pointer;vertical-align:middle;white-space:normal;}
.chip-pill{background:#F2C94C;color:#5C3A1A;font-size:10px;font-weight:500;padding:3px 9px;border-radius:10px;display:inline-block;max-width:112px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;}
.chip-assign,.obs-add{font-size:10px;color:var(--dim);}
.obs-text{font-size:10px;color:var(--muted);white-space:normal;word-break:break-word;line-height:1.35;display:block;}
td.cell-del{text-align:center;padding:2px;vertical-align:middle;}
.th-alert{color:var(--red-d)!important;}
.th-delayresp{color:var(--brown)!important;}
td.cell-alert{padding:4px 6px;border-left:2px solid rgba(233,92,36,.35)!important;text-align:center;vertical-align:middle;}
td.cell-delayresp{padding:2px 4px;border-left:2px solid rgba(233,92,36,.20)!important;vertical-align:middle;}
.badge-atraso{display:inline-block;background:rgba(233,92,36,.15);color:var(--red-d);font-size:9px;font-weight:500;padding:2px 6px;border-radius:8px;white-space:nowrap;}
.badge-no-time{display:inline-block;background:rgba(40,107,55,.12);color:var(--done);font-size:9px;font-weight:500;padding:2px 6px;border-radius:8px;}
.badge-pendente{display:inline-block;color:var(--dim);font-size:9px;font-weight:300;}
.delayresp-sel{border:none;background:transparent;font-size:10px;font-weight:500;outline:none;width:100%;padding:3px 4px;border-radius:4px;cursor:pointer;-webkit-appearance:none;appearance:none;text-align:center;color:var(--text);}
.delayresp-zenatur{background:rgba(40,107,55,.12);color:var(--done);}
.delayresp-dengo{background:rgba(244,197,0,.25);color:var(--brown);}
.delayresp-empty{color:var(--dim);}
.btn-del{background:transparent;border:1px solid rgba(233,92,36,.3);color:var(--red-d);font-size:11px;font-weight:500;padding:2px 5px;border-radius:4px;line-height:1;}
.btn-del:hover{background:rgba(233,92,36,.1);}
.row-late{background:rgba(233,92,36,.04)!important;}
/* calendar */
td.cal-day{position:relative;padding:0;text-align:center;overflow:hidden;}
th.cal-day-h{font-size:8px;padding:2px 0;text-align:center;min-width:20px;max-width:20px;width:20px;}
th.cal-day-h.today-h{font-weight:600;color:var(--brown);background:var(--accent2)!important;}
td.cal-day.weekend{background:rgba(72,49,27,.025);}
td.cal-day.today-col{border-right:2px solid var(--accent2)!important;border-left:1px solid rgba(244,197,0,.5)!important;background:rgba(244,197,0,.13)!important;}
.gantt-bar{position:absolute;top:50%;transform:translateY(-50%);height:11px;border-radius:3px;pointer-events:none;}
.gantt-bar-late{position:absolute;top:50%;transform:translateY(-50%);height:11px;border-radius:0 3px 3px 0;pointer-events:none;background:repeating-linear-gradient(45deg,var(--red-d),var(--red-d) 4px,#C04A1A 4px,#C04A1A 8px);opacity:.85;box-shadow:0 0 0 1px rgba(0,0,0,.08);}

/* ===== FLOAT EDITOR ===== */
#float-editor{position:fixed;z-index:500;background:var(--s1);border:1.5px solid var(--accent2);border-radius:8px;padding:12px;box-shadow:0 8px 32px rgba(72,49,27,.18);min-width:200px;display:none;}
#float-editor label{font-size:9px;text-transform:uppercase;font-weight:500;color:var(--muted);display:block;margin-bottom:5px;}
#float-editor input[type=text],#float-editor input[type=date]{border:1px solid var(--border2);background:var(--bg);padding:6px 8px;border-radius:5px;font-size:12px;width:100%;outline:none;color:var(--text);}
#float-editor input:focus{border-color:var(--accent);}
.float-hint{font-size:9px;color:var(--dim);margin-top:5px;}

/* ===== PRIO SELECTOR ===== */
#prio-sel{position:fixed;z-index:400;background:var(--s1);border:1.5px solid var(--border2);border-radius:8px;padding:6px;box-shadow:0 4px 16px rgba(72,49,27,.15);display:none;min-width:130px;}
.prio-opt{display:flex;align-items:center;gap:8px;padding:6px 8px;border-radius:5px;cursor:pointer;font-size:11px;font-weight:500;}
.prio-opt:hover{background:var(--s2);}

/* ===== MODALS ===== */
.modal-overlay{position:fixed;inset:0;background:rgba(42,31,14,.45);z-index:300;display:flex;align-items:center;justify-content:center;}
.modal-card{background:var(--bg);border:1.5px solid var(--border2);border-radius:12px;padding:24px;min-width:320px;max-width:480px;max-height:90vh;overflow-y:auto;box-shadow:0 12px 48px rgba(72,49,27,.2);}
.modal-title{font-size:16px;font-weight:600;margin-bottom:16px;}
.form-group{margin-bottom:12px;}
.form-group label{font-size:10px;font-weight:500;text-transform:uppercase;color:var(--muted);display:block;margin-bottom:4px;}
.form-group input,.form-group select{width:100%;padding:7px 10px;border:1.5px solid var(--border2);border-radius:6px;background:var(--s1);font-size:12px;color:var(--text);outline:none;}
.form-group input:focus,.form-group select:focus{border-color:var(--accent);}
.modal-btns{display:flex;gap:8px;justify-content:flex-end;margin-top:16px;}
.color-swatches{display:flex;flex-wrap:wrap;gap:6px;margin-bottom:8px;}
.color-swatch{width:22px;height:22px;border-radius:50%;cursor:pointer;border:2px solid transparent;transition:transform .1s;}
.color-swatch:hover,.color-swatch.sel{border-color:var(--text);transform:scale(1.15);}
.tmpl-grid{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-bottom:16px;}
.tmpl-card{border:1.5px solid var(--border2);border-radius:8px;padding:12px;cursor:pointer;text-align:center;transition:all .12s;}
.tmpl-card:hover,.tmpl-card.sel{border-color:var(--accent2);background:rgba(244,197,0,.10);}
.col-type-grid{display:grid;grid-template-columns:1fr 1fr 1fr;gap:8px;margin-bottom:12px;}
.col-type-card{border:1.5px solid var(--border2);border-radius:8px;padding:10px;cursor:pointer;text-align:center;transition:all .12s;}
.col-type-card:hover,.col-type-card.sel{border-color:var(--accent2);background:rgba(244,197,0,.10);}

/* ===== PRESENTATION ===== */
#present{position:fixed;inset:0;z-index:9000;background:var(--bg);display:none;flex-direction:column;}
#pres-header{display:flex;align-items:center;padding:14px 24px;border-bottom:2px solid var(--border);gap:12px;}
#pres-content{flex:1;overflow-y:auto;padding:40px 60px;}
#pres-foot{height:4px;background:var(--s3);}
#pres-prog{height:100%;background:var(--accent);transition:width .3s;}
.slide{display:none;animation:fadein .25s;}
.slide.active{display:block;}
@keyframes fadein{from{opacity:0;transform:translateY(8px);}to{opacity:1;}}
.slide-label{font-size:10px;font-weight:500;text-transform:uppercase;letter-spacing:1.5px;color:var(--muted);margin-bottom:8px;}
.kpi-grid-4{display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-top:24px;}
.kpi-grid-2{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-top:16px;}
.slide-kpi{background:var(--s1);border-radius:8px;padding:16px;text-align:center;}
.slide-kpi-val{font-size:28px;font-weight:600;}
.slide-kpi-label{font-size:10px;color:var(--muted);margin-top:4px;}
.prog-big{height:12px;border-radius:6px;background:var(--s3);margin:16px 0;overflow:hidden;}
.prog-big-fill{height:100%;border-radius:6px;background:var(--accent);}
.slide-row{display:flex;align-items:center;gap:8px;padding:8px 0;border-bottom:1px solid var(--border);}
.slide-name{flex:1;font-size:12px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;}
.final-num{font-size:24px;font-weight:600;color:var(--dim);min-width:40px;}
.final-pill{font-size:10px;font-weight:500;padding:3px 8px;border-radius:10px;}

/* ===== COLORBLIND MODES ===== */
body.cb-deuteranopia{--done:#0072B2;--prog:#E69F00;--blok:#D55E00;--accent:#E69F00;--green:#0072B2;--red-d:#D55E00;}
body.cb-tritanopia  {--done:#009E73;--prog:#CC79A7;--blok:#56B4E9;--accent:#CC79A7;--green:#009E73;--red-d:#56B4E9;}
body.cb-achroma     {--done:#333;--prog:#777;--blok:#555;--accent:#777;--green:#333;--red-d:#555;filter:grayscale(100%);}
body.cb-contrast    {--bg:#000;--s1:#111;--s2:#1A1A1A;--s3:#222;--border:rgba(255,255,255,.15);--border2:rgba(255,255,255,.3);--text:#FFF;--muted:#CCC;--dim:#888;--accent:#FFD700;--done:#00FF88;--red-d:#FF4444;--brown:#FFD700;--prog:#FFD700;--wait:#888;--blok:#FF4444;--green:#00FF88;}


/* ===== DENGO LOGO ===== */
.dengo-logo{background:var(--logo-dengo) center/contain no-repeat;display:inline-block;}
.dengo-logo-tb{width:46px;height:26px;}
.dengo-logo-hub{width:120px;height:80px;margin-bottom:6px;}
.dengo-logo-pres{width:48px;height:26px;}
.dengo-logo-yellow{background-color:var(--accent2);border-radius:6px;padding:2px 6px;}
.hub-credit{font-size:10px;color:var(--dim);text-align:center;margin-top:24px;font-weight:300;letter-spacing:.3px;opacity:.7;}
.hub-credit::before{content:"";display:block;width:40px;height:1px;background:var(--border);margin:0 auto 8px;}

/* ===== MISC ===== */
#toast{position:fixed;bottom:16px;left:50%;transform:translateX(-50%);background:var(--brown);color:#fff;padding:8px 18px;border-radius:20px;font-size:12px;font-weight:500;z-index:9999;display:none;pointer-events:none;}
#loading{position:fixed;inset:0;background:var(--bg);display:flex;align-items:center;justify-content:center;z-index:9999;flex-direction:column;gap:12px;}
::-webkit-scrollbar{width:6px;height:6px;}
::-webkit-scrollbar-track{background:var(--s1);}
::-webkit-scrollbar-thumb{background:var(--dim);border-radius:3px;}
</style>
</head>
<body>

<!-- LOADING -->
<div id="loading"><div class="dengo-logo dengo-logo-yellow" style="width:120px;height:70px;padding:4px 10px;border-radius:8px;"></div><div style="font-size:14px;color:var(--muted);margin-top:14px;font-weight:300">Carregando...</div></div>

<!-- HUB -->
<div id="hub" style="display:none">
  <div class="hub-logo dengo-logo dengo-logo-hub" aria-label="Dengo"></div>
  <div class="hub-title">Gerenciador de Projetos</div>
  <div class="hub-sub">Selecione um projeto ou crie um novo</div>
  <div class="hub-projects" id="hub-projects"></div>
  <button class="btn-new" id="btn-novo-proj">+ Novo Projeto</button>
  <div style="margin-top:16px;display:flex;flex-direction:column;align-items:center;gap:8px;">
    <div class="hub-access">
      <input type="text" id="hub-id-input" placeholder="Cole o ID de um projeto externo…">
      <button class="btn btn-primary" id="btn-access-id">Abrir</button>
    </div>
  </div>
  <div class="hub-footer">💾 Dados salvos localmente · Compartilhe o ID do projeto para colaboração</div>
  <div class="hub-credit">criado por Gabriel Gomes</div>
</div>

<!-- APP -->
<div id="app">
  <div id="topbar">
    <div class="tb-logo dengo-logo-yellow"><span class="dengo-logo dengo-logo-tb" aria-label="Dengo"></span></div>
    <span class="tb-proj" id="tb-proj-name"></span>
    <span class="tb-id" id="tb-proj-id"></span>
    <div class="tb-right">
      <div class="tb-group">
        <span class="chip" id="chip-date">▶ --</span>
        <span class="chip"><span class="sync-dot pulse" id="sync-dot"></span><span id="sync-label">Sincronizado</span></span>
      </div>
      <div class="tb-sep"></div>
      <div class="tb-group">
        <button class="btn btn-ghost" id="tb-add-task">+ Tarefa</button>
        <button class="btn btn-primary" id="tb-add-group">+ Grupo</button>
        <button class="btn btn-ghost" id="tb-add-col">+ Coluna</button>
      </div>
      <div class="tb-sep"></div>
      <div class="tb-group">
        <button class="btn btn-ghost" id="tb-csv">⬇ CSV</button>
        <button class="btn btn-success" id="tb-xlsx">⬇ Excel</button>
        <button class="btn btn-brown" id="tb-present">▶ Apresentação</button>
      </div>
      <div class="tb-sep"></div>
      <div class="tb-group">
        <button class="btn btn-ghost" id="tb-share">🔗 Compartilhar</button>
        <button class="btn btn-ghost" id="tb-pin">🔑 PIN</button>
        <button class="btn btn-ghost" id="tb-a11y" title="Modo daltônico">👁 Daltonismo</button>
        <button class="btn btn-ghost" id="tb-reset-cols" title="Resetar larguras das colunas">↔ Colunas</button>
      </div>
      <div class="tb-sep"></div>
      <div class="tb-group">
        <button class="btn btn-success" id="tb-save">💾 Salvar</button>
        <button class="btn btn-danger" id="tb-del-proj">🗑 Excluir</button>
        <button class="btn btn-warn" id="tb-hub">⬅ Hub</button>
      </div>
    </div>
  </div>

  <div id="filterbar">
    <span style="font-size:10px;font-weight:500;color:var(--muted);margin-right:4px;">STATUS</span>
    <button class="filter-btn" data-sf="all">Todos</button>
    <button class="filter-btn" data-sf="EM ANDAMENTO">Em Andamento</button>
    <button class="filter-btn" data-sf="REALIZADO">Realizadas</button>
    <button class="filter-btn" data-sf="NÃO INICIADO">Pendentes</button>
    <button class="filter-btn" data-sf="BLOQUEADO">Bloqueadas</button>
    <button class="filter-btn" data-sf="ATRASADA">⚠ Atrasadas</button>
    <div class="filter-sep"></div>
    <span style="font-size:10px;font-weight:500;color:var(--muted);margin-right:4px;">GRUPO</span>
    <div id="group-filters"></div>
  </div>

  <div id="kpi-strip">
    <div class="kpi-card"><div class="kpi-label">Progresso Geral</div><div class="kpi-val" id="k-prog" style="color:var(--accent)">0%</div><div class="kpi-sub" id="k-prog-sub">--</div><div class="kpi-bar"><div class="kpi-bar-fill" id="k-prog-bar" style="background:var(--accent)"></div></div></div>
    <div class="kpi-card"><div class="kpi-label">Concluídas</div><div class="kpi-val" id="k-done" style="color:var(--done)">0</div><div class="kpi-sub">tarefas realizadas</div><div class="kpi-bar"><div class="kpi-bar-fill" id="k-done-bar" style="background:var(--done)"></div></div></div>
    <div class="kpi-card"><div class="kpi-label">Em Andamento</div><div class="kpi-val" id="k-prog2" style="color:var(--accent)">0</div><div class="kpi-sub">tarefas ativas</div><div class="kpi-bar"><div class="kpi-bar-fill" id="k-prog2-bar" style="background:var(--accent)"></div></div></div>
    <div class="kpi-card"><div class="kpi-label">Pendentes</div><div class="kpi-val" id="k-pend" style="color:var(--muted)">0</div><div class="kpi-sub">aguardando início</div><div class="kpi-bar"><div class="kpi-bar-fill" id="k-pend-bar" style="background:var(--muted)"></div></div></div>
    <div class="kpi-card"><div class="kpi-label">⚠ Atrasadas</div><div class="kpi-val" id="k-late" style="color:var(--blok)">0</div><div class="kpi-sub">passaram do prazo</div><div class="kpi-bar"><div class="kpi-bar-fill" id="k-late-bar" style="background:var(--blok)"></div></div></div>
    <div class="kpi-card"><div class="kpi-label">Dias p/ Prazo</div><div class="kpi-val" id="k-days" style="color:var(--brown)">--</div><div class="kpi-sub" id="k-days-sub">--</div><div class="kpi-bar"><div class="kpi-bar-fill" id="k-days-bar" style="background:var(--brown)"></div></div></div>
  </div>

  <div id="main-scroll"></div>
</div>

<!-- FLOAT EDITOR (texto + data) -->
<div id="float-editor">
  <label id="fe-label">Campo</label>
  <input type="text" id="fe-text" style="display:none">
  <input type="date" id="fe-date" style="display:none">
  <div class="float-hint">Enter confirma · Esc cancela</div>
</div>

<!-- PRIO SELECTOR -->
<div id="prio-sel">
  <div class="prio-opt" data-prio="ALTA"><span class="prio-dot" style="background:#E95C24"></span>Alta</div>
  <div class="prio-opt" data-prio="MÉDIA"><span class="prio-dot" style="background:#FFA600"></span>Média</div>
  <div class="prio-opt" data-prio="BAIXA"><span class="prio-dot" style="background:#286B37"></span>Baixa</div>
  <div class="prio-opt" data-prio=""><span class="prio-dot" style="background:#C4A882"></span>Sem prioridade</div>
</div>

<!-- MODALS -->
<div id="modals"></div>

<!-- TOAST -->
<div id="toast"></div>

<!-- PRESENTATION -->
<div id="present">
  <div id="pres-header">
    <span class="dengo-logo dengo-logo-pres dengo-logo-yellow" style="padding:3px 6px;border-radius:6px"></span>
    <span style="font-weight:600;font-size:15px" id="pres-proj-name"></span>
    <span style="color:var(--muted);font-size:12px" id="pres-date-label"></span>
    <div style="margin-left:auto;display:flex;align-items:center;gap:10px;">
      <span style="font-size:12px;color:var(--muted)" id="pres-counter">1/1</span>
      <button class="btn btn-ghost" id="pres-prev">◀</button>
      <button class="btn btn-ghost" id="pres-next">▶</button>
      <button class="btn btn-danger" id="pres-close">✕ Sair</button>
    </div>
  </div>
  <div id="pres-content"></div>
  <div id="pres-foot"><div id="pres-prog" style="width:0%"></div></div>
</div>

<script>
// =============================================
// CONSTANTS & STATE
// =============================================
const TODAY = new Date(); TODAY.setHours(0,0,0,0);
const todayStr = TODAY.toISOString().slice(0,10);

const PRIO_COLORS = {ALTA:'#E95C24',MÉDIA:'#FFA600',BAIXA:'#286B37','':'#C4A882'};
const STATUS_CLS  = {'REALIZADO':'s-done','EM ANDAMENTO':'s-prog','NÃO INICIADO':'s-wait','BLOQUEADO':'s-blok'};
const DATE_FIELDS = ['inicio','fim','conc'];
const FLOAT_LABEL = {resp:'Responsável',obs:'Observação',inicio:'Data de Início',fim:'Data Fim Prev.',conc:'Data de Conclusão'};
const STATUS_FILTER_COLOR = {all:['var(--brown)','#fff'],'EM ANDAMENTO':['var(--prog)','#48311B'],REALIZADO:['var(--done)','#fff'],'NÃO INICIADO':['var(--wait)','#fff'],BLOQUEADO:['var(--blok)','#fff'],ATRASADA:['var(--blok)','#fff']};
const SWATCHES = ['#FFD200','#FFA600','#286B37','#E95C24','#48311B','#F1A6B5','#0072B2','#7C3AED','#0891B2','#65A30D'];
const TEMPLATES = [
  {id:'blank',icon:'📋',name:'Em branco',desc:'Sem grupos',groups:{}},
  {id:'export',icon:'🚢',name:'Exportação',desc:'IDEIAS · PROJETOS · FOLLOW UP',groups:{IDEIAS:{color:'#2563EB'},'PROJETOS EM ANDAMENTO':{color:'#FFA600'},'FOLLOW UP':{color:'#286B37'},BRAINSTORMING:{color:'#7C3AED'}}},
  {id:'sap',icon:'⚙️',name:'Migração SAP',desc:'TESTES · CUTOVER · GO-LIVE',groups:{TESTES:{color:'#0891B2'},CUTOVER:{color:'#EA580C'},'GO-LIVE':{color:'#286B37'}}},
  {id:'pm',icon:'📦',name:'Gestão de Projeto',desc:'PLANEJAMENTO · EXECUÇÃO · ENCERRAMENTO',groups:{PLANEJAMENTO:{color:'#2563EB'},EXECUÇÃO:{color:'#FFA600'},ENCERRAMENTO:{color:'#286B37'}}}
];

let state = {
  projects: {},
  currentId: null,
  statusFilter: 'all',
  groupFilter: 'all',
  dirty: false,
  syncTimer: null,
  pushTimer: null,
  lastPulledVersion: 0,
  prioTaskId: null,
  floatTaskId: null,
  floatField: null,
  presSlides: [],
  presIdx: 0,
  groupMap: {},   // idx -> groupName (safe, avoids HTML encoding issues)
  selColType: 'text',
  selTmpl: 'blank',
  pinAttempts: 0,
  pinBlocked: false,
  colWidths: (()=>{ try{ return JSON.parse(localStorage.getItem('dengo_col_widths')||'{}'); }catch(e){ return {}; } })()
};

const COL_DEFAULTS = {task:180,prio:26,status:112,inicio:52,fim:52,conc:52,days:32,pct:88,resp:124,obs:170,alert:90,delayresp:110,del:28};
const COL_MIN = {task:60,prio:22,status:60,inicio:40,fim:40,conc:40,days:26,pct:60,resp:60,obs:60,alert:60,delayresp:70,del:24,'extra:':60};

function getColMin(key){
  if(key.startsWith('extra:')) return COL_MIN['extra:'];
  return COL_MIN[key]||40;
}
function saveColWidths(){
  try{ localStorage.setItem('dengo_col_widths',JSON.stringify(state.colWidths)); }catch(e){}
}
function resetAllColWidths(){
  state.colWidths = {};
  saveColWidths();
  applyColWidths();
  toast('Larguras das colunas resetadas.');
}

// CSS-safe identifier (used as part of selectors and data attrs)
function tableIdFor(gname){
  // simple slug — preserves uniqueness, removes anything that breaks attribute selectors
  return 'tbl_' + gname.replace(/[^a-zA-Z0-9_]/g,'_').toLowerCase();
}

function applyColWidths(){
  // Build per-table-id CSS rules — each Gantt table (one per group) is independent
  let css='';
  for(const tableId in state.colWidths){
    const cols = state.colWidths[tableId];
    if(!cols) continue;
    for(const key in cols){
      const w = cols[key];
      // Target only descendants of the specific table-id
      const colSel = `[data-col-key="${key.replace(/"/g,'\\"')}"]`;
      css += `.gantt-table[data-table-id="${tableId}"] ${colSel}{width:${w}px!important;min-width:${w}px!important;max-width:${w}px!important;}\n`;
    }
  }
  let styleEl = document.getElementById('col-widths-style');
  if(!styleEl){
    styleEl = document.createElement('style');
    styleEl.id = 'col-widths-style';
    document.head.appendChild(styleEl);
  }
  styleEl.textContent = css;
}

function installResizers(){
  document.querySelectorAll('.gantt-table th[data-col-key]').forEach(th=>{
    if(th.querySelector('.col-resizer')) return; // already installed
    const table = th.closest('.gantt-table');
    const tableId = table?.dataset.tableId;
    if(!tableId) return;

    const handle = document.createElement('div');
    handle.className = 'col-resizer';
    handle.title = 'Arraste para redimensionar · Duplo-clique para resetar';
    th.appendChild(handle);

    // Drag to resize — only this table's column
    handle.addEventListener('mousedown', e=>{
      e.preventDefault();
      e.stopPropagation();
      const key = th.dataset.colKey;
      const startX = e.clientX;
      const startW = th.getBoundingClientRect().width;
      const min = getColMin(key);
      handle.classList.add('dragging');
      document.body.classList.add('col-resizing');

      if(!state.colWidths[tableId]) state.colWidths[tableId] = {};

      const onMove = ev=>{
        const newW = Math.max(min, Math.round(startW + (ev.clientX - startX)));
        state.colWidths[tableId][key] = newW;
        applyColWidths();
      };
      const onUp = ()=>{
        document.removeEventListener('mousemove', onMove);
        document.removeEventListener('mouseup', onUp);
        handle.classList.remove('dragging');
        document.body.classList.remove('col-resizing');
        saveColWidths();
      };
      document.addEventListener('mousemove', onMove);
      document.addEventListener('mouseup', onUp);
    });

    // Double-click to reset just this column of this table
    handle.addEventListener('dblclick', e=>{
      e.preventDefault(); e.stopPropagation();
      const key = th.dataset.colKey;
      if(state.colWidths[tableId]){
        delete state.colWidths[tableId][key];
        if(Object.keys(state.colWidths[tableId]).length===0) delete state.colWidths[tableId];
      }
      applyColWidths();
      saveColWidths();
      toast('Largura resetada');
    });
  });
}

// =============================================
// UTILS
// =============================================
const esc  = s => s==null?'':String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');
const fmtDate = d => { if(!d)return'--'; const p=d.split('-'); return p[2]+'/'+p[1]; };
const genId = () => 'proj_'+Date.now().toString(36)+'_'+Math.random().toString(36).slice(2,6);
const hashPin = p => { let h=5381; for(let i=0;i<p.length;i++) h=((h<<5)+h)+p.charCodeAt(i); return(h>>>0).toString(16); };
const calcDays = (s,e) => (!s||!e)?0:Math.max(0,Math.round((new Date(e)-new Date(s))/86400000));
const addDays  = (s,n) => { const d=new Date(s); d.setDate(d.getDate()+n); return d.toISOString().slice(0,10); };
const isLate   = t => t.status!=='REALIZADO' && t.fim && t.fim < todayStr;
// Atraso na conclusão: tarefa REALIZADA com conc > fim previsto
const getDelay = t => {
  if(t.status!=='REALIZADO' || !t.conc || !t.fim) return 0;
  return Math.max(0, Math.round((new Date(t.conc) - new Date(t.fim)) / 86400000));
};

// Ordena tarefas in-place: por grupo, depois por data de início (mais antiga primeiro),
// depois por data de fim, depois por id como desempate estável
function sortTasks(p){
  if(!p || !p.tasks) return;
  p.tasks.sort((a,b)=>{
    // 1. Mesmo grupo fica junto
    if(a.grupo!==b.grupo) return (a.grupo||'').localeCompare(b.grupo||'');
    // 2. Data de início (mais antiga primeiro). Vazios vão pro fim
    const ai = a.inicio || '9999-12-31';
    const bi = b.inicio || '9999-12-31';
    if(ai!==bi) return ai.localeCompare(bi);
    // 3. Fim previsto
    const af = a.fim || '9999-12-31';
    const bf = b.fim || '9999-12-31';
    if(af!==bf) return af.localeCompare(bf);
    // 4. Desempate estável por id
    return (a.id||0)-(b.id||0);
  });
}
const toast    = msg => { const el=document.getElementById('toast'); el.textContent=msg; el.style.display='block'; setTimeout(()=>el.style.display='none',2400); };
const markDirty = () => { state.dirty = true; scheduleAutoPush(); };
const getProject = () => state.projects[state.currentId];
const getTask    = id => getProject()?.tasks.find(t=>t.id===id);

// =============================================
// STORAGE  (Firebase Realtime Database + localStorage cache/fallback)
// =============================================
const SYNC_INTERVAL_MS = 4000;   // pull cycle (fallback quando sem listener live)
const PUSH_DEBOUNCE_MS = 700;    // push delay after edit

let _remoteBroken = false;

// Firebase keys não podem conter . # $ [ ] / — o id já é seguro (proj_xxx), mas sanitizamos por garantia
const fbKey = id => String(id).replace(/[.#$\[\]\/]/g,'_');

async function storageSave(id, data) {
  data.updatedAt = new Date().toISOString();
  data.version   = (data.version||0) + 1;
  // Remove campos efêmeros antes de persistir
  const clean = JSON.parse(JSON.stringify(data));
  delete clean._saveStatus;

  // 1) localStorage sempre primeiro (cache local instantâneo, nunca perde)
  let localOk=false;
  try { localStorage.setItem('proj_'+id, JSON.stringify(clean)); localOk=true; }
  catch(e){ console.warn('localStorage save error', e); }

  // 2) Firebase (compartilhado entre todos os usuários)
  let remoteOk=false;
  if(fbReady && fbDB && !_remoteBroken){
    try {
      await fbDB.ref('projects/'+fbKey(id)).set(clean);
      remoteOk=true;
    } catch(e){
      console.warn('[Firebase] save falhou, usando local:', e?.message||e);
      _remoteBroken=true; setTimeout(()=>{ _remoteBroken=false; }, 20000);
    }
  }

  data._saveStatus = remoteOk ? 'remote' : (localOk ? 'local' : 'fail');
  return data;
}

async function storageLoad(id) {
  // Firebase primeiro (versão compartilhada mais atual)
  if(fbReady && fbDB && !_remoteBroken){
    try {
      const snap = await fbDB.ref('projects/'+fbKey(id)).get();
      if(snap.exists()){
        const val = normalizeProject(snap.val());
        // atualiza cache local
        try { localStorage.setItem('proj_'+id, JSON.stringify(val)); }catch(e){}
        return val;
      }
    } catch(e){
      console.warn('[Firebase] load falhou, usando local:', e?.message||e);
      _remoteBroken=true; setTimeout(()=>{ _remoteBroken=false; }, 20000);
    }
  }
  // fallback localStorage
  try { const r=localStorage.getItem('proj_'+id); return r?JSON.parse(r):null; }
  catch(e){ return null; }
}

async function storageDelete(id) {
  try { localStorage.removeItem('proj_'+id); } catch(e){}
  if(fbReady && fbDB && !_remoteBroken){
    try { await fbDB.ref('projects/'+fbKey(id)).remove(); } catch(e){}
  }
}

// Listener em tempo real — chama callback sempre que o projeto muda no servidor
let _liveRef = null;
function subscribeLive(id, callback){
  unsubscribeLive();
  if(!(fbReady && fbDB)) return false;
  try {
    _liveRef = fbDB.ref('projects/'+fbKey(id));
    _liveRef.on('value', snap=>{
      if(snap.exists()) callback(snap.val());
    });
    return true;
  } catch(e){ console.warn('[Firebase] subscribe falhou:', e?.message||e); return false; }
}
function unsubscribeLive(){
  if(_liveRef){ try{ _liveRef.off(); }catch(e){} _liveRef=null; }
}

const hubList    = () => { try{ return JSON.parse(localStorage.getItem('dengo_hub')||'[]'); }catch(e){return[];} };
const hubSave    = l => localStorage.setItem('dengo_hub',JSON.stringify(l));
const hubAdd     = id => { const l=hubList(); if(!l.includes(id)){l.push(id);hubSave(l);} };
const hubRemove  = id => hubSave(hubList().filter(x=>x!==id));

// Compara duas versões: retorna true se "a" é mais recente que "b"
function isNewer(a, b){
  if(!b) return true;
  if(!a) return false;
  // version tem prioridade (mais confiável que timestamp em colaboração)
  if(typeof a.version==='number' && typeof b.version==='number'){
    if(a.version!==b.version) return a.version > b.version;
  }
  // empate de version: usa updatedAt
  const ta = a.updatedAt||'';
  const tb = b.updatedAt||'';
  return ta > tb;
}

// Firebase remove arrays vazios e chaves undefined — restaura a forma esperada
function normalizeProject(p){
  if(!p || typeof p!=='object') return p;
  if(!Array.isArray(p.tasks))    p.tasks = p.tasks ? Object.values(p.tasks) : [];
  if(!Array.isArray(p.extraCols))p.extraCols = p.extraCols ? Object.values(p.extraCols) : [];
  if(!p.groups || typeof p.groups!=='object') p.groups = {};
  if(typeof p.nextId!=='number') p.nextId = (p.tasks.reduce((m,t)=>Math.max(m,t.id||0),0)||0)+1;
  // cada tarefa precisa de extra:{} e campos mínimos
  p.tasks.forEach(t=>{ if(!t.extra||typeof t.extra!=='object') t.extra={}; });
  return p;
}

// =============================================
// DEFAULT PROJECT DATA
// =============================================
function makeDefaultProject(){
  return {
    id:'dengo_mig_estoque_v2',
    name:'Migração de Estoque · Dengo',
    desc:'Projeto de migração e inventário de estoque Dengo',
    color:'#FFA600', pinHash:'',
    groups:{ASTORE:{color:'#FFD200'},ZENATUR:{color:'#286B37'},RODOLUKI:{color:'#E95C24'},ACOMPANHAMENTO:{color:'#48311B'}},
    extraCols:[], nextId:50,
    createdAt:new Date().toISOString(), updatedAt:new Date().toISOString(),
    tasks:[
      {id:1, grupo:'ASTORE',prio:'ALTA',  tarefa:'CRIAÇÃO DE LOGINS (LOJAS / ADM / ERICK)',        inicio:'2026-05-05',fim:'2026-05-07',conc:'2026-05-08',status:'REALIZADO',   pct:100,resp:'ASTORE E LIVIA',  obs:'',extra:{}},
      {id:2, grupo:'ASTORE',prio:'ALTA',  tarefa:'ANALISAR A DISPONIBILIDADE DOS ITENS NO PORTAL', inicio:'2026-05-08',fim:'2026-05-11',conc:'',           status:'REALIZADO',   pct:100,resp:'BRUNO E GABRIEL',obs:'açúcar e adoçante não estão no portal',extra:{}},
      {id:3, grupo:'ASTORE',prio:'MÉDIA', tarefa:'ANALISAR A FACILIDADE DE NAVEGAÇÃO DO PORTAL',   inicio:'2026-05-08',fim:'2026-05-08',conc:'2026-05-12',status:'REALIZADO',   pct:100,resp:'BRUNO',           obs:'descrição dos itens não intuitivas no portal da astore',extra:{}},
      {id:4, grupo:'ASTORE',prio:'ALTA',  tarefa:'TESTAR A CRIAÇÃO DE PEDIDOS NO PORTAL',           inicio:'2026-05-11',fim:'2026-05-12',conc:'2026-05-12',status:'REALIZADO',   pct:100,resp:'BRUNO',           obs:'',extra:{}},
      {id:5, grupo:'ASTORE',prio:'ALTA',  tarefa:'CRIAR PEDIDO MÍNIMO PARA CADA LOJA',              inicio:'2026-05-11',fim:'2026-05-15',conc:'',           status:'EM ANDAMENTO',pct:0,  resp:'BRUNO',           obs:'COBRAR RC DO TIME DE COMPRAS',extra:{}},
      {id:6, grupo:'ASTORE',prio:'ALTA',  tarefa:'COLOCAR O TIME FINANCEIRO E FISCAL NO PORTAL',   inicio:'2026-05-11',fim:'2026-05-13',conc:'2026-05-15',status:'REALIZADO',   pct:100,resp:'GABRIEL',         obs:'',extra:{}},
      {id:7, grupo:'ASTORE',prio:'ALTA',  tarefa:'SOLICITAR AJUSTES NO PORTAL',                     inicio:'2026-05-11',fim:'2026-05-11',conc:'2026-05-12',status:'REALIZADO',   pct:100,resp:'BRUNO E GABRIEL', obs:'',extra:{}},
      {id:8, grupo:'ASTORE',prio:'MÉDIA', tarefa:'ACOMPANHAR A ATUALIZAÇÃO DO PORTAL',              inicio:'2026-05-12',fim:'2026-05-12',conc:'2026-05-14',status:'REALIZADO',   pct:100,resp:'BRUNO',           obs:'',extra:{}},
      {id:9, grupo:'ASTORE',prio:'ALTA',  tarefa:'ACOMPANHAR LEADTIME DE ENTREGA',                  inicio:'2026-05-13',fim:'2026-05-20',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'BRUNO E GABRIEL', obs:'',extra:{}},
      {id:10,grupo:'ASTORE',prio:'ALTA',  tarefa:'HOMOLOGAÇÃO FISCAL DOS NOVOS PEDIDOS',            inicio:'2026-05-13',fim:'2026-05-15',conc:'',           status:'EM ANDAMENTO',pct:0,  resp:'FISCAL',          obs:'',extra:{}},
      {id:11,grupo:'ASTORE',prio:'MÉDIA', tarefa:'TREINAMENTO GERENTES E FRANQUEADOS',              inicio:'2026-05-15',fim:'2026-05-25',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'ERICK E BRUNO',   obs:'',extra:{}},
      {id:12,grupo:'ASTORE',prio:'ALTA',  tarefa:'VALIDAR PROCESSO DE FRANQUIAS',                   inicio:'2026-05-18',fim:'2026-05-29',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'BRUNO E GABRIEL', obs:'PROCESSO DE EMISSÃO DE NOTAS E FATURAMENTO ETC',extra:{}},
      {id:13,grupo:'ASTORE',prio:'ALTA',  tarefa:'GO LIVE ASTORE',                                  inicio:'2026-06-01',fim:'2026-06-24',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'ERICK',           obs:'',extra:{}},
      {id:37,grupo:'ZENATUR',prio:'ALTA', tarefa:'ATUALIZAR O MRP',                                  inicio:'2026-05-05',fim:'2026-05-06',conc:'2026-05-06',status:'REALIZADO',   pct:100,resp:'BRUNO',           obs:'',extra:{}},
      {id:14,grupo:'ZENATUR',prio:'ALTA', tarefa:'ATUALIZAR A PLANILHA DE PEDIDOS',                  inicio:'2026-05-05',fim:'2026-05-06',conc:'2026-05-06',status:'REALIZADO',   pct:100,resp:'BRUNO',           obs:'',extra:{}},
      {id:15,grupo:'ZENATUR',prio:'ALTA', tarefa:'ENVIAR PLANILHA PARA OS REGIONAIS',                inicio:'2026-05-05',fim:'2026-05-06',conc:'2026-05-06',status:'REALIZADO',   pct:100,resp:'BRUNO',           obs:'',extra:{}},
      {id:16,grupo:'ZENATUR',prio:'MÉDIA',tarefa:'VALIDAR RETORNO DAS PLANILHAS',                    inicio:'2026-05-11',fim:'2026-05-13',conc:'2026-05-12',status:'REALIZADO',   pct:100,resp:'BRUNO E GABRIEL', obs:'',extra:{}},
      {id:18,grupo:'ZENATUR',prio:'MÉDIA',tarefa:'ENVIAR PLANILHA PARA ZENATUR',                     inicio:'2026-05-13',fim:'2026-05-13',conc:'2026-05-13',status:'REALIZADO',   pct:100,resp:'BRUNO',           obs:'',extra:{}},
      {id:19,grupo:'ZENATUR',prio:'ALTA', tarefa:'SEPARAÇÕES DOS PEDIDOS E DEVOLUÇÃO DE NF',         inicio:'2026-05-14',fim:'2026-05-20',conc:'',           status:'EM ANDAMENTO',pct:0,  resp:'ZENATUR',         obs:'',extra:{}},
      {id:20,grupo:'ZENATUR',prio:'MÉDIA',tarefa:'COMUNICAÇÃO FORMAL A ZENATUR',                     inicio:'2026-05-15',fim:'2026-05-15',conc:'2026-05-15',status:'REALIZADO',   pct:100,resp:'CAIO',            obs:'',extra:{}},
      {id:38,grupo:'ZENATUR',prio:'MÉDIA',tarefa:'ALINHAR EXPECTATIVAS DE TEMPO',                    inicio:'2026-05-20',fim:'2026-05-20',conc:'2026-05-20',status:'REALIZADO',   pct:100,resp:'GABRIEL',         obs:'',extra:{}},
      {id:24,grupo:'ZENATUR',prio:'BAIXA',tarefa:'INFORME EQUIPE OPERACIONAL E ADM',                 inicio:'2026-05-20',fim:'2026-05-20',conc:'',           status:'EM ANDAMENTO',pct:0,  resp:'ZENATUR',         obs:'',extra:{}},
      {id:25,grupo:'ZENATUR',prio:'ALTA', tarefa:'RESPONSÁVEL LEGAL PELA ASSINATURA DO INVENTÁRIO',  inicio:'2026-05-20',fim:'2026-05-20',conc:'',           status:'EM ANDAMENTO',pct:0,  resp:'ZENATUR',         obs:'',extra:{}},
      {id:21,grupo:'ZENATUR',prio:'MÉDIA',tarefa:'PLANEJAMENTO DO INVENTÁRIO',                       inicio:'2026-05-20',fim:'2026-05-22',conc:'',           status:'EM ANDAMENTO',pct:0,  resp:'GABRIEL',         obs:'',extra:{}},
      {id:26,grupo:'ZENATUR',prio:'ALTA', tarefa:'GARANTIR QUE NÃO TEMOS PENDÊNCIA FISCAL',         inicio:'2026-05-22',fim:'2026-05-22',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'GABRIEL E ZENATUR',obs:'',extra:{}},
      {id:17,grupo:'ZENATUR',prio:'ALTA', tarefa:'DESENVOLVER A NOVA POLÍTICA DE DISTRIBUIÇÃO',     inicio:'2026-05-25',fim:'2026-05-29',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'BRUNO',           obs:'',extra:{}},
      {id:39,grupo:'ZENATUR',prio:'MÉDIA',tarefa:'EXECUÇÃO DE INVENTARIO',                           inicio:'2026-05-26',fim:'2026-05-29',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'GABRIEL',         obs:'',extra:{}},
      {id:27,grupo:'ZENATUR',prio:'ALTA', tarefa:'GARANTIR AJUSTE DENTRO DO PERÍODO DETERMINADO',   inicio:'2026-05-28',fim:'2026-05-30',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'GABRIEL E ZENATUR',obs:'',extra:{}},
      {id:28,grupo:'ZENATUR',prio:'MÉDIA',tarefa:'FECHAMENTO DE VOLUME DE PALETS',                   inicio:'2026-06-01',fim:'2026-06-05',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'GABRIEL',         obs:'',extra:{}},
      {id:29,grupo:'ZENATUR',prio:'MÉDIA',tarefa:'INICIO DO MOVING',                                 inicio:'2026-06-08',fim:'2026-06-12',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'GABRIEL E ZENATUR',obs:'',extra:{}},
      {id:32,grupo:'RODOLUKI',prio:'ALTA', tarefa:'DEFINIR DATA DE INÍCIO DA MIGRAÇÃO',             inicio:'2026-05-28',fim:'2026-05-29',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'GABRIEL',         obs:'',extra:{}},
      {id:33,grupo:'RODOLUKI',prio:'ALTA', tarefa:'QUAL A CAPACIDADE DE RECEBIMENTO DIÁRIO',        inicio:'2026-05-29',fim:'2026-05-30',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'GABRIEL',         obs:'',extra:{}},
      {id:34,grupo:'RODOLUKI',prio:'MÉDIA',tarefa:'QUANTO TEMPO PARA DAR ENTRADA E LIBERAR SALDO', inicio:'2026-05-29',fim:'2026-05-30',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'GABRIEL',         obs:'',extra:{}},
      {id:35,grupo:'RODOLUKI',prio:'MÉDIA',tarefa:'QUANTOS CARROS POR DIA',                         inicio:'2026-05-30',fim:'2026-06-02',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'GABRIEL',         obs:'',extra:{}},
      {id:31,grupo:'RODOLUKI',prio:'ALTA', tarefa:'CUSTO MÉDIO DE MOVING',                          inicio:'2026-06-01',fim:'2026-06-05',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'GABRIEL',         obs:'',extra:{}},
      {id:36,grupo:'ACOMPANHAMENTO',prio:'ALTA',tarefa:'CUSTOS GERADOS NA MOVIMENTAÇÃO',            inicio:'2026-06-08',fim:'2026-06-12',conc:'',           status:'NÃO INICIADO',pct:0,  resp:'GABRIEL',         obs:'',extra:{}}
    ]
  };
}

// =============================================
// INIT
// =============================================
async function init(){
  const list = hubList();
  if(!list.includes('dengo_mig_estoque_v2')){ list.unshift('dengo_mig_estoque_v2'); hubSave(list); }
  for(const id of list){
    // Carrega a versão salva (mais recente). Só usa o template default se nunca foi salvo.
    let p = await storageLoad(id);
    if(!p && id==='dengo_mig_estoque_v2'){
      // Semeia o projeto Dengo apenas se NÃO existir no Firebase (evita sobrescrever dados do time)
      p = makeDefaultProject();
      if(fbReady && fbDB){
        try {
          // transação atômica: só grava se o nó ainda estiver vazio
          const ref = fbDB.ref('projects/'+fbKey(id));
          await ref.transaction(current => current===null ? p : undefined);
          // recarrega o que ficou efetivamente no servidor (pode ser de outro usuário que semeou antes)
          const reloaded = await storageLoad(id);
          if(reloaded) p = reloaded;
        } catch(e){
          console.warn('[Firebase] seed falhou, usando local:', e?.message||e);
          await storageSave(id, p);
        }
      } else {
        await storageSave(id, p);
      }
    }
    if(p){
      normalizeProject(p);
      sortTasks(p);
      state.projects[id]=p;
    }
  }
  document.getElementById('loading').style.display='none';
  showHub();
  bindStaticEvents();
}

// =============================================
// STATIC EVENT BINDING (once, on init)
// =============================================
function bindStaticEvents(){
  // Hub
  document.getElementById('btn-novo-proj').addEventListener('click', showNewProjectModal);
  document.getElementById('btn-access-id').addEventListener('click', accessById);

  // Topbar
  document.getElementById('tb-add-task').addEventListener('click', ()=>showNewTaskModal());
  document.getElementById('tb-add-group').addEventListener('click', showNewGroupModal);
  document.getElementById('tb-add-col').addEventListener('click', showNewColModal);
  document.getElementById('tb-csv').addEventListener('click', exportCSV);
  document.getElementById('tb-xlsx').addEventListener('click', exportXLSX);
  document.getElementById('tb-present').addEventListener('click', startPresentation);
  document.getElementById('tb-share').addEventListener('click', shareProject);
  document.getElementById('tb-pin').addEventListener('click', showPinModal);
  document.getElementById('tb-a11y').addEventListener('click', cycleColorblindMode);
  document.getElementById('tb-reset-cols').addEventListener('click', resetAllColWidths);
  document.getElementById('tb-save').addEventListener('click', saveProject);
  document.getElementById('tb-del-proj').addEventListener('click', deleteCurrentProject);
  document.getElementById('tb-hub').addEventListener('click', goHub);

  // Filter bar status buttons
  document.querySelectorAll('[data-sf]').forEach(btn=>{
    btn.addEventListener('click', ()=> setStatusFilter(btn.dataset.sf));
  });
  setStatusFilter('all'); // activate default

  // Prio selector options
  document.querySelectorAll('.prio-opt').forEach(opt=>{
    opt.addEventListener('click', ()=> applyPrio(opt.dataset.prio));
  });

  // Presentation controls
  document.getElementById('pres-prev').addEventListener('click', ()=>moveSlide(-1));
  document.getElementById('pres-next').addEventListener('click', ()=>moveSlide(1));
  document.getElementById('pres-close').addEventListener('click', closePresentation);

  // Global click: close floats/prio
  document.addEventListener('click', globalClickHandler);
  // Keyboard
  document.addEventListener('keydown', globalKeyHandler);

  // Global delegated events for dynamic content
  document.getElementById('main-scroll').addEventListener('click',  mainClickDelegate);
  document.getElementById('main-scroll').addEventListener('change', mainChangeDelegate);
  document.getElementById('main-scroll').addEventListener('input',  mainInputDelegate);
  document.getElementById('main-scroll').addEventListener('keydown',mainKeyDelegate);

  // Salva edições pendentes ao fechar/atualizar a aba (síncrono via localStorage fallback)
  window.addEventListener('beforeunload', ()=>{
    if(state.currentId && state.dirty){
      try{
        const p = state.projects[state.currentId];
        p.updatedAt = new Date().toISOString();
        p.version = (p.version||0)+1;
        // localStorage é síncrono, garante gravação antes de fechar
        localStorage.setItem('proj_'+state.currentId, JSON.stringify(p));
      }catch(e){}
    }
  });

  // Ao voltar o foco para a aba, puxa a versão mais recente imediatamente
  document.addEventListener('visibilitychange', ()=>{
    if(document.visibilityState==='visible' && state.currentId && !state.dirty){
      pullRemote();
    }
  });
}

// =============================================
// HUB
// =============================================
function showHub(){
  document.getElementById('hub').style.display='flex';
  document.getElementById('app').style.display='none';
  renderHub();
}

function renderHub(){
  const list = hubList();
  const cont = document.getElementById('hub-projects');
  cont.innerHTML='';
  list.forEach(id=>{
    const p = state.projects[id]; if(!p) return;
    const total=p.tasks.length, done=p.tasks.filter(t=>t.status==='REALIZADO').length;
    const pct=total?Math.round(done/total*100):0;
    const shortId=p.id.length>18?p.id.slice(0,18)+'…':p.id;
    const card=document.createElement('div');
    card.className='proj-card';
    card.innerHTML=`
      <div class="proj-badge">${done}/${total} ✓</div>
      <div class="proj-card-top">
        <span class="proj-dot" style="background:${esc(p.color)}"></span>
        <span class="proj-name">${esc(p.name)}</span>
        ${p.pinHash?'<span title="Protegido">🔒</span>':''}
      </div>
      <div class="proj-meta">${total} tarefas · ${pct}% concluído</div>
      <div class="proj-id">${esc(shortId)}</div>
      <div class="proj-btns"></div>`;
    const bOpen=document.createElement('button');
    bOpen.className='btn btn-primary'; bOpen.textContent='Abrir';
    bOpen.addEventListener('click', e=>{ e.stopPropagation(); openProject(id); });
    const bDel=document.createElement('button');
    bDel.className='btn btn-danger'; bDel.textContent='✕ Excluir';
    bDel.addEventListener('click', e=>{ e.stopPropagation(); deleteProjectById(id); });
    card.querySelector('.proj-btns').append(bOpen, bDel);
    cont.appendChild(card);
  });
}

function openProject(id){
  const p=state.projects[id]; if(!p) return;
  if(p.pinHash){ showPinUnlockModal(id); return; }
  launchProject(id);
}

async function launchProject(id){
  // Antes de abrir, busca a versão mais recente do storage (outro usuário pode ter editado)
  const remote = await storageLoad(id);
  if(remote && isNewer(remote, state.projects[id])){
    state.projects[id] = remote;
  }
  state.currentId=id;
  const p=state.projects[id];
  sortTasks(p);
  state.lastPulledVersion = p.version || 0;
  state.dirty = false;
  document.getElementById('hub').style.display='none';
  document.getElementById('app').style.display='flex';
  document.getElementById('tb-proj-name').textContent=p.name;
  document.getElementById('tb-proj-id').textContent=p.id.length>18?p.id.slice(0,18)+'…':p.id;
  document.getElementById('chip-date').textContent='▶ '+new Date().toLocaleDateString('pt-BR',{weekday:'short',day:'numeric',month:'short',year:'numeric'});
  renderApp();
  startSync();
}

async function goHub(){
  // Garante que qualquer edição pendente seja salva antes de sair
  clearTimeout(state.pushTimer);
  if(state.dirty) await pushLocal();
  stopSync();
  state.currentId=null;
  showHub();
}

function accessById(){
  const id=document.getElementById('hub-id-input').value.trim();
  if(!id){ toast('Digite um ID.'); return; }
  if(state.projects[id]){ openProject(id); return; }
  storageLoad(id).then(p=>{
    if(!p){ toast('Projeto não encontrado.'); return; }
    state.projects[id]=p; hubAdd(id); renderHub(); openProject(id);
  });
}

// =============================================
// DELETE OPERATIONS (all use modal — no confirm())
// =============================================
function deleteProjectById(id){
  const p=state.projects[id]; if(!p) return;
  openModal(`<div class="modal-title" style="color:var(--red-d)">🗑 Excluir Projeto</div>
    <p style="font-size:13px;margin-bottom:16px;">Excluir <strong>${esc(p.name)}</strong>?<br>
    <span style="color:var(--muted);font-size:11px;">${p.tasks.length} tarefas serão removidas. Ação irreversível.</span></p>
    <div class="modal-btns">
      <button class="btn btn-ghost" onclick="closeTopModal()">Cancelar</button>
      <button class="btn btn-danger" id="confirm-del-proj">🗑 Confirmar</button>
    </div>`);
  document.getElementById('confirm-del-proj').addEventListener('click', async()=>{
    closeTopModal();
    delete state.projects[id]; hubRemove(id);
    await storageDelete(id);
    renderHub(); toast('Projeto excluído.');
  });
}

function deleteCurrentProject(){
  if(!state.currentId) return;
  deleteProjectById(state.currentId);
  // After user confirms in modal, we also need to go to hub
  // Wrap by re-attaching confirm button after modal renders
  setTimeout(()=>{
    const btn=document.getElementById('confirm-del-proj');
    if(!btn) return;
    const origClick = btn.onclick;
    btn.addEventListener('click', ()=>{
      stopSync(); state.currentId=null;
      setTimeout(showHub, 50);
    });
  }, 50);
}

function deleteGroupByName(gname){
  const p=getProject(); if(!p) return;
  const count=p.tasks.filter(t=>t.grupo===gname).length;
  openModal(`<div class="modal-title" style="color:var(--red-d)">Excluir Grupo</div>
    <p style="font-size:13px;margin-bottom:16px;">Excluir grupo <strong>${esc(gname)}</strong>?<br>
    <span style="color:var(--muted);font-size:11px;">${count>0?count+' tarefas serão removidas.':'Grupo vazio.'} Ação irreversível.</span></p>
    <div class="modal-btns">
      <button class="btn btn-ghost" onclick="closeTopModal()">Cancelar</button>
      <button class="btn btn-danger" id="confirm-del-grp">✕ Confirmar</button>
    </div>`);
  document.getElementById('confirm-del-grp').addEventListener('click', ()=>{
    closeTopModal();
    delete p.groups[gname];
    p.tasks=p.tasks.filter(t=>t.grupo!==gname);
    markDirty(); renderApp(); toast('Grupo excluído.');
  });
}

function deleteTaskById(id){
  const p=getProject(); if(!p) return;
  p.tasks=p.tasks.filter(t=>t.id!==id);
  markDirty(); renderApp(); toast('Tarefa excluída.');
}

function deleteColById(colId){
  const p=getProject(); if(!p) return;
  openModal(`<div class="modal-title" style="color:var(--red-d)">Remover Coluna</div>
    <p style="font-size:13px;margin-bottom:16px;">Remover esta coluna? Os dados serão perdidos.</p>
    <div class="modal-btns">
      <button class="btn btn-ghost" onclick="closeTopModal()">Cancelar</button>
      <button class="btn btn-danger" id="confirm-del-col">✕ Confirmar</button>
    </div>`);
  document.getElementById('confirm-del-col').addEventListener('click', ()=>{
    closeTopModal();
    p.extraCols=p.extraCols.filter(c=>c.id!==colId);
    p.tasks.forEach(t=>{ if(t.extra) delete t.extra[colId]; });
    markDirty(); renderApp(); toast('Coluna removida.');
  });
}

// =============================================
// SYNC — Firebase live + version-aware last-write-wins
// =============================================
function startSync(){
  stopSync();
  state.lastPulledVersion = getProject()?.version || 0;

  // 1) Listener em tempo real do Firebase — mudanças de outros usuários chegam na hora
  const live = subscribeLive(state.currentId, remoteVal=>{
    applyRemote(remoteVal);
  });

  // 2) Polling de segurança (cobre o caso de Firebase indisponível → localStorage entre abas)
  //    Intervalo maior quando há listener live, menor quando é só localStorage
  state.syncTimer = setInterval(pullRemote, live ? SYNC_INTERVAL_MS*3 : SYNC_INTERVAL_MS);
}
function stopSync(){
  clearInterval(state.syncTimer);
  clearTimeout(state.pushTimer);
  unsubscribeLive();
}
function setSyncLabel(txt,pulse){
  document.getElementById('sync-label').textContent=txt;
  document.getElementById('sync-dot').className='sync-dot'+(pulse?' pulse':'');
}

// Aplica uma versão remota recebida (do listener live ou do polling)
function applyRemote(remote){
  if(!remote || !state.currentId) return;
  remote = normalizeProject(remote);
  if(state.dirty) return; // não sobrescreve edição local pendente
  const local = state.projects[state.currentId];
  if(!isNewer(remote, local)) return; // só aplica se for genuinamente mais novo

  // Preserva foco: se o usuário está digitando numa célula, adia
  const active = document.activeElement;
  const editing = active && (active.tagName==='INPUT'||active.tagName==='SELECT'||active.tagName==='TEXTAREA') && active.closest('#main-scroll');
  if(editing) return;

  state.projects[state.currentId] = remote;
  state.lastPulledVersion = remote.version || 0;
  sortTasks(remote);
  renderApp();
  setSyncLabel('↻ Atualizado', true);
  setTimeout(()=>setSyncLabel('Sincronizado', false), 1800);
}

// Agenda um push com debounce (chamado por markDirty em toda edição)
function scheduleAutoPush(){
  if(!state.currentId) return;
  setSyncLabel('Editando…', true);
  clearTimeout(state.pushTimer);
  state.pushTimer = setTimeout(pushLocal, PUSH_DEBOUNCE_MS);
}

// Envia o estado local para o storage
async function pushLocal(){
  if(!state.currentId || !state.dirty) return;
  const p = state.projects[state.currentId];
  if(!p) return;
  setSyncLabel('Salvando…', true);
  const saved = await storageSave(state.currentId, p); // incrementa version + updatedAt
  if(saved){
    state.lastPulledVersion = saved.version;
    state.dirty = false;
    if(saved._saveStatus === 'remote') setSyncLabel('Sincronizado', false);
    else if(saved._saveStatus === 'local') setSyncLabel('Salvo local', false);
    else setSyncLabel('⚠ Erro ao salvar', false);
  } else {
    setSyncLabel('⚠ Erro ao salvar', false);
  }
}

// Busca a versão remota via fetch pontual (polling de segurança)
async function pullRemote(){
  if(!state.currentId) return;
  if(state.dirty){ await pushLocal(); return; }
  const remote = await storageLoad(state.currentId);
  applyRemote(remote);
}

// Salvar manual (botão 💾) — força push imediato
async function saveProject(){
  if(!state.currentId) return;
  clearTimeout(state.pushTimer);
  const p = state.projects[state.currentId];
  setSyncLabel('Salvando…', true);
  const saved = await storageSave(state.currentId, p);
  if(saved) state.lastPulledVersion = saved.version;
  state.dirty = false;
  if(saved && saved._saveStatus === 'remote'){ setSyncLabel('Sincronizado', false); toast('Salvo e sincronizado!'); }
  else if(saved && saved._saveStatus === 'local'){ setSyncLabel('Salvo local', false); toast('Salvo localmente neste navegador.'); }
  else { setSyncLabel('⚠ Erro ao salvar', false); toast('Erro ao salvar. Tente novamente.'); }
}

// =============================================
// RENDER APP
// =============================================
function renderApp(){ updateKPIs(); renderGroupFilters(); renderGroups(); }

function updateKPIs(){
  const p=getProject(); if(!p) return;
  const total=p.tasks.length, done=p.tasks.filter(t=>t.status==='REALIZADO').length;
  const prog=p.tasks.filter(t=>t.status==='EM ANDAMENTO').length;
  const pend=p.tasks.filter(t=>t.status==='NÃO INICIADO'||t.status==='BLOQUEADO').length;
  const late=p.tasks.filter(t=>isLate(t)).length;
  const pct=total?Math.round(done/total*100):0;
  const set=(id,v,w)=>{ document.getElementById(id).textContent=v; if(w) document.getElementById(id+'-bar').style.width=w; };
  set('k-prog',pct+'%','k-prog-bar'); document.getElementById('k-prog-bar').style.width=pct+'%';
  document.getElementById('k-prog-sub').textContent=done+' de '+total+' concluídas';
  set('k-done',done); document.getElementById('k-done-bar').style.width=(total?done/total*100:0)+'%';
  set('k-prog2',prog); document.getElementById('k-prog2-bar').style.width=(total?prog/total*100:0)+'%';
  set('k-pend',pend); document.getElementById('k-pend-bar').style.width=(total?pend/total*100:0)+'%';
  set('k-late',late); document.getElementById('k-late-bar').style.width=(total?late/total*100:0)+'%';
  const fims=p.tasks.filter(t=>t.fim&&t.status!=='REALIZADO').map(t=>t.fim).sort();
  if(fims.length){
    const last=fims[fims.length-1]; const days=Math.round((new Date(last)-TODAY)/86400000);
    document.getElementById('k-days').textContent=days>=0?days+'d':Math.abs(days)+'d atrás';
    document.getElementById('k-days-sub').textContent=fmtDate(last)+'/'+last.slice(0,4);
    document.getElementById('k-days-bar').style.width=Math.max(0,Math.min(100,100-days))+'%';
  }
}

function setStatusFilter(sf){
  state.statusFilter=sf;
  document.querySelectorAll('[data-sf]').forEach(b=>{
    const active=b.dataset.sf===sf;
    const [bg,fg]=STATUS_FILTER_COLOR[b.dataset.sf]||['var(--s2)','var(--muted)'];
    b.style.background=active?bg:''; b.style.color=active?fg:'';
    b.style.borderColor=active?'transparent':'';
  });
  renderGroups();
}

function renderGroupFilters(){
  const p=getProject(); if(!p) return;
  const cont=document.getElementById('group-filters'); cont.innerHTML='';
  const mk=(label,val,color)=>{
    const b=document.createElement('button');
    b.className='filter-btn'; b.textContent=label;
    const active=state.groupFilter===val;
    if(active){ b.style.background=color; b.style.color='#fff'; b.style.borderColor='transparent'; }
    b.addEventListener('click',()=>{ state.groupFilter=val; renderApp(); });
    cont.appendChild(b);
  };
  mk('Todos','all','var(--brown)');
  for(const g in p.groups) mk(g,g,p.groups[g].color);
}

function getFilteredTasks(tasks){
  return tasks.filter(t=>{
    if(state.statusFilter==='all') return true;
    if(state.statusFilter==='ATRASADA') return isLate(t);
    return t.status===state.statusFilter;
  });
}

// =============================================
// GANTT RANGE HELPERS
// =============================================
function getGanttRange(tasks){
  const dates=tasks.flatMap(t=>[t.inicio,t.fim,t.conc].filter(Boolean)).sort();
  if(!dates.length){ const s=new Date(TODAY),e=new Date(TODAY); s.setDate(s.getDate()-1); e.setDate(e.getDate()+30); return{start:s,end:e}; }
  const start=new Date(dates[0]); start.setDate(start.getDate()-1);
  const end=new Date(dates[dates.length-1]); end.setDate(end.getDate()+3);
  return{start,end};
}
function getDays(start,end){ const days=[],cur=new Date(start); while(cur<=end){days.push(new Date(cur));cur.setDate(cur.getDate()+1);} return days; }
function groupByMonth(days){
  const months={}; days.forEach(d=>{const k=d.getFullYear()+'-'+d.getMonth(); if(!months[k])months[k]={label:d.toLocaleDateString('pt-BR',{month:'short',year:'numeric'}).toUpperCase(),count:0}; months[k].count++;});
  return Object.values(months);
}

// =============================================
// RENDER GROUPS
// =============================================
function renderGroups(){
  const p=getProject(); if(!p) return;
  const cont=document.getElementById('main-scroll');
  // save collapsed state
  const collapsed={};
  cont.querySelectorAll('.group-block').forEach(el=>{ if(el.dataset.group) collapsed[el.dataset.group]=el.classList.contains('collapsed'); });
  cont.innerHTML=''; state.groupMap={};

  const allTasks=p.tasks;
  // Garante ordenação canônica (grupo → início → fim → id) antes de qualquer render
  sortTasks(p);
  const {start,end}=getGanttRange(allTasks);
  const days=getDays(start,end); const months=groupByMonth(days);

  // info bar
  const nG=Object.keys(p.groups).length;
  const fmtD=d=>d.toLocaleDateString('pt-BR',{day:'2-digit',month:'short',year:'numeric'}).toUpperCase();
  const info=document.createElement('div');
  info.style.cssText='display:flex;justify-content:space-between;padding:5px 2px 10px;font-size:10px;font-weight:500;color:var(--muted);text-transform:uppercase;letter-spacing:.5px;';
  info.innerHTML=`<span>Cronograma · ${fmtD(start)} – ${fmtD(end)} · ${nG} grupos · ${allTasks.length} tarefas</span><span style="font-weight:400;color:var(--dim);display:flex;align-items:center;gap:10px"><span style="display:inline-flex;align-items:center;gap:4px"><span style="width:14px;height:8px;background:var(--accent);border-radius:2px;display:inline-block;opacity:.7"></span>previsto</span><span style="display:inline-flex;align-items:center;gap:4px"><span style="width:14px;height:8px;background:repeating-linear-gradient(45deg,var(--red-d),var(--red-d) 2px,#C04A1A 2px,#C04A1A 4px);border-radius:2px;display:inline-block"></span>atraso na entrega</span></span>`;
  cont.appendChild(info);

  let gIdx=0;
  for(const gname in p.groups){
    if(state.groupFilter!=='all'&&state.groupFilter!==gname) continue;
    const gc=p.groups[gname].color;
    const tasks=allTasks.filter(t=>t.grupo===gname); // já ordenado por sortTasks(p)
    const filtered=getFilteredTasks(tasks);
    const total=tasks.length, done=tasks.filter(t=>t.status==='REALIZADO').length;
    const late=tasks.filter(t=>isLate(t)).length;
    const gpct=total?Math.round(done/total*100):0;

    const idx=gIdx++; state.groupMap[idx]=gname;
    const block=document.createElement('div');
    block.className='group-block'+(collapsed[gname]?' collapsed':'');
    block.dataset.group=gname;
    block.style.borderTop=`3px solid ${gc}`;

    const lateTxt=late>0?`<span style="color:var(--red-d);font-weight:500;"> · ${late} atrasada${late>1?'s':''}</span>`:'';
    block.innerHTML=`<div class="group-head">
      <span class="group-color-dot" style="background:${gc}" data-gname="${esc(gname)}"></span>
      <input class="group-name-inp" style="color:${gc}" value="${esc(gname)}" data-oldname="${esc(gname)}">
      <span class="group-meta">${total} tarefas · ${done} concluídas${lateTxt}</span>
      <div style="flex:1"></div>
      <div class="group-mini-bar"><div class="group-mini-fill" style="width:${gpct}%;background:${gc}"></div></div>
      <span class="group-pct" style="color:${gc}">${gpct}%</span>
      <button class="group-btn group-add-btn" data-add-task="${esc(gname)}">+ Tarefa</button>
      <button class="group-btn group-del-btn" data-del-gidx="${idx}">✕</button>
      <button class="collapse-btn">▼</button>
    </div>`;

    // TABLE
    const bodyDiv=document.createElement('div'); bodyDiv.className='group-body';
    const table=document.createElement('table'); table.className='gantt-table';
    table.dataset.tableId = tableIdFor(gname);
    const fixedCols=13+(p.extraCols||[]).length;
    let mHtml=`<tr class="row-month"><th colspan="${fixedCols}" style="min-width:1116px"></th>`;
    months.forEach(m=>mHtml+=`<th colspan="${m.count}">${m.label}</th>`);
    mHtml+='</tr>';
    let hHtml='<tr class="row-fixed"><th class="col-task" data-col-key="task">TAREFA</th><th class="col-prio" data-col-key="prio">!</th><th class="col-status" data-col-key="status">STATUS</th><th class="col-date" data-col-key="inicio">INÍCIO</th><th class="col-date" data-col-key="fim">FIM PREV.</th><th class="col-date" data-col-key="conc">CONCLUSÃO</th><th class="col-days" data-col-key="days">DIAS</th><th class="col-pct" data-col-key="pct">%</th><th class="col-resp th-resp" data-col-key="resp">👤 RESP.</th><th class="col-obs th-obs" data-col-key="obs">📝 OBS.</th><th class="col-alert th-alert" data-col-key="alert">⚠ ATRASO</th><th class="col-delayresp th-delayresp" data-col-key="delayresp">RESP. ATRASO</th><th class="col-del" data-col-key="del">DEL</th>';
    (p.extraCols||[]).forEach(ec=>hHtml+=`<th data-col-key="extra:${ec.id}" style="width:${ec.width}px;min-width:60px;border-left:2px solid rgba(72,49,27,.2);color:var(--brown);font-size:8px;padding:4px 6px;position:relative;">${esc(ec.name)} <button class="group-btn group-del-btn" style="padding:1px 4px;font-size:9px;" data-del-col="${ec.id}">✕</button></th>`);
    days.forEach(d=>{
      const ds=d.toISOString().slice(0,10);
      hHtml+=`<th class="cal-day-h${ds===todayStr?' today-h':''}${(d.getDay()===0||d.getDay()===6)?' weekend':''}">${d.getDate()}</th>`;
    });
    hHtml+='</tr>';
    table.innerHTML=mHtml+hHtml;
    filtered.forEach(t=>table.appendChild(makeTaskRow(t,days,gc,p)));
    bodyDiv.appendChild(table); block.appendChild(bodyDiv);

    // FOOTER quick add
    const foot=document.createElement('div'); foot.className='group-footer';
    const qa=document.createElement('input'); qa.className='quick-add';
    qa.placeholder='+ Adicionar tarefa rápida… (Enter)'; qa.dataset.qaGroup=gname;
    foot.appendChild(qa); block.appendChild(foot);
    cont.appendChild(block);
  }

  // Apply user-defined column widths and install resize handles
  applyColWidths();
  installResizers();
}

// =============================================
// MAKE TASK ROW
// =============================================
function renderAlertCell(t){
  const d = getDelay(t);
  if(d > 0) return `<span class="badge-atraso">⚠ ${d}d atraso</span>`;
  if(t.status==='REALIZADO') return `<span class="badge-no-time">✓ no prazo</span>`;
  return `<span class="badge-pendente">—</span>`;
}

function renderDelayRespCell(t){
  const d = getDelay(t);
  if(d <= 0) return `<span style="font-size:9px;color:var(--dim)">—</span>`;
  const v = t.delayResp || '';
  const cls = v==='ZENATUR' ? 'delayresp-zenatur' : v==='DENGO' ? 'delayresp-dengo' : 'delayresp-empty';
  return `<select class="delayresp-sel ${cls}" data-tid="${t.id}" data-field="delayResp">
    <option value="" ${v===''?'selected':''}>Selecionar...</option>
    <option value="ZENATUR" ${v==='ZENATUR'?'selected':''}>ZENATUR</option>
    <option value="DENGO"   ${v==='DENGO'?'selected':''}>DENGO</option>
  </select>`;
}

function makeTaskRow(t, days, gc, p){
  const late=isLate(t);
  const tr=document.createElement('tr');
  if(late) tr.classList.add('row-late');
  tr.dataset.tid=t.id;
  const pc=t.pct>=100?'var(--done)':t.pct>0?'var(--accent)':'var(--dim)';
  const sc=STATUS_CLS[t.status]||'s-wait';
  const dn=calcDays(t.inicio,t.fim);

  let extra='';
  (p.extraCols||[]).forEach(ec=>{
    const v=t.extra&&t.extra[ec.id]!=null?t.extra[ec.id]:'';
    if(ec.type==='check') extra+=`<td data-col-key="extra:${ec.id}" style="text-align:center;padding:4px;width:${ec.width}px"><input type="checkbox" style="width:14px;height:14px" ${v?'checked':''} data-extra-tid="${t.id}" data-extra-col="${ec.id}" data-extra-type="check"></td>`;
    else extra+=`<td data-col-key="extra:${ec.id}" style="padding:2px 4px;width:${ec.width}px"><input type="${ec.type==='number'?'number':ec.type==='date'?'date':'text'}" value="${esc(String(v))}" data-extra-tid="${t.id}" data-extra-col="${ec.id}" style="border:none;background:transparent;font-size:10px;outline:none;width:100%;"></td>`;
  });

  let calCells='';
  days.forEach(d=>{
    const ds=d.toISOString().slice(0,10); const isToday=ds===todayStr; const isWk=d.getDay()===0||d.getDay()===6;
    calCells+=`<td class="cal-day${isWk?' weekend':''}${isToday?' today-col':''}" style="width:20px;min-width:20px;max-width:20px;height:28px;"></td>`;
  });

  tr.innerHTML=`
    <td class="cell-task" data-col-key="task"><input class="task-name-inp" value="${esc(t.tarefa)}" data-tid="${t.id}" data-field="tarefa">${late?'<span class="badge-late">ATRASADA</span>':''}</td>
    <td class="cell-prio" data-col-key="prio"><span class="prio-dot" style="background:${PRIO_COLORS[t.prio]||PRIO_COLORS['']}" data-prio-tid="${t.id}" title="${t.prio||'Sem prioridade'}"></span></td>
    <td class="cell-status" data-col-key="status"><select class="status-sel ${sc}" data-tid="${t.id}" data-field="status">
      <option value="NÃO INICIADO" ${t.status==='NÃO INICIADO'?'selected':''}>NÃO INICIADO</option>
      <option value="EM ANDAMENTO" ${t.status==='EM ANDAMENTO'?'selected':''}>EM ANDAMENTO</option>
      <option value="REALIZADO"    ${t.status==='REALIZADO'?'selected':''}>REALIZADO</option>
      <option value="BLOQUEADO"    ${t.status==='BLOQUEADO'?'selected':''}>BLOQUEADO</option>
    </select></td>
    <td class="cell-date" data-col-key="inicio" data-float-tid="${t.id}" data-float-field="inicio"><span class="date-display${t.inicio?'':' empty'}"><span>${t.inicio?fmtDate(t.inicio):'dd/mm'}</span><span class="cal-icon">🗓</span></span></td>
    <td class="cell-date" data-col-key="fim" data-float-tid="${t.id}" data-float-field="fim"><span class="date-display${t.fim?'':' empty'}"><span>${t.fim?fmtDate(t.fim):'dd/mm'}</span><span class="cal-icon">🗓</span></span></td>
    <td class="cell-date" data-col-key="conc" data-float-tid="${t.id}" data-float-field="conc"><span class="date-display${t.conc?' conc-filled':''}${t.conc?'':' empty'}"><span>${t.conc?fmtDate(t.conc):'dd/mm'}</span><span class="cal-icon">🗓</span></span></td>
    <td class="cell-days" data-col-key="days"><input class="days-inp" type="number" value="${dn}" min="0" data-tid="${t.id}"></td>
    <td class="cell-pct" data-col-key="pct"><div class="pct-bar"><div class="pct-fill" style="width:${t.pct}%;background:${pc}"></div></div><input class="pct-inp" type="number" value="${t.pct}" min="0" max="100" data-tid="${t.id}" data-field="pct"></td>
    <td class="cell-resp" data-col-key="resp" data-float-tid="${t.id}" data-float-field="resp">${t.resp?`<span class="chip-pill">${esc(t.resp)}</span>`:'<span class="chip-assign">+ Atribuir</span>'}</td>
    <td class="cell-obs"  data-col-key="obs" data-float-tid="${t.id}" data-float-field="obs">${t.obs?`<span class="obs-text">${esc(t.obs.slice(0,60))}</span>`:'<span class="obs-add">+ Nota</span>'}</td>
    <td class="cell-alert" data-col-key="alert">${renderAlertCell(t)}</td>
    <td class="cell-delayresp" data-col-key="delayresp">${renderDelayRespCell(t)}</td>
    <td class="cell-del" data-col-key="del"><button class="btn-del" data-del-tid="${t.id}">✕</button></td>
    ${extra}${calCells}`;

  // Gantt bar — barra principal (previsto) + extensão vermelha quando concluído com atraso
  setTimeout(()=>{
    const cells=tr.querySelectorAll('.cal-day'); if(!cells.length||!t.inicio||!t.fim) return;
    const dayList=Array.from(cells);
    const tr2=tr.getBoundingClientRect();

    // Helper: encontra o elemento da célula correspondente a uma data ISO
    const cellForDate=(ds)=>{
      let target=null;
      dayList.forEach((el,i)=>{ if(days[i]?.toISOString().slice(0,10)===ds) target=el; });
      return target;
    };
    // Helpers para faixa [from,to] inclusivos
    const firstCellFrom=(ds)=>{
      let res=null;
      dayList.forEach((el,i)=>{ if(!res && days[i]?.toISOString().slice(0,10)>=ds) res=el; });
      return res;
    };
    const lastCellUntil=(ds)=>{
      let res=null;
      dayList.forEach((el,i)=>{ if(days[i]?.toISOString().slice(0,10)<=ds) res=el; });
      return res;
    };

    // Barra principal: do início até o fim previsto
    const sEl=firstCellFrom(t.inicio), eEl=lastCellUntil(t.fim);
    if(sEl&&eEl){
      const sr=sEl.getBoundingClientRect(), er=eEl.getBoundingClientRect();
      const bar=document.createElement('div'); bar.className='gantt-bar';
      const op={REALIZADO:.7,'EM ANDAMENTO':.55,BLOQUEADO:.45,'NÃO INICIADO':.2}[t.status]||.2;
      bar.style.cssText=`left:${sr.left-tr2.left}px;width:${Math.max(er.right-sr.left,2)}px;background:${gc};opacity:${op};z-index:1;`;
      tr.style.position='relative'; tr.appendChild(bar);
    }

    // Barra de atraso: do dia seguinte ao fim previsto até a conclusão real
    const delay=getDelay(t);
    if(delay>0 && t.conc){
      const afterFim=addDays(t.fim,1);
      const lsEl=firstCellFrom(afterFim), leEl=lastCellUntil(t.conc);
      if(lsEl&&leEl){
        const lsr=lsEl.getBoundingClientRect(), ler=leEl.getBoundingClientRect();
        if(ler.right>lsr.left){
          const lateBar=document.createElement('div'); lateBar.className='gantt-bar-late';
          lateBar.style.cssText=`left:${lsr.left-tr2.left}px;width:${Math.max(ler.right-lsr.left,2)}px;z-index:2;`;
          lateBar.title=`Atraso na conclusão: ${delay} dia${delay>1?'s':''}`;
          tr.style.position='relative'; tr.appendChild(lateBar);
        }
      }
    }
  },60);

  return tr;
}

// =============================================
// MAIN-SCROLL DELEGATED EVENTS
// =============================================
function mainClickDelegate(e){
  // Delete task
  const delTid=e.target.closest('[data-del-tid]');
  if(delTid){ deleteTaskById(parseInt(delTid.dataset.delTid)); return; }

  // Delete group
  const delGidx=e.target.closest('[data-del-gidx]');
  if(delGidx){ deleteGroupByName(state.groupMap[parseInt(delGidx.dataset.delGidx)]); return; }

  // Delete col
  const delCol=e.target.closest('[data-del-col]');
  if(delCol){ deleteColById(delCol.dataset.delCol); return; }

  // Add task to group
  const addTask=e.target.closest('[data-add-task]');
  if(addTask){ showNewTaskModal(addTask.dataset.addTask); return; }

  // Collapse group
  const collBtn=e.target.closest('.collapse-btn');
  if(collBtn){ collBtn.closest('.group-block').classList.toggle('collapsed'); return; }

  // Color picker for group dot
  const dot=e.target.closest('.group-color-dot');
  if(dot){ pickGroupColor(dot.dataset.gname, dot); return; }

  // Prio selector
  const prioDot=e.target.closest('[data-prio-tid]');
  if(prioDot){ openPrioSel(parseInt(prioDot.dataset.prioTid), prioDot); return; }

  // Float editor
  const floatCell=e.target.closest('[data-float-tid]');
  if(floatCell){ openFloat(parseInt(floatCell.dataset.floatTid), floatCell.dataset.floatField, floatCell); return; }
}

function mainChangeDelegate(e){
  const tid=e.target.dataset.tid;
  if(!tid) return;
  const id=parseInt(tid); const t=getTask(id); if(!t) return;
  const field=e.target.dataset.field;

  if(field==='status'){
    t.status=e.target.value;
    if(t.status==='REALIZADO') t.pct=100;
    else if(t.status==='EM ANDAMENTO') t.pct=Math.max(t.pct,1);
    else t.pct=0;
    e.target.className='status-sel '+(STATUS_CLS[t.status]||'s-wait');
    const row=e.target.closest('tr');
    if(row){ const pi=row.querySelector('.pct-inp'); if(pi) pi.value=t.pct; const pf=row.querySelector('.pct-fill'); if(pf){pf.style.width=t.pct+'%';pf.style.background=t.pct>=100?'var(--done)':t.pct>0?'var(--accent)':'var(--dim)';} }
    markDirty(); updateKPIs(); return;
  }
  if(field==='pct'){
    t.pct=Math.max(0,Math.min(100,parseInt(e.target.value)||0));
    const row=e.target.closest('tr');
    if(row){ const pf=row.querySelector('.pct-fill'); if(pf){pf.style.width=t.pct+'%';pf.style.background=t.pct>=100?'var(--done)':t.pct>0?'var(--accent)':'var(--dim)';} }
    markDirty(); return;
  }
  if(field==='delayResp'){
    t.delayResp = e.target.value;
    // update visual class on the select
    e.target.className = 'delayresp-sel ' + (t.delayResp==='ZENATUR'?'delayresp-zenatur':t.delayResp==='DENGO'?'delayresp-dengo':'delayresp-empty');
    markDirty(); return;
  }
  // extra checkbox
  if(e.target.dataset.extraTid){
    const et=getTask(parseInt(e.target.dataset.extraTid)); if(!et) return;
    if(!et.extra) et.extra={};
    et.extra[e.target.dataset.extraCol]=e.target.type==='checkbox'?e.target.checked:e.target.value;
    markDirty(); return;
  }
}

function mainInputDelegate(e){
  // days input
  const daysInp=e.target.classList.contains('days-inp');
  if(daysInp){
    const t=getTask(parseInt(e.target.dataset.tid)); if(!t||!t.inicio) return;
    t.fim=addDays(t.inicio,parseInt(e.target.value)||0);
    markDirty();
    // re-render to keep order + recompute alert column
    const p=getProject(); if(p) sortTasks(p);
    renderApp();
    return;
  }
}

function mainKeyDelegate(e){
  // task name Enter
  if(e.target.classList.contains('task-name-inp') && e.key==='Enter') e.target.blur();
  // quick add Enter
  if(e.target.classList.contains('quick-add') && e.key==='Enter'){
    const name=e.target.value.trim(); if(!name) return;
    const gname=e.target.dataset.qaGroup; const p=getProject(); if(!p) return;
    p.tasks.push({id:p.nextId++,grupo:gname,prio:'',tarefa:name.toUpperCase(),inicio:todayStr,fim:todayStr,conc:'',status:'NÃO INICIADO',pct:0,resp:'',obs:'',extra:{}});
    sortTasks(p);
    e.target.value=''; markDirty(); renderApp();
  }
}

// task name blur
document.addEventListener('blur', e=>{
  if(e.target.classList.contains('task-name-inp')){
    const t=getTask(parseInt(e.target.dataset.tid)); if(!t) return;
    t.tarefa=e.target.value.toUpperCase(); e.target.value=t.tarefa; markDirty();
  }
  // extra text blur
  if(e.target.dataset.extraTid){
    const t=getTask(parseInt(e.target.dataset.extraTid)); if(!t) return;
    if(!t.extra)t.extra={}; t.extra[e.target.dataset.extraCol]=e.target.value; markDirty();
  }
  // group name blur
  if(e.target.classList.contains('group-name-inp')) renameGroup(e.target);
}, true);

// =============================================
// GROUP OPS
// =============================================
function renameGroup(inp){
  const oldName=inp.dataset.oldname, newName=inp.value.trim().toUpperCase();
  if(!newName||newName===oldName){inp.value=oldName;return;}
  const p=getProject(); if(!p) return;
  if(p.groups[newName]){toast('Grupo já existe.');inp.value=oldName;return;}
  p.groups[newName]=p.groups[oldName]; delete p.groups[oldName];
  p.tasks.forEach(t=>{if(t.grupo===oldName)t.grupo=newName;});
  markDirty(); renderApp();
}

function pickGroupColor(gname, dotEl){
  const inp=document.createElement('input'); inp.type='color';
  inp.value=getProject()?.groups[gname]?.color||'#FFA600';
  inp.style.cssText='position:fixed;opacity:0;pointer-events:none;'; document.body.appendChild(inp);
  inp.click();
  inp.onchange=()=>{ const p=getProject(); if(p&&p.groups[gname])p.groups[gname].color=inp.value; inp.remove(); markDirty(); renderApp(); };
}

// =============================================
// FLOAT EDITOR
// =============================================
function openFloat(id, field, cellEl){
  state.floatTaskId=id; state.floatField=field;
  const t=getTask(id); if(!t) return;
  const isDate=DATE_FIELDS.includes(field);
  const fe=document.getElementById('float-editor');
  document.getElementById('fe-label').textContent=FLOAT_LABEL[field]||field;
  const txt=document.getElementById('fe-text'), dt=document.getElementById('fe-date');
  txt.style.display='none'; dt.style.display='none';
  txt.onblur=null; dt.onchange=null;

  if(isDate){
    dt.style.display='block'; dt.value=t[field]||'';
    dt.onchange=()=>confirmFloat();
    setTimeout(()=>{dt.focus(); try{dt.showPicker&&dt.showPicker();}catch(err){}},60);
  } else {
    txt.style.display='block'; txt.value=t[field]||'';
    setTimeout(()=>{txt.focus(); txt.select();},60);
    txt.onblur=()=>setTimeout(()=>{ if(state.floatTaskId!==null) confirmFloat(); },150);
  }

  const rect=cellEl.getBoundingClientRect();
  let top=rect.bottom+4, left=rect.left;
  if(top+90>window.innerHeight) top=rect.top-94;
  if(left+220>window.innerWidth) left=window.innerWidth-224;
  fe.style.top=Math.max(4,top)+'px'; fe.style.left=Math.max(4,left)+'px';
  fe.style.display='block';
}

function confirmFloat(){
  if(state.floatTaskId===null) return;
  const t=getTask(state.floatTaskId); if(!t) return;
  const isDate=DATE_FIELDS.includes(state.floatField);
  const val=isDate?document.getElementById('fe-date').value:document.getElementById('fe-text').value;
  const prevVal=t[state.floatField];
  t[state.floatField]=val; markDirty();

  // Datas de início/fim alteram a ordem visual — re-render completo
  if(isDate && (state.floatField==='inicio' || state.floatField==='fim') && prevVal!==val){
    const p=getProject(); if(p) sortTasks(p);
    closeFloat();
    renderApp();
    return;
  }

  // Update cell without full re-render
  const row=document.querySelector(`tr[data-tid="${state.floatTaskId}"]`);
  if(row){
    if(isDate){
      const idx={inicio:0,fim:1,conc:2}[state.floatField];
      const cell=row.querySelectorAll('.cell-date')[idx];
      if(cell){
        const sp=cell.querySelector('.date-display span'); if(sp) sp.textContent=val?fmtDate(val):'dd/mm';
        const disp=cell.querySelector('.date-display');
        if(disp){ disp.className='date-display'+(state.floatField==='conc'&&val?' conc-filled':'')+(val?'':' empty'); }
      }
      // recalc days display
      const t2=getTask(state.floatTaskId); if(t2){
        const di=row.querySelector('.days-inp'); if(di) di.value=calcDays(t2.inicio,t2.fim);
      }
    } else if(state.floatField==='resp'){
      const c=row.querySelector('.cell-resp'); if(c) c.innerHTML=val?`<span class="chip-pill">${esc(val)}</span>`:'<span class="chip-assign">+ Atribuir</span>';
    } else if(state.floatField==='obs'){
      const c=row.querySelector('.cell-obs'); if(c) c.innerHTML=val?`<span class="obs-text">${esc(val.slice(0,60))}</span>`:'<span class="obs-add">+ Nota</span>';
    }
  }
  closeFloat(); updateKPIs();
}

function closeFloat(){
  document.getElementById('float-editor').style.display='none';
  document.getElementById('fe-text').onblur=null; document.getElementById('fe-date').onchange=null;
  state.floatTaskId=null; state.floatField=null;
}

// =============================================
// PRIO SELECTOR
// =============================================
function openPrioSel(id, dotEl){
  state.prioTaskId=id;
  const ps=document.getElementById('prio-sel'), r=dotEl.getBoundingClientRect();
  ps.style.top=(r.bottom+4)+'px'; ps.style.left=r.left+'px'; ps.style.display='block';
}
function applyPrio(val){
  const t=getTask(state.prioTaskId); if(!t) return;
  t.prio=val; markDirty();
  document.getElementById('prio-sel').style.display='none';
  const row=document.querySelector(`tr[data-tid="${state.prioTaskId}"]`);
  if(row){ const dot=row.querySelector('.prio-dot'); if(dot) dot.style.background=PRIO_COLORS[val]||PRIO_COLORS['']; }
  state.prioTaskId=null;
}

// =============================================
// GLOBAL HANDLERS
// =============================================
function globalClickHandler(e){
  // Close float
  const fe=document.getElementById('float-editor');
  if(fe.style.display==='block'&&!fe.contains(e.target)&&!e.target.closest('[data-float-tid]')) confirmFloat();
  // Close prio
  const ps=document.getElementById('prio-sel');
  if(ps.style.display==='block'&&!ps.contains(e.target)&&!e.target.closest('[data-prio-tid]')){ ps.style.display='none'; state.prioTaskId=null; }
}
function globalKeyHandler(e){
  if(document.getElementById('float-editor').style.display==='block'){
    if(e.key==='Enter'&&state.floatField&&!DATE_FIELDS.includes(state.floatField)) confirmFloat();
    if(e.key==='Escape') closeFloat();
  }
  if(document.getElementById('present').style.display==='flex'){
    if(e.key==='ArrowRight'||e.key==='ArrowDown') moveSlide(1);
    else if(e.key==='ArrowLeft'||e.key==='ArrowUp') moveSlide(-1);
    else if(e.key==='Escape') closePresentation();
  }
}

// =============================================
// MODALS
// =============================================
function openModal(html){
  const cont=document.getElementById('modals');
  const ov=document.createElement('div'); ov.className='modal-overlay';
  ov.innerHTML=`<div class="modal-card">${html}</div>`;
  ov.addEventListener('click',e=>{ if(e.target===ov) ov.remove(); });
  cont.appendChild(ov);
  setTimeout(()=>{ const f=ov.querySelector('input,select'); if(f) f.focus(); },150);
  return ov;
}
function closeTopModal(){ const c=document.getElementById('modals'); if(c.lastChild) c.lastChild.remove(); }

// NEW TASK
function showNewTaskModal(pregroup){
  const p=getProject(); if(!p) return;
  const opts=Object.keys(p.groups).map(g=>`<option ${pregroup===g?'selected':''}>${esc(g)}</option>`).join('');
  const ov=openModal(`<div class="modal-title">+ Nova Tarefa</div>
    <div class="form-group"><label>Nome</label><input type="text" id="nt-name" placeholder="NOME DA TAREFA"></div>
    <div class="form-group"><label>Grupo</label><select id="nt-group">${opts}</select></div>
    <div class="form-group"><label>Prioridade</label><select id="nt-prio"><option value="">Sem prioridade</option><option>ALTA</option><option>MÉDIA</option><option>BAIXA</option></select></div>
    <div style="display:grid;grid-template-columns:1fr 1fr;gap:8px">
      <div class="form-group"><label>Início</label><input type="date" id="nt-ini" value="${todayStr}"></div>
      <div class="form-group"><label>Fim</label><input type="date" id="nt-fim" value="${todayStr}"></div>
    </div>
    <div class="form-group"><label>Responsável</label><input type="text" id="nt-resp"></div>
    <div class="form-group"><label>Observação</label><input type="text" id="nt-obs"></div>
    <div class="modal-btns">
      <button class="btn btn-ghost" onclick="closeTopModal()">Cancelar</button>
      <button class="btn btn-primary" id="btn-create-task">Criar</button>
    </div>`);
  ov.querySelector('#btn-create-task').addEventListener('click',()=>{
    const name=document.getElementById('nt-name').value.trim().toUpperCase();
    if(!name){toast('Nome obrigatório.');return;}
    p.tasks.push({id:p.nextId++,grupo:document.getElementById('nt-group').value,prio:document.getElementById('nt-prio').value,tarefa:name,inicio:document.getElementById('nt-ini').value,fim:document.getElementById('nt-fim').value,conc:'',status:'NÃO INICIADO',pct:0,resp:document.getElementById('nt-resp').value,obs:document.getElementById('nt-obs').value,extra:{}});
    sortTasks(p);
    markDirty(); closeTopModal(); renderApp(); toast('Tarefa criada!');
  });
}

// NEW GROUP
function showNewGroupModal(){
  const ov=openModal(`<div class="modal-title">+ Novo Grupo</div>
    <div class="form-group"><label>Nome</label><input type="text" id="ng-name" placeholder="NOME DO GRUPO"></div>
    <div class="form-group"><label>Cor</label>
      <div class="color-swatches">${SWATCHES.map(c=>`<span class="color-swatch" style="background:${c}" data-c="${c}"></span>`).join('')}</div>
      <input type="color" id="ng-color" value="#FFA600" style="width:100%;height:32px;border:1.5px solid var(--border2);border-radius:6px;cursor:pointer;">
    </div>
    <div class="modal-btns">
      <button class="btn btn-ghost" onclick="closeTopModal()">Cancelar</button>
      <button class="btn btn-primary" id="btn-create-group">Criar</button>
    </div>`);
  ov.querySelectorAll('.color-swatch').forEach(s=>s.addEventListener('click',()=>{ ov.querySelectorAll('.color-swatch').forEach(x=>x.classList.remove('sel')); s.classList.add('sel'); document.getElementById('ng-color').value=s.dataset.c; }));
  ov.querySelector('#btn-create-group').addEventListener('click',()=>{
    const p=getProject(); if(!p) return;
    const name=document.getElementById('ng-name').value.trim().toUpperCase();
    if(!name){toast('Nome obrigatório.');return;}
    if(p.groups[name]){toast('Grupo já existe.');return;}
    p.groups[name]={color:document.getElementById('ng-color').value};
    markDirty(); closeTopModal(); renderApp(); toast('Grupo criado!');
  });
}

// NEW COLUMN
const COL_TYPES=[{type:'text',icon:'📝',name:'Texto'},{type:'number',icon:'🔢',name:'Número'},{type:'date',icon:'📅',name:'Data'},{type:'check',icon:'☑️',name:'Check'},{type:'text',icon:'🔗',name:'Link'}];
function showNewColModal(){
  state.selColType='text';
  const ov=openModal(`<div class="modal-title">+ Nova Coluna</div>
    <div class="col-type-grid">${COL_TYPES.map(c=>`<div class="col-type-card" data-ct="${c.type}"><div style="font-size:20px">${c.icon}</div><div style="font-size:11px;font-weight:500">${c.name}</div></div>`).join('')}</div>
    <div class="form-group"><label>Nome da Coluna</label><input type="text" id="ec-name" placeholder="Ex: Custo, Aprovado..."></div>
    <div class="form-group"><label>Largura (px)</label><input type="number" id="ec-width" value="100" min="60" max="300"></div>
    <div class="modal-btns">
      <button class="btn btn-ghost" onclick="closeTopModal()">Cancelar</button>
      <button class="btn btn-primary" id="btn-create-col">Criar</button>
    </div>`);
  ov.querySelectorAll('.col-type-card').forEach(card=>card.addEventListener('click',()=>{ ov.querySelectorAll('.col-type-card').forEach(x=>x.classList.remove('sel')); card.classList.add('sel'); state.selColType=card.dataset.ct; }));
  ov.querySelector('#btn-create-col').addEventListener('click',()=>{
    const p=getProject(); if(!p) return;
    const name=document.getElementById('ec-name').value.trim(); if(!name){toast('Nome obrigatório.');return;}
    const id='ec_'+Date.now().toString(36);
    if(!p.extraCols) p.extraCols=[];
    p.extraCols.push({id,name,type:state.selColType,width:parseInt(document.getElementById('ec-width').value)||100});
    p.tasks.forEach(t=>{if(!t.extra)t.extra={};t.extra[id]=state.selColType==='check'?false:'';});
    markDirty(); closeTopModal(); renderApp(); toast('Coluna criada!');
  });
}

// NEW PROJECT
function showNewProjectModal(){
  state.selTmpl='blank';
  const ov=openModal(`<div class="modal-title">+ Novo Projeto</div>
    <div class="form-group"><label>Nome</label><input type="text" id="np-name" placeholder="Nome do projeto"></div>
    <div class="form-group"><label>Cor</label><input type="color" id="np-color" value="#FFA600" style="width:100%;height:32px;border:1.5px solid var(--border2);border-radius:6px;cursor:pointer;"></div>
    <div class="form-group"><label>Template</label>
      <div class="tmpl-grid">${TEMPLATES.map(t=>`<div class="tmpl-card" data-tmpl="${t.id}"><div style="font-size:24px">${t.icon}</div><div style="font-size:11px;font-weight:500">${t.name}</div><div style="font-size:10px;color:var(--muted)">${t.desc}</div></div>`).join('')}</div>
    </div>
    <div class="form-group"><label>PIN (opcional, mín. 3 chars)</label><input type="password" id="np-pin" placeholder="Deixe vazio para sem PIN"></div>
    <div class="modal-btns">
      <button class="btn btn-ghost" onclick="closeTopModal()">Cancelar</button>
      <button class="btn btn-primary" id="btn-create-proj">Criar</button>
    </div>`);
  ov.querySelectorAll('.tmpl-card').forEach(c=>c.addEventListener('click',()=>{ ov.querySelectorAll('.tmpl-card').forEach(x=>x.classList.remove('sel')); c.classList.add('sel'); state.selTmpl=c.dataset.tmpl; }));
  ov.querySelector('#btn-create-proj').addEventListener('click', async()=>{
    const name=document.getElementById('np-name').value.trim(); if(!name){toast('Nome obrigatório.');return;}
    const pin=document.getElementById('np-pin').value;
    if(pin&&pin.length<3){toast('PIN mínimo 3 chars.');return;}
    const tmpl=TEMPLATES.find(t=>t.id===state.selTmpl)||TEMPLATES[0];
    const id=genId();
    const p={id,name,color:document.getElementById('np-color').value,pinHash:pin?hashPin(pin):'',groups:{...tmpl.groups},tasks:[],extraCols:[],nextId:1,createdAt:new Date().toISOString(),updatedAt:new Date().toISOString()};
    state.projects[id]=p; hubAdd(id); await storageSave(id,p);
    closeTopModal(); renderHub(); toast('Projeto criado!');
  });
}

// SHARE
function shareProject(){ const p=getProject(); if(!p) return; navigator.clipboard.writeText(p.id).catch(()=>{}); toast('ID copiado: '+p.id); }

// PIN
function showPinUnlockModal(projectId){
  state.pinAttempts=0; state.pinBlocked=false;
  const p=state.projects[projectId];
  const ov=openModal(`<div style="text-align:center">
    <div style="font-size:40px;margin-bottom:8px">🔒</div>
    <div style="font-weight:600;font-size:16px;margin-bottom:4px">${esc(p.name)}</div>
    <div style="font-size:12px;color:var(--muted);margin-bottom:16px">Protegido por PIN</div>
    <div class="form-group"><input type="password" id="pin-input" style="text-align:center;letter-spacing:6px;font-size:18px;"></div>
    <div id="pin-msg" style="font-size:11px;color:var(--red-d);margin-top:6px;min-height:16px;"></div>
    <div class="modal-btns" style="justify-content:center;margin-top:16px">
      <button class="btn btn-ghost" onclick="closeTopModal()">Cancelar</button>
      <button class="btn btn-primary" id="btn-pin-ok">Entrar</button>
    </div></div>`);
  const tryUnlock=()=>{
    if(state.pinBlocked) return;
    const val=document.getElementById('pin-input').value;
    if(hashPin(val)===p.pinHash){ closeTopModal(); launchProject(projectId); return; }
    state.pinAttempts++;
    if(state.pinAttempts>=5){
      state.pinBlocked=true;
      document.getElementById('pin-input').disabled=true;
      document.getElementById('pin-msg').textContent='Bloqueado por 30s.';
      setTimeout(()=>{ state.pinBlocked=false; const i=document.getElementById('pin-input'); if(i){i.disabled=false;} document.getElementById('pin-msg').textContent=''; state.pinAttempts=0; },30000);
    } else {
      document.getElementById('pin-msg').textContent=`PIN incorreto. ${5-state.pinAttempts} tentativas restantes.`;
      document.getElementById('pin-input').value='';
    }
  };
  ov.querySelector('#btn-pin-ok').addEventListener('click', tryUnlock);
  ov.querySelector('#pin-input').addEventListener('keydown', e=>{ if(e.key==='Enter') tryUnlock(); });
}

function showPinModal(){
  const p=getProject(); if(!p) return;
  if(p.pinHash){
    const ov=openModal(`<div class="modal-title">🔑 Gerenciar PIN</div>
      <div class="form-group"><label>PIN Atual</label><input type="password" id="pm-cur"></div>
      <div class="form-group"><label>Novo PIN (vazio = remover)</label><input type="password" id="pm-new" placeholder="mín. 3 chars"></div>
      <div class="form-group"><label>Confirmar Novo PIN</label><input type="password" id="pm-conf"></div>
      <div class="modal-btns"><button class="btn btn-ghost" onclick="closeTopModal()">Cancelar</button><button class="btn btn-primary" id="btn-pin-manage">Confirmar</button></div>`);
    ov.querySelector('#btn-pin-manage').addEventListener('click',()=>{
      if(hashPin(document.getElementById('pm-cur').value)!==p.pinHash){toast('PIN atual incorreto.');return;}
      const np=document.getElementById('pm-new').value, cf=document.getElementById('pm-conf').value;
      if(!np){p.pinHash='';markDirty();closeTopModal();toast('PIN removido.');return;}
      if(np.length<3){toast('Mínimo 3 chars.');return;}
      if(np!==cf){toast('PINs não coincidem.');return;}
      p.pinHash=hashPin(np);markDirty();closeTopModal();toast('PIN alterado!');
    });
  } else {
    const ov=openModal(`<div class="modal-title">🔑 Criar PIN</div>
      <div class="form-group"><label>Novo PIN</label><input type="password" id="pm-new" placeholder="mín. 3 chars"></div>
      <div class="form-group"><label>Confirmar PIN</label><input type="password" id="pm-conf"></div>
      <div class="modal-btns"><button class="btn btn-ghost" onclick="closeTopModal()">Cancelar</button><button class="btn btn-primary" id="btn-pin-create">Criar</button></div>`);
    ov.querySelector('#btn-pin-create').addEventListener('click',()=>{
      const np=document.getElementById('pm-new').value, cf=document.getElementById('pm-conf').value;
      if(np.length<3){toast('Mínimo 3 chars.');return;}
      if(np!==cf){toast('PINs não coincidem.');return;}
      p.pinHash=hashPin(np);markDirty();closeTopModal();toast('PIN criado!');
    });
  }
}

// CSV EXPORT
function exportCSV(){
  const p=getProject(); if(!p) return;
  let csv='\uFEFFID;GRUPO;PRIORIDADE;TAREFA;INÍCIO;FIM PREV.;CONCLUSÃO;DIAS;STATUS;%;RESPONSÁVEL;OBS;DIAS DE ATRASO;RESP. ATRASO';
  (p.extraCols||[]).forEach(ec=>csv+=';'+ec.name);
  csv+='\n';
  p.tasks.forEach(t=>{
    const d=getDelay(t);
    let row=[t.id,t.grupo,t.prio||'',t.tarefa,t.inicio||'',t.fim||'',t.conc||'',calcDays(t.inicio,t.fim),t.status,t.pct,t.resp||'',(t.obs||'').replace(/[\r\n;]/g,' '),d>0?d:'',d>0?(t.delayResp||''):''].join(';');
    (p.extraCols||[]).forEach(ec=>row+=';'+(t.extra&&t.extra[ec.id]!=null?t.extra[ec.id]:''));
    csv+=row+'\n';
  });
  const a=document.createElement('a'); a.href=URL.createObjectURL(new Blob([csv],{type:'text/csv;charset=utf-8'}));
  a.download='gantt_'+todayStr+'.csv'; a.click();
  toast('CSV baixado!');
}

// XLSX EXPORT (via SheetJS)
function exportXLSX(){
  if(typeof XLSX==='undefined'){ toast('Biblioteca Excel não carregou. Tente novamente.'); return; }
  const p=getProject(); if(!p) return;

  const headers=['ID','GRUPO','PRIORIDADE','TAREFA','INÍCIO','FIM PREV.','CONCLUSÃO','DIAS','STATUS','%','RESPONSÁVEL','OBS','DIAS DE ATRASO','RESP. ATRASO'];
  (p.extraCols||[]).forEach(ec=>headers.push(ec.name));

  // Ordenar por grupo e depois por início — espelha a visão em tela
  const ordered = [...p.tasks].sort((a,b)=>{
    if(a.grupo!==b.grupo) return a.grupo.localeCompare(b.grupo);
    return (a.inicio||'').localeCompare(b.inicio||'');
  });

  const rows = ordered.map(t=>{
    const d=getDelay(t);
    const base=[t.id,t.grupo,t.prio||'',t.tarefa,t.inicio||'',t.fim||'',t.conc||'',calcDays(t.inicio,t.fim),t.status,t.pct,t.resp||'',t.obs||'',d>0?d:'',d>0?(t.delayResp||''):''];
    (p.extraCols||[]).forEach(ec=>base.push(t.extra&&t.extra[ec.id]!=null?t.extra[ec.id]:''));
    return base;
  });

  const wb = XLSX.utils.book_new();
  const ws = XLSX.utils.aoa_to_sheet([headers, ...rows]);

  // Larguras de coluna
  const colWidths = [
    {wch:6},{wch:18},{wch:12},{wch:48},{wch:12},{wch:12},{wch:12},{wch:6},{wch:16},{wch:6},
    {wch:24},{wch:48},{wch:14},{wch:14}
  ];
  (p.extraCols||[]).forEach(ec=>colWidths.push({wch:Math.max(10,Math.min(40,Math.round((ec.width||100)/8)))}));
  ws['!cols']=colWidths;

  // Freeze header row
  ws['!freeze']={xSplit:0,ySplit:1};

  XLSX.utils.book_append_sheet(wb, ws, 'Cronograma');

  // ===== Aba 2: Resumo por Grupo =====
  const sumHeaders=['GRUPO','TOTAL','REALIZADAS','EM ANDAMENTO','PENDENTES','BLOQUEADAS','ATRASADAS (PRAZO)','REALIZADAS COM ATRASO','% CONCLUSÃO'];
  const sumRows=[];
  for(const g in p.groups){
    const gT=p.tasks.filter(t=>t.grupo===g);
    const done=gT.filter(t=>t.status==='REALIZADO').length;
    const prog=gT.filter(t=>t.status==='EM ANDAMENTO').length;
    const pend=gT.filter(t=>t.status==='NÃO INICIADO').length;
    const blok=gT.filter(t=>t.status==='BLOQUEADO').length;
    const late=gT.filter(t=>isLate(t)).length;
    const delayed=gT.filter(t=>getDelay(t)>0).length;
    const pct=gT.length?Math.round(done/gT.length*100):0;
    sumRows.push([g,gT.length,done,prog,pend,blok,late,delayed,pct+'%']);
  }
  const ws2=XLSX.utils.aoa_to_sheet([sumHeaders,...sumRows]);
  ws2['!cols']=[{wch:20},{wch:8},{wch:12},{wch:14},{wch:12},{wch:12},{wch:18},{wch:22},{wch:14}];
  XLSX.utils.book_append_sheet(wb, ws2, 'Resumo');

  // ===== Aba 3: Atrasos =====
  const lateHeaders=['GRUPO','TAREFA','FIM PREVISTO','CONCLUSÃO','DIAS DE ATRASO','RESP. ATRASO','RESPONSÁVEL','OBS'];
  const lateRows=ordered.filter(t=>getDelay(t)>0)
    .map(t=>[t.grupo,t.tarefa,t.fim,t.conc,getDelay(t),t.delayResp||'(sem responsável)',t.resp||'',t.obs||'']);
  const ws3=XLSX.utils.aoa_to_sheet([lateHeaders,...lateRows]);
  ws3['!cols']=[{wch:18},{wch:48},{wch:14},{wch:14},{wch:16},{wch:18},{wch:24},{wch:48}];
  XLSX.utils.book_append_sheet(wb, ws3, 'Atrasos');

  const fname='gantt_'+esc(p.name).replace(/[^\w]+/g,'_').toLowerCase()+'_'+todayStr+'.xlsx';
  XLSX.writeFile(wb, fname);
  toast('Excel baixado!');
}

// =============================================
// PRESENTATION
// =============================================
function startPresentation(){
  const p=getProject(); if(!p) return;
  state.presIdx=0; state.presSlides=[];
  const cont=document.getElementById('pres-content'); cont.innerHTML='';
  document.getElementById('pres-proj-name').textContent=p.name;
  document.getElementById('pres-date-label').textContent=new Date().toLocaleDateString('pt-BR',{weekday:'long',day:'numeric',month:'long',year:'numeric'});

  const total=p.tasks.length,done=p.tasks.filter(t=>t.status==='REALIZADO').length,prog=p.tasks.filter(t=>t.status==='EM ANDAMENTO').length,pend=p.tasks.filter(t=>t.status==='NÃO INICIADO').length,late=p.tasks.filter(t=>isLate(t)).length,pct=total?Math.round(done/total*100):0;

  const addSlide=html=>{ const s=document.createElement('div'); s.className='slide'; s.innerHTML=html; cont.appendChild(s); state.presSlides.push(s); };

  // Slide 1 capa
  addSlide(`<div class="slide-label">Status Report · Gerência</div><div style="font-size:36px;font-weight:600;margin-bottom:12px">${esc(p.name)}</div>
    <div style="font-size:14px;color:var(--muted)">${new Date().toLocaleDateString('pt-BR',{weekday:'long',day:'numeric',month:'long',year:'numeric'})}</div>
    <div class="kpi-grid-4">
      <div class="slide-kpi"><div class="slide-kpi-val" style="color:var(--accent)">${pct}%</div><div class="slide-kpi-label">Progresso</div></div>
      <div class="slide-kpi"><div class="slide-kpi-val" style="color:var(--done)">${done}</div><div class="slide-kpi-label">Concluídas</div></div>
      <div class="slide-kpi"><div class="slide-kpi-val" style="color:var(--muted)">${pend}</div><div class="slide-kpi-label">Pendentes</div></div>
      <div class="slide-kpi"><div class="slide-kpi-val" style="color:var(--blok)">${late}</div><div class="slide-kpi-label">Atrasadas</div></div>
    </div>`);

  // Slide 2 progresso
  addSlide(`<div class="slide-label">Visão Geral</div><div style="font-size:22px;font-weight:600">Progresso do Projeto</div>
    <div class="prog-big"><div class="prog-big-fill" style="width:${pct}%"></div></div>
    <div style="font-size:30px;font-weight:600;color:var(--accent);text-align:right">${pct}%</div>
    <div class="kpi-grid-2">
      <div class="slide-kpi"><div class="slide-kpi-val" style="color:var(--done)">✓ ${done}</div><div class="slide-kpi-label">Concluídas</div></div>
      <div class="slide-kpi"><div class="slide-kpi-val" style="color:var(--prog)">▶ ${prog}</div><div class="slide-kpi-label">Em Andamento</div></div>
      <div class="slide-kpi"><div class="slide-kpi-val" style="color:var(--muted)">○ ${pend}</div><div class="slide-kpi-label">Pendentes</div></div>
      <div class="slide-kpi"><div class="slide-kpi-val" style="color:var(--blok)">⚠ ${late}</div><div class="slide-kpi-label">Atrasadas</div></div>
    </div>`);

  // Group slides
  for(const gname in p.groups){
    const gc=p.groups[gname].color;
    const gtasks=p.tasks.filter(t=>t.grupo===gname).sort((a,b)=>(a.inicio||'').localeCompare(b.inicio||''));
    const gd=gtasks.filter(t=>t.status==='REALIZADO').length, gl=gtasks.filter(t=>isLate(t)).length;
    const gpct=gtasks.length?Math.round(gd/gtasks.length*100):0;
    const rows=gtasks.map(t=>`<div class="slide-row">
      <span class="prio-dot" style="background:${PRIO_COLORS[t.prio]||PRIO_COLORS['']};flex-shrink:0"></span>
      <span class="slide-name">${esc(t.tarefa)}${isLate(t)?'<span class="badge-late" style="margin-left:6px">ATRASADA</span>':''}</span>
      <span style="font-size:11px;color:var(--muted)">${fmtDate(t.inicio)}→${fmtDate(t.fim)}</span>
      <span style="font-size:11px;min-width:80px;overflow:hidden;text-overflow:ellipsis">${esc(t.resp||'')}</span>
      <div style="width:40px;height:4px;background:var(--s3);border-radius:2px;flex-shrink:0"><div style="width:${t.pct}%;height:100%;background:${gc};border-radius:2px"></div></div>
      <span style="font-size:10px">${t.pct}%</span>
    </div>`).join('');
    addSlide(`<div style="display:flex;align-items:center;gap:8px;margin-bottom:4px"><span style="width:12px;height:12px;border-radius:50%;background:${gc};display:inline-block"></span><span style="font-size:10px;color:var(--muted);font-weight:500;text-transform:uppercase">Grupo</span></div>
      <div style="font-size:28px;font-weight:600;color:${gc};margin-bottom:8px">${esc(gname)}</div>
      <div style="font-size:13px;color:var(--muted);margin-bottom:12px">${gd}/${gtasks.length} concluídas${gl>0?` · <span style="color:var(--blok)">${gl} atrasada${gl>1?'s':''}</span>`:''}</div>
      <div class="prog-big" style="height:8px;margin-bottom:4px"><div class="prog-big-fill" style="width:${gpct}%;background:${gc}"></div></div>
      <div style="font-size:14px;font-weight:600;color:${gc};text-align:right;margin-bottom:16px">${gpct}%</div>
      <div style="max-height:360px;overflow-y:auto">${rows}</div>`);
  }

  // Final slide
  const upcoming=p.tasks.filter(t=>t.status!=='REALIZADO').sort((a,b)=>(a.fim||'').localeCompare(b.fim||'')).slice(0,8);
  const upRows=upcoming.map((t,i)=>{
    const dl=t.fim?Math.round((new Date(t.fim)-TODAY)/86400000):999;
    const pill=isLate(t)?`<span class="final-pill" style="background:rgba(233,92,36,.15);color:var(--blok)">ATRASADA</span>`:dl===0?`<span class="final-pill" style="background:rgba(244,197,0,.25);color:var(--brown)">HOJE</span>`:`<span class="final-pill" style="background:var(--s2);color:var(--muted)">${dl}d</span>`;
    return `<div class="slide-row"><span class="final-num">${String(i+1).padStart(2,'0')}</span><span class="slide-name">${esc(t.tarefa)}</span><span style="font-size:11px;font-weight:500;color:${p.groups[t.grupo]?.color||'var(--accent)'}">${esc(t.grupo)}</span><span style="font-size:11px;color:var(--muted)">${fmtDate(t.fim)}</span>${pill}</div>`;
  }).join('');
  addSlide(`<div class="slide-label">Próximas Ações</div><div style="font-size:22px;font-weight:600;margin-bottom:20px">Top tarefas em aberto</div>${upRows||'<div style="color:var(--muted)">Tudo concluído! 🎉</div>'}`);

  document.getElementById('present').style.display='flex';
  renderSlide();
}

function renderSlide(){
  state.presSlides.forEach((s,i)=>s.className='slide'+(i===state.presIdx?' active':''));
  document.getElementById('pres-counter').textContent=(state.presIdx+1)+'/'+state.presSlides.length;
  document.getElementById('pres-prog').style.width=(((state.presIdx+1)/state.presSlides.length)*100)+'%';
}
function moveSlide(d){ state.presIdx=Math.max(0,Math.min(state.presSlides.length-1,state.presIdx+d)); renderSlide(); }
function closePresentation(){ document.getElementById('present').style.display='none'; }

// =============================================
// COLORBLIND MODE
// =============================================
const CB_MODES = [
  { cls: '',                label: '👁 Daltonismo',    tip: 'Normal'             },
  { cls: 'cb-deuteranopia', label: '🔵 Deuteranopia', tip: 'Deuteranopia/Protanopia (sem vermelho/verde)' },
  { cls: 'cb-tritanopia',   label: '🟢 Tritanopia',   tip: 'Tritanopia (sem azul/amarelo)'  },
  { cls: 'cb-achroma',      label: '⬜ Acromatopsia', tip: 'Acromatopsia (escala de cinza)' },
  { cls: 'cb-contrast',     label: '⚫ Alto Contraste',tip: 'Alto contraste'      },
];
let _cbIdx = 0;

function cycleColorblindMode(){
  // remove current
  CB_MODES.forEach(m=>{ if(m.cls) document.body.classList.remove(m.cls); });
  _cbIdx = (_cbIdx + 1) % CB_MODES.length;
  const mode = CB_MODES[_cbIdx];
  if(mode.cls) document.body.classList.add(mode.cls);
  const btn = document.getElementById('tb-a11y');
  btn.textContent = mode.label;
  btn.title = mode.tip;
  toast(mode.tip);
}

// =============================================
// BOOT
// =============================================
init();
</script>
</body>
</html>
