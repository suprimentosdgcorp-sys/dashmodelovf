<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Dashboard · Modelo Panificadora</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700;900&display=swap" rel="stylesheet">
<style>
:root {
  --bg:#F7F3EE; --surface:#FFFFFF; --surface2:#FDF8F2; --surface3:#F2EBE0;
  --border:#E8DDD0; --border2:#D4C4A8;
  --gold:#B8860B; --gold2:#D4A017; --amber:#C47A00; --orange:#E8880A;
  --red:#B83232; --green:#2E7D4F;
  --text:#2C1F0A; --text2:#6B4E1A; --text3:#9B7A3A; --text4:#C4A870;
}
*{box-sizing:border-box;margin:0;padding:0;}
body{background:var(--bg);color:var(--text);font-family:'Roboto',sans-serif;font-size:13px;min-height:100vh;}

/* LOADING */
#loading{position:fixed;inset:0;background:var(--text);display:flex;flex-direction:column;align-items:center;justify-content:center;z-index:999;gap:14px;}
.spinner{width:38px;height:38px;border-radius:50%;border:3px solid rgba(212,160,23,0.2);border-top-color:var(--gold2);animation:spin .8s linear infinite;}
@keyframes spin{to{transform:rotate(360deg);}}
#loading p{color:var(--text4);font-size:12px;}
#err-msg{display:none;color:#E88080;font-size:12px;text-align:center;max-width:380px;line-height:1.6;padding:0 20px;}

/* HEADER */
.header{background:var(--text);padding:0 28px;display:flex;align-items:center;justify-content:space-between;height:62px;border-bottom:3px solid var(--gold2);}
.logo-mark{width:40px;height:40px;background:linear-gradient(135deg,var(--orange),var(--gold2));border-radius:9px;display:flex;align-items:center;justify-content:center;font-family:'Roboto',sans-serif;font-weight:700;font-size:21px;font-weight:900;color:#1A1208;}
.brand-name{font-family:'Roboto',sans-serif;font-weight:700;font-size:17px;font-weight:700;color:#F5E9CC;margin-left:12px;}
.brand-sub{font-size:10px;color:var(--text4);letter-spacing:1.2px;text-transform:uppercase;margin-left:12px;margin-top:1px;}
.hdr-right{display:flex;align-items:center;gap:10px;}
.badge{font-size:10px;font-weight:600;padding:4px 10px;border-radius:20px;letter-spacing:0.4px;}
.badge-period{background:rgba(212,160,23,0.15);color:var(--gold2);border:1px solid rgba(212,160,23,0.3);}
.badge-unit{background:rgba(255,255,255,0.07);color:var(--text4);border:1px solid rgba(255,255,255,0.1);}
.btn-refresh{background:rgba(255,255,255,0.07);border:1px solid rgba(212,160,23,0.3);color:var(--text4);padding:6px 12px;border-radius:8px;font-size:11px;cursor:pointer;display:flex;align-items:center;gap:6px;transition:all .2s;}
.btn-refresh:hover{border-color:var(--gold2);color:var(--gold2);}
.btn-refresh.spinning svg{animation:spin .8s linear infinite;}
#upd-time{font-size:10px;color:var(--text4);}

/* ABAS */
.tabs-bar{background:var(--surface);border-bottom:1px solid var(--border);padding:0 28px;display:flex;align-items:center;}
.tab-btn{padding:14px 22px;font-size:12px;font-weight:600;color:var(--text3);border:none;background:none;cursor:pointer;border-bottom:3px solid transparent;transition:all .2s;display:flex;align-items:center;gap:7px;}
.tab-btn:hover{color:var(--text2);}
.tab-btn.active{color:var(--gold);border-bottom-color:var(--gold2);}

/* FILTROS */
.filters{background:var(--surface2);border-bottom:1px solid var(--border);padding:10px 28px;display:flex;align-items:center;gap:10px;flex-wrap:wrap;}
.flbl{font-size:10px;font-weight:600;color:var(--text3);text-transform:uppercase;letter-spacing:.8px;}
select,input[type=text]{background:var(--surface);border:1px solid var(--border2);color:var(--text);border-radius:7px;padding:6px 10px;font-size:11px;font-family:'Roboto',sans-serif;outline:none;cursor:pointer;}
select:focus,input:focus{border-color:var(--gold2);box-shadow:0 0 0 2px rgba(212,160,23,.12);}
.cnt-badge{background:rgba(184,134,11,.1);color:var(--gold);font-size:10px;font-weight:700;padding:3px 9px;border-radius:20px;border:1px solid rgba(184,134,11,.2);}

/* CONTENT */
#content{display:none;}
.tab-pane{display:none;}
.tab-pane.active{display:block;}
.main{padding:20px 28px;display:flex;flex-direction:column;gap:16px;}

.sec{font-size:10px;font-weight:700;color:var(--text3);text-transform:uppercase;letter-spacing:1.3px;display:flex;align-items:center;gap:10px;}
.sec::after{content:'';flex:1;height:1px;background:var(--border);}

/* KPI */
.kpi-row{display:grid;grid-template-columns:repeat(5,1fr);gap:12px;}
.kpi{background:var(--surface);border:1px solid var(--border);border-radius:12px;padding:16px 18px;border-top:3px solid var(--kt,var(--gold2));box-shadow:0 1px 4px rgba(44,31,10,.06);}
.kpi-lbl{font-size:10px;font-weight:600;color:var(--text3);text-transform:uppercase;letter-spacing:.9px;margin-bottom:7px;}
.kpi-val{font-family:'Roboto',sans-serif;font-weight:700;font-size:24px;color:var(--text);line-height:1;}
.kpi-sub{font-size:10px;color:var(--text3);margin-top:5px;}
.kpi-delta{font-size:11px;font-weight:600;margin-top:4px;}
.up{color:var(--green);} .down{color:var(--red);}

/* CARDS */
.card{background:var(--surface);border:1px solid var(--border);border-radius:12px;padding:18px;box-shadow:0 1px 4px rgba(44,31,10,.05);}
.card-title{font-size:11px;font-weight:700;color:var(--text2);text-transform:uppercase;letter-spacing:.7px;margin-bottom:14px;display:flex;align-items:center;justify-content:space-between;}
.card-title span{font-weight:400;color:var(--text3);font-size:10px;text-transform:none;letter-spacing:0;}

/* ANÁLISE */
.analise{background:linear-gradient(135deg,#FDF6E8,#FFF9F0);border:1px solid #E8D4A0;border-left:4px solid var(--gold2);border-radius:0 10px 10px 0;padding:14px 18px;}
.analise-title{font-size:11px;font-weight:700;color:var(--gold);text-transform:uppercase;letter-spacing:.8px;margin-bottom:8px;}
.analise-text{font-size:12px;color:var(--text2);line-height:1.75;}
.analise-text strong{color:var(--text);font-weight:600;}

/* TABELAS */
.tw{overflow:hidden;border-radius:8px;border:1px solid var(--border);}
table{width:100%;border-collapse:collapse;font-size:11px;}
thead tr{background:#F7F0E6;}
th{text-align:left;padding:9px 12px;font-size:10px;font-weight:700;color:var(--text2);text-transform:uppercase;letter-spacing:.6px;border-bottom:1px solid var(--border);}
td{padding:9px 12px;border-bottom:1px solid #F0E8D8;color:var(--text2);}
tbody tr:last-child td{border-bottom:none;}
tbody tr:hover td{background:#FDF8F0;}
.tdn{color:var(--text);font-weight:500;}
.tdv{text-align:right;color:var(--text);font-weight:600;font-variant-numeric:tabular-nums;}
.tdr{text-align:right;}
.pill{display:inline-block;padding:2px 8px;border-radius:20px;font-size:10px;font-weight:700;}
.pill-g{background:#E8F5EE;color:var(--green);}
.pill-r{background:#FAEAEA;color:var(--red);}
.rb-track{height:4px;background:#EDE0C8;border-radius:2px;margin-top:3px;}
.rb-fill{height:100%;border-radius:2px;background:var(--gold2);}

/* GRIDS */
.g2{display:grid;grid-template-columns:1fr 1fr;gap:14px;}
.g3{display:grid;grid-template-columns:1fr 1fr 1fr;gap:14px;}
.g32{display:grid;grid-template-columns:1.6fr 1fr;gap:14px;}

/* SEMANAS */
.sem-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:10px;}
.sem-card{background:var(--surface);border:1px solid var(--border);border-top:2px solid var(--border2);border-radius:10px;padding:12px 14px;}
.sem-lbl{font-size:10px;color:var(--text3);text-transform:uppercase;letter-spacing:.6px;margin-bottom:4px;}
.sem-val{font-family:'Roboto',sans-serif;font-weight:700;font-size:20px;color:var(--text);}
.sem-delta{font-size:10px;margin-top:3px;font-weight:600;}

/* CATEGORIAS */
.cat-item{display:flex;align-items:center;gap:10px;padding:8px 0;border-bottom:1px solid #F0E8D8;}
.cat-item:last-child{border-bottom:none;}
.cat-dot{width:10px;height:10px;border-radius:3px;flex-shrink:0;}
.cat-name{flex:1;font-size:11px;color:var(--text2);}
.cat-val{font-size:12px;font-weight:600;color:var(--text);font-variant-numeric:tabular-nums;}
.cat-pct{font-size:10px;color:var(--text3);width:38px;text-align:right;}

.legend{display:flex;flex-wrap:wrap;gap:8px;margin-bottom:10px;}
.li{display:flex;align-items:center;gap:5px;font-size:11px;color:var(--text2);}
.ld{width:9px;height:9px;border-radius:2px;flex-shrink:0;}

.footer{text-align:center;padding:12px;font-size:10px;color:var(--text3);border-top:1px solid var(--border);margin-top:4px;}
.footer strong{color:var(--gold);}

/* ── RESPONSIVE MOBILE ───────────────────────────────────── */
@media (max-width: 768px) {

  /* Header compacto */
  .header { height:auto; padding:10px 14px; flex-wrap:wrap; gap:8px; }
  .brand-name { font-size:15px; }
  .brand-sub  { font-size:9px; }
  .logo-mark  { width:34px; height:34px; font-size:18px; }
  .hdr-right  { gap:6px; flex-wrap:wrap; }
  #upd-time   { display:none; }
  .badge      { font-size:9px; padding:3px 8px; }
  .btn-refresh span { display:none; }

  /* Abas menores */
  .tabs-bar { padding:0 10px; }
  .tab-btn  { padding:10px 12px; font-size:11px; gap:4px; }
  .tab-icon { font-size:13px; }

  /* Filtros empilhados */
  .filters  { padding:10px 14px; gap:8px; }
  .filters select, .filters input { min-width:0; width:100%; }
  .flbl     { display:none; }

  /* Main padding */
  .main { padding:14px; gap:14px; }

  /* KPI: 2 colunas no mobile */
  .kpi-row { grid-template-columns:repeat(2,1fr); gap:8px; }
  .kpi-row .kpi:last-child { grid-column:1 / -1; }
  .kpi-val  { font-size:20px; }
  .kpi-lbl  { font-size:9px; }
  .kpi-sub  { font-size:9px; }
  .kpi-delta{ font-size:10px; }
  .kpi      { padding:12px 14px; }

  /* Grids: 1 coluna */
  .g2, .g3, .g32, .g23 { grid-template-columns:1fr; }

  /* Cards */
  .card { padding:14px; }
  .card-title { font-size:10px; margin-bottom:10px; }

  /* Semanas: 2 colunas */
  .sem-grid { grid-template-columns:1fr 1fr; gap:8px; }
  .sem-val  { font-size:17px; }

  /* Análise */
  .analise { padding:12px 14px; }
  .analise-text { font-size:11px; }

  /* Tabelas: scroll horizontal */
  .tw { overflow-x:auto; -webkit-overflow-scrolling:touch; }
  table { min-width:480px; }
  td, th { padding:7px 8px; font-size:10px; }

  /* Gráficos: altura menor */
  canvas { max-height:180px; }

  /* Categorias */
  .cat-val { font-size:11px; }
  .cat-pct { font-size:9px; }

  /* Footer */
  .footer { font-size:9px; padding:10px 14px; }

  /* Seção labels */
  .sec { font-size:9px; letter-spacing:1px; }
}

@media (max-width: 400px) {
  .kpi-row { grid-template-columns:1fr 1fr; }
  .tab-btn  { padding:8px 10px; font-size:10px; }
  .brand-name { font-size:13px; }
}
</style>
</head>
<body>

<!-- LOADING -->
<div id="loading">
  <div class="logo-mark" style="width:48px;height:48px;font-size:26px;border-radius:12px">M</div>
  <div class="spinner"></div>
  <p>Carregando dados da planilha...</p>
  <p id="err-msg"></p>
</div>

<!-- CONTEÚDO -->
<div id="content">

<div class="header">
  <div style="display:flex;align-items:center">
    <img src="https://modelopanificadora.com.br/wp-content/webp-express/webp-images/uploads/2024/06/LOGO-PNG-5-1536x1186.png.webp"
         alt="Modelo Panificadora"
         onerror="this.style.display='none';document.getElementById('logo-fallback').style.display='flex';"
         style="height:50px;width:auto;object-fit:contain;filter:brightness(0) invert(1);">
    <div id="logo-fallback" style="display:none;width:42px;height:42px;background:linear-gradient(135deg,#E8880A,#D4A017);border-radius:9px;align-items:center;justify-content:center;font-family:'Roboto',sans-serif;font-size:21px;font-weight:900;color:#1A1208">M</div>
    <div style="margin-left:12px">
      <div style="font-family:'Roboto',sans-serif;font-size:17px;font-weight:700;color:#F5E9CC;letter-spacing:0.3px">Modelo Panificadora</div>
      <div style="font-size:10px;color:#C4A870;letter-spacing:1.2px;text-transform:uppercase;margin-top:1px">Dashboard Gerencial · Desde 1979</div>
    </div>
  </div>
  <div class="hdr-right">
    <span id="upd-time"></span>
    <span id="bdg-meta" class="badge badge-period">Carregando...</span>
    <span id="bdg-periodo" class="badge badge-unit">—</span>
    <button class="btn-refresh" onclick="carregarTudo(true)">
      <svg id="ref-icon" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
        <path d="M3 12a9 9 0 0 1 9-9 9.75 9.75 0 0 1 6.74 2.74L21 8"/><path d="M21 3v5h-5"/>
        <path d="M21 12a9 9 0 0 1-9 9 9.75 9.75 0 0 1-6.74-2.74L3 16"/><path d="M8 16H3v5"/>
      </svg>
      Atualizar
    </button>
  </div>
</div>

<div class="tabs-bar">
  <button class="tab-btn active" onclick="trocarAbaComBadge('vendas',this)">📈 Vendas</button>
  <button class="tab-btn" onclick="trocarAbaComBadge('despesas',this)">💸 Despesas</button>
  <button class="tab-btn" onclick="trocarAbaComBadge('consolidado',this)">📊 Consolidado</button>
</div>

<div class="filters">
  <span class="flbl">Filial:</span>
  <select id="selFilial" onchange="trocarFilial(this.value)">
    <option value="frei">Frei Serafim</option>
    <option value="eliseu">Eliseu Martins</option>
    <option value="petronio">Petrônio</option>
  </select>
  <span class="flbl" style="margin-left:4px">Categoria desp.:</span>
  <select id="filtCat" onchange="filtrarForn()">
    <option value="todos">Todas</option>
    <option value="Pessoal">Pessoal</option>
    <option value="Fornecedor">Fornecedor</option>
    <option value="Fixo">Fixo</option>
    <option value="Impostos">Impostos</option>
    <option value="Operacional">Operacional</option>
  </select>
<span class="cnt-badge" id="cntForn">—</span>
</div>

<!-- ABA VENDAS -->
<div id="tab-vendas" class="tab-pane active">
<div class="main">
  <div id="analise-vendas" class="analise"><div class="analise-title">💡 Análise do período</div><div class="analise-text" id="txt-analise-vendas">Carregando...</div></div>
  <div class="sec">Indicadores do mês</div>
  <div class="kpi-row">
    <div class="kpi" style="--kt:var(--gold2)"><div class="kpi-lbl">Receita Bruta</div><div class="kpi-val" id="kv-receita">—</div><div class="kpi-sub" id="ks-receita">—</div></div>
    <div class="kpi" style="--kt:var(--red)"><div class="kpi-lbl">Desp. Escritório</div><div class="kpi-val" id="kv-desc">—</div><div class="kpi-sub" id="ks-desc">—</div></div>
    <div class="kpi" style="--kt:var(--amber)"><div class="kpi-lbl">Desp. Caixa</div><div class="kpi-val" id="kv-desp">—</div><div class="kpi-sub" id="ks-desp">—</div></div>
    <div class="kpi" style="--kt:var(--green)"><div class="kpi-lbl">Lucro Operacional</div><div class="kpi-val" id="kv-lucro" style="color:var(--green)">—</div><div class="kpi-sub">Receita – Despesas</div></div>
    <div class="kpi" style="--kt:var(--red)"><div class="kpi-lbl">% de Meta</div><div class="kpi-val" id="kv-meta">—</div><div class="kpi-sub">Meta: 20%</div></div>
  </div>

  <div class="sec">Análise de vendas</div>
  <div class="g32">
    <div class="card">
      <div class="card-title">Receita diária vs Meta <span id="periodo-graf">—</span></div>
      <div class="legend">
        <span class="li"><span class="ld" style="background:var(--green)"></span>Bateu meta</span>
        <span class="li"><span class="ld" style="background:var(--gold2)"></span>Abaixo da meta</span>
        <span class="li"><span class="ld" style="background:var(--red);width:18px;height:3px;border-radius:0"></span>Linha de meta</span>
      </div>
      <div style="position:relative;width:100%;height:210px"><canvas id="cVendasDia"></canvas></div>
    </div>
    <div class="card">
      <div class="card-title">Mix de canais</div>
      <div class="legend" id="legCanais"></div>
      <div style="position:relative;width:100%;height:200px"><canvas id="cCanais"></canvas></div>
    </div>
  </div>
  <div class="sec">Detalhamento</div>
  <div class="g2">
    <div class="card">
      <div class="card-title">Top dias por receita</div>
      <div class="tw"><table><thead><tr><th>#</th><th>Data</th><th>Dia</th><th class="tdr">Receita</th><th class="tdr">Meta</th><th class="tdr">Resultado</th></tr></thead><tbody id="tbTopDias"></tbody></table></div>
    </div>
    <div class="card">
      <div class="card-title">Resumo financeiro</div>
      <div class="tw"><table><thead><tr><th>Indicador</th><th class="tdr">Valor</th><th class="tdr">% Receita</th></tr></thead><tbody id="tbResumo"></tbody></table></div>
    </div>
  </div>
</div>
</div>

<!-- ABA DESPESAS -->
<div id="tab-despesas" class="tab-pane">
<div class="main">
  <div class="analise"><div class="analise-title">💡 Análise de despesas</div><div class="analise-text" id="txt-analise-desp">Carregando...</div></div>
  <div class="sec">Indicadores de despesas</div>
  <div class="kpi-row">
    <div class="kpi" style="--kt:var(--red)"><div class="kpi-lbl">Total Despesas</div><div class="kpi-val" id="kd-total">—</div><div class="kpi-sub">Escritório + Extras</div></div>
    <div class="kpi" style="--kt:var(--amber)"><div class="kpi-lbl">Maior Fornecedor</div><div class="kpi-val" id="kd-top" style="font-size:16px">—</div><div class="kpi-sub" id="kd-top-val">—</div></div>
    <div class="kpi" style="--kt:var(--gold2)"><div class="kpi-lbl">Total Fornecedores</div><div class="kpi-val" id="kd-forn">—</div><div class="kpi-sub">No período</div></div>
    <div class="kpi" style="--kt:var(--border2)"><div class="kpi-lbl">Média por Dia</div><div class="kpi-val" id="kd-media">—</div><div class="kpi-sub">Despesa diária média</div></div>
    <div class="kpi" style="--kt:var(--red)"><div class="kpi-lbl">Maior Dia</div><div class="kpi-val" id="kd-pico" style="color:var(--red)">—</div><div class="kpi-sub" id="kd-pico-data">—</div></div>
  </div>
  <div class="sec">Evolução e composição</div>
  <div class="g32">
    <div class="card">
      <div class="card-title">Despesas por dia <span>R$ · período</span></div>
      <div style="position:relative;width:100%;height:200px"><canvas id="cDespDia"></canvas></div>
    </div>
    <div class="card">
      <div class="card-title">Por categoria</div>
      <div id="catDesp"></div>
    </div>
  </div>
  <div class="sec">Ranking de fornecedores</div>
  <div class="g32">
    <div class="card">
      <div class="card-title">Top fornecedores <span id="subtRanking">—</span></div>
      <div class="tw"><table><thead><tr><th>#</th><th>Fornecedor</th><th>Categoria</th><th class="tdr">Valor</th><th class="tdr">%</th><th style="width:90px">Part.</th></tr></thead><tbody id="tbForn"></tbody></table></div>
    </div>
    <div class="card">
      <div class="card-title">Distribuição top 8</div>
      <div class="legend" id="legForn"></div>
      <div style="position:relative;width:100%;height:210px"><canvas id="cFornRosca"></canvas></div>
    </div>
  </div>
</div>
</div>

<!-- ABA CONSOLIDADO -->
<div id="tab-consolidado" class="tab-pane">
<div class="main">
  <div class="analise"><div class="analise-title">📊 Visão consolidada</div><div class="analise-text" id="txt-analise-cons">Carregando...</div></div>

  <div class="sec">Comparativo entre filiais</div>
  <div class="g3">
    <div class="card" style="border-top:3px solid var(--gold2)">
      <div class="card-title">Receita por filial</div>
      <div style="position:relative;width:100%;height:180px"><canvas id="cCompReceita"></canvas></div>
    </div>
    <div class="card" style="border-top:3px solid var(--red)">
      <div class="card-title">% Meta por filial</div>
      <div style="position:relative;width:100%;height:180px"><canvas id="cCompMeta"></canvas></div>
    </div>
    <div class="card" style="border-top:3px solid var(--amber)">
      <div class="card-title">Despesas por filial</div>
      <div style="position:relative;width:100%;height:180px"><canvas id="cCompDesp"></canvas></div>
    </div>
  </div>

  <div class="sec">Top dias por receita · todas as filiais</div>
  <div class="card">
    <div class="card-title">Melhores dias de todas as filiais</div>
    <div class="tw"><table><thead><tr>
      <th>#</th><th>Data</th><th>Dia</th><th>Filial</th>
      <th class="tdr">Receita</th><th class="tdr">Meta</th><th class="tdr">Resultado</th>
    </tr></thead><tbody id="tbTopDiasCons"></tbody></table></div>
  </div>

</div>
</div>

</div><!-- /content -->

<div class="footer"><strong>Modelo Panificadora</strong> &nbsp;·&nbsp; Dashboard Gerencial · Frei Serafim · Desde 1979 &nbsp;·&nbsp; Dados via Google Sheets</div>

<script>
// ── SHEETDB API ──────────────────────────────────────────────
// SheetDB serve os dados direto — sem proxy, sem bloqueio CORS
const SHEETDB = 'https://sheetdb.io/api/v1/rej57eyqhq7ak';

const ABAS = {
  frei:     { vendas:'FREI SERAFIM',   despesas:'DESP. FREI SERAFIM',   label:'Frei Serafim'   },
  eliseu:   { vendas:'ELISEU MARTINS', despesas:'DESP. ELISEU MARTINS', label:'Eliseu Martins' },
  petronio: { vendas:'PETRÔNIO',       despesas:'DESP. PETRÔNIO',       label:'Petrônio'       },
};
const FILIAIS_LISTA = ['frei','eliseu','petronio'];
const ABA_HISTORICO  = 'HISTÓRICO ANUAL';
const ABA_CONSOLIDADO = 'CONSOLIDADO';
const URL_CONSOLIDADO = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTPDfc5OiCH7tQZ8qOBqDOtQIrQLGjEt-a1-SVnup1Z_scXEShgBYzKN8D6QizmmhZyAfgdALhqfVsu/pub?gid=114134815&single=true&output=csv';

let FILIAL_ATIVA = 'frei';

// ── ESTADO GLOBAL ────────────────────────────────────────────
let STATE = {
  dias:[], despesas:[], consolidado:[], fornFiltrados:[],
  filiais: { frei:{dias:[],despesas:[]}, eliseu:{dias:[],despesas:[]}, petronio:{dias:[],despesas:[]} }
};
let CHARTS = {};

// ── UTILITÁRIOS ──────────────────────────────────────────────
const fmt   = v => 'R$' + Number(v).toLocaleString('pt-BR',{minimumFractionDigits:0,maximumFractionDigits:0});
const fmtK  = v => { const n=Number(v); return n>=1000?'R$'+(n/1000).toFixed(1).replace('.',',')+' k':fmt(n); };
const fmtP  = v => (Number(v)*100).toFixed(1).replace('.',',')+' %';
const num   = v => { if(!v||v===''||v==='-')return 0; return parseFloat(String(v).replace(/[R$\s]/g,'').replace(/\./g,'').replace(',','.')) || 0; };

// parseCSV não mais necessário — SheetDB retorna JSON diretamente

// ── PROXY FETCH ──────────────────────────────────────────────
async function fetchCSVDireto(url) {
  // Busca CSV via proxy — mesmo método que funcionou para vendas
  const proxies = [
    u => `https://api.allorigins.win/get?url=${encodeURIComponent(u)}`,
    u => `https://api.allorigins.win/raw?url=${encodeURIComponent(u)}`,
    u => `https://corsproxy.io/?${encodeURIComponent(u)}`,
  ];
  let lastErr = '';
  for(const px of proxies){
    try{
      const r = await fetch(px(url+'&ts='+Date.now()), {cache:'no-store'});
      if(!r.ok) throw new Error('HTTP '+r.status);
      let txt;
      if(px(url).includes('/get?url=')) {
        const j = await r.json();
        txt = j.contents;
      } else {
        txt = await r.text();
      }
      if(!txt || txt.length<20 || txt.trimStart().startsWith('<!')) continue;
      // Parsear CSV e converter para array de objetos usando primeira linha como cabeçalho
      const rows = parseCSV(txt);
      if(rows.length < 2) continue;
      const headers = rows[0];
      return rows.slice(1).map(row => {
        const obj = {};
        headers.forEach((h,i) => { obj[h] = row[i]||''; });
        return obj;
      }).filter(obj => obj[headers[0]] && !String(obj[headers[0]]).toUpperCase().includes('TOTAL'));
    } catch(e){ lastErr=e.message; }
    await new Promise(r=>setTimeout(r,500));
  }
  throw new Error('fetchCSVDireto falhou: '+lastErr);
}

async function fetchAba(nomeAba) {
  const url = `${SHEETDB}?sheet=${encodeURIComponent(nomeAba)}&limit=500`;
  const r = await fetch(url, {cache:'no-store'});
  if(!r.ok) throw new Error(`SheetDB HTTP ${r.status} para aba "${nomeAba}"`);
  const json = await r.json();
  if(!Array.isArray(json) || json.length === 0) return [];
  // Para aba CONSOLIDADO, a chave principal é INDICADOR — não filtrar por DATA
  if(nomeAba === 'CONSOLIDADO') {
    return json.filter(row => String(row['INDICADOR']||'').trim() !== '');
  }
  // Para abas de vendas/despesas, filtrar linhas sem data ou de total
  return json.filter(row => {
    const d = String(row['DATA']||row['MÊS/ANO']||row['ANO']||'').trim();
    return d && !d.toUpperCase().includes('TOTAL');
  });
}

// ── PARSERS (SheetDB retorna objetos JSON com chaves = cabeçalhos) ──
function parseVendas(rows) {
  const dias = [];
  const reData = /^\d{2}[\/\-]\d{2}[\/\-]\d{2,4}$/;
  for(const row of rows){
    // SheetDB usa as chaves do cabeçalho — DATA, DIA, etc.
    const d0 = String(row['DATA']||row['data']||'').trim();
    if(!reData.test(d0)) continue;
    if(d0.toUpperCase().includes('TOTAL')) continue;
    const receita = num(row['RECEITA BRUTA (R$)']||row['ReceitaBruta']||0);
    const meta    = num(row['META DIA (R$)']||row['MetaDia']||16000);
    dias.push({
      data:d0,
      dia:       String(row['DIA']||'').trim(),
      sangria:   num(row['SANGRIA (R$)']||0),
      cartao:    num(row['CARTÃO (R$)']||row['CARTAO (R$)']||0),
      pix:       num(row['PIX (R$)']||0),
      ifood:     num(row['IFOOD (R$)']||0),
      whats:     num(row['WHATSAPP (R$)']||0),
      vendaOnline:num(row['VENDA ONLINE (R$)']||0),
      despCx:    num(row['DESP. CAIXA (R$)']||0),
      despEsc:   num(row['DESP. ESCRIT. (R$)']||0),
      receita, metaDia:meta,
      bateu:  receita>0 && receita>=meta,
      fechado:receita===0,
    });
  }
  return dias;
}

function parseDespesas(rows) {
  const list = [];
  for(const row of rows){
    const forn = String(row['FORNECEDOR']||row['Fornecedor']||'').trim();
    const cat  = String(row['CATEGORIA']||row['Categoria']||'').trim();
    const val  = num(row['VALOR (R$)']||row['Valor']||0);
    const venc = String(row['VENCIMENTO']||row['Vencimento']||'').trim();
    if(!forn || val===0 || forn.toUpperCase().includes('TOTAL')) continue;
    list.push({ mes:row['MÊS/ANO']||'', venc, forn, cat, val, tipo:String(row['TIPO']||'') });
  }
  return list;
}

function parseHistorico(rows) {
  const list = [];
  for(const row of rows){
    const ano = String(row['ANO']||row['Ano']||'').trim();
    const mes = String(row['MÊS']||row['MES']||row['Mes']||'').trim();
    if(!ano || !mes || isNaN(parseInt(ano))) continue;
    if(ano.toUpperCase().includes('TOTAL')) continue;
    list.push({
      ano, mes,
      receita: num(row['RECEITA BRUTA (R$)']||0),
      despEsc: num(row['DESP. ESCRITÓRIO (R$)']||row['DESP. ESCRIT (R$)']||0),
      despCx:  num(row['DESP. CAIXA (R$)']||0),
      pctMeta: num(row['% META']||0),
      lucro:   num(row['LUCRO (R$)']||0),
    });
  }
  return list;
}

// ── RENDERIZAR VENDAS ────────────────────────────────────────
function renderVendas(dias) {
  if(!dias.length) return;
  const totR   = dias.reduce((a,d)=>a+d.receita,0);
  const totDE  = dias.reduce((a,d)=>a+d.despEsc,0);
  const totDC  = dias.reduce((a,d)=>a+d.despCx,0);
  const totL   = totR - totDE - totDC;
  const totS   = dias.reduce((a,d)=>a+d.sangria,0);
  const totC   = dias.reduce((a,d)=>a+d.cartao,0);
  const totP   = dias.reduce((a,d)=>a+d.pix,0);
  const totI   = dias.reduce((a,d)=>a+d.ifood,0);
  const totW   = dias.reduce((a,d)=>a+d.whats,0);
  const pctM   = totR>0?(totR-totDC)/totR:0;
  const nOk    = dias.filter(d=>d.bateu).length;
  const nNok   = dias.filter(d=>!d.bateu&&!d.fechado).length;
  const periodo = dias[0]?.data.substring(3)+' – '+dias[dias.length-1]?.data.substring(3) || '—';

  // Badges header
  const bdgM = document.getElementById('bdg-meta');
  bdgM.textContent = `Meta: ${fmtP(pctM)}`;
  bdgM.className   = 'badge '+(pctM>=0.2?'badge-period':'badge-period');
  bdgM.style.color = pctM>=0.2?'var(--green)':'var(--red)';
  document.getElementById('bdg-periodo').textContent = dias[0]?.data.substring(3)||'—';
  document.getElementById('periodo-graf').textContent = dias[0]?.data+' – '+dias[dias.length-1]?.data;

  // KPIs
  document.getElementById('kv-receita').textContent = fmtK(totR);
  document.getElementById('ks-receita').textContent = `${fmtP(totDE/totR)} da receita`;
  document.getElementById('kv-desc').textContent    = fmtK(totDE);
  document.getElementById('ks-desc').textContent    = `${fmtP(totDE/totR)} da receita`;
  document.getElementById('kv-desp').textContent    = fmtK(totDC);
  document.getElementById('ks-desp').textContent    = `${fmtP(totDC/totR)} da receita`;
  const kl = document.getElementById('kv-lucro');
  kl.textContent  = fmtK(totL);
  kl.style.color  = totL>=0?'var(--green)':'var(--red)';
  const km = document.getElementById('kv-meta');
  km.textContent  = fmtP(pctM);
  km.style.color  = pctM>=0.2?'var(--green)':'var(--red)';


  // Análise automática
  document.getElementById('txt-analise-vendas').innerHTML =
    `O período registrou receita bruta de <strong>${fmtK(totR)}</strong>. `+
    `<strong>${nOk} de ${dias.length}</strong> dias bateram a meta. `+
    `A % de meta ficou em <strong style="color:${pctM>=0.2?'var(--green)':'var(--red)'}">${fmtP(pctM)}</strong> `+
    `(meta: 20%). O cartão domina com <strong>${fmtP(totC/totR)}</strong> da receita, `+
    `seguido de PIX com <strong>${fmtP(totP/totR)}</strong>. `+
    `Despesa de escritório consumiu <strong>${fmtP(totDE/totR)}</strong> de tudo que foi vendido.`;

  // Gráfico diário
  if(CHARTS.vendasDia) CHARTS.vendasDia.destroy();
  CHARTS.vendasDia = new Chart(document.getElementById('cVendasDia'),{
    type:'bar',
    data:{
      labels:dias.map(d=>d.data.substring(0,5)),
      datasets:[
        {label:'Receita',data:dias.map(d=>d.receita),
         backgroundColor:dias.map(d=>d.bateu?'rgba(46,125,79,.75)':'rgba(184,134,11,.6)'),
         borderColor:dias.map(d=>d.bateu?'#2E7D4F':'#B8860B'),
         borderWidth:1,borderRadius:3},
        {label:'Meta',data:dias.map(d=>d.metaDia),type:'line',
         borderColor:'#B83232',borderWidth:2,borderDash:[5,3],pointRadius:0,fill:false,tension:0},
      ]
    },
    options:{responsive:true,maintainAspectRatio:false,
      plugins:{legend:{display:false},tooltip:{callbacks:{label:c=>' '+c.dataset.label+': '+fmt(c.parsed.y)}}},
      scales:{
        x:{ticks:{color:'#9B7A3A',font:{size:9},maxRotation:45,autoSkip:false},grid:{display:false}},
        y:{ticks:{color:'#9B7A3A',font:{size:9},callback:v=>'R$'+(v/1000).toFixed(0)+'k'},grid:{color:'rgba(184,134,11,.08)'}}
      }}
  });

  // Rosca canais
  const canais = [
    {l:'Cartão',v:totC,c:'#B8860B'},{l:'PIX',v:totP,c:'#C47A00'},
    {l:'iFood',v:totI,c:'#D4A017'},{l:'WhatsApp',v:totW,c:'#8B6914'},
    {l:'Sangria',v:totS,c:'#6B4E1A'},
  ].filter(x=>x.v>0);
  document.getElementById('legCanais').innerHTML = canais.map(c=>
    `<span class="li"><span class="ld" style="background:${c.c}"></span>${c.l} ${fmtP(c.v/totR)}</span>`).join('');
  if(CHARTS.canais) CHARTS.canais.destroy();
  CHARTS.canais = new Chart(document.getElementById('cCanais'),{
    type:'doughnut',
    data:{labels:canais.map(c=>c.l),datasets:[{data:canais.map(c=>c.v),backgroundColor:canais.map(c=>c.c),borderColor:'#FFF',borderWidth:2}]},
    options:{responsive:true,maintainAspectRatio:false,cutout:'65%',
      plugins:{legend:{display:false},tooltip:{callbacks:{label:c=>' '+c.label+': '+fmt(c.parsed)+' ('+fmtP(c.parsed/totR)+')'}}}
    }
  });

  // Tabela top dias
  const sd = [...dias].sort((a,b)=>b.receita-a.receita).slice(0,8);
  document.getElementById('tbTopDias').innerHTML = sd.map((d,i)=>{
    const diff = d.receita - d.metaDia;
    return `<tr>
      <td style="color:var(--text3);font-size:10px">${i+1}</td>
      <td class="tdn">${d.data.substring(0,5)}</td>
      <td style="color:var(--text3)">${d.dia}</td>
      <td class="tdv">${fmt(d.receita)}</td>
      <td class="tdr" style="color:var(--text3)">${fmt(d.metaDia)}</td>
      <td class="tdr"><span class="pill ${d.bateu?'pill-g':'pill-r'}">${d.bateu?'▲':'▼'} ${fmt(Math.abs(diff))}</span></td>
    </tr>`;
  }).join('');

  // Tabela resumo
  document.getElementById('tbResumo').innerHTML = [
    ['Receita Bruta',totR,1,'var(--text)'],
    ['(-) Desp. Escritório',-totDE,totDE/totR,'var(--red)'],
    ['(-) Desp. Caixa',-totDC,totDC/totR,'var(--red)'],
    ['Lucro Líquido',totL,totL/totR,totL>=0?'var(--green)':'var(--red)'],
    ['Dias operacionais',null,null,null],
    ['Bateram meta',null,null,null],
  ].map((r,i)=>{
    if(i===4) return `<tr><td class="tdn">Dias operacionais</td><td class="tdv">${dias.length} dias</td><td class="tdr" style="color:var(--text3)">—</td></tr>`;
    if(i===5) return `<tr><td class="tdn">Dias que bateram meta</td><td class="tdv" style="color:var(--green)">${nOk} dias</td><td class="tdr" style="color:var(--green)">${fmtP(nOk/dias.length)}</td></tr>`;
    const bgRow = i===3?'background:#FDF6E8;':'';
    return `<tr style="${bgRow}"><td class="tdn" style="color:${i===3?'var(--gold)':'var(--text)'}">${r[0]}</td><td class="tdv" style="color:${r[3]}">${fmt(Math.abs(r[1]))}</td><td class="tdr" style="color:${r[3]}">${fmtP(r[2])}</td></tr>`;
  }).join('');
}

// ── RENDERIZAR DESPESAS ──────────────────────────────────────
const catCores = {Pessoal:'#C47A00',Fornecedor:'#B8860B',Fixo:'#2563A8',Impostos:'#B83232',Operacional:'#2E7D4F'};
const catBgs   = {Pessoal:'#FFF3E0',Fornecedor:'#FFF8E8',Fixo:'#EEF3FF',Impostos:'#FFF0F0',Operacional:'#F0FFF4'};

function renderDespesas(list) {
  if(!list.length) return;

  // Agregar por fornecedor
  const fornMap = {};
  list.forEach(d=>{ if(!fornMap[d.forn]) fornMap[d.forn]={nome:d.forn,val:0,cat:d.cat}; fornMap[d.forn].val+=d.val; });
  const fornList = Object.values(fornMap).sort((a,b)=>b.val-a.val);
  STATE.fornFiltrados = [...fornList];

  const total = list.reduce((a,d)=>a+d.val,0);
  const topF  = fornList[0];

  // KPIs
  document.getElementById('kd-total').textContent  = fmtK(total);
  document.getElementById('kd-top').textContent    = topF?.nome||'—';
  document.getElementById('kd-top-val').textContent= fmtK(topF?.val||0);
  document.getElementById('kd-forn').textContent   = fornList.length;
  document.getElementById('kd-media').textContent  = fmtK(total/28);

  // Pico diário
  const porData = {};
  list.forEach(d=>{ const k=d.venc||''; if(!k) return; if(!porData[k]) porData[k]=0; porData[k]+=d.val; });
  const pico = Object.entries(porData).sort((a,b)=>b[1]-a[1])[0];
  document.getElementById('kd-pico').textContent      = pico?fmtK(pico[1]):'—';
  document.getElementById('kd-pico-data').textContent = pico?pico[0]:'—';

  // Análise
  const top3 = fornList.slice(0,3).map(f=>f.nome).join(', ');
  document.getElementById('txt-analise-desp').innerHTML =
    `Total de despesas: <strong>${fmtK(total)}</strong> com <strong>${fornList.length} fornecedores</strong>. `+
    `Dia de maior gasto: <strong>${pico?pico[0]:'—'}</strong> com <strong>${pico?fmtK(pico[1]):'—'}</strong>. `+
    `Top 3 fornecedores: <strong>${top3}</strong> — concentram <strong>${fmtP(fornList.slice(0,3).reduce((a,f)=>a+f.val,0)/total)}</strong> do total.`;

  // Gráfico por vencimento
  const diasDesp = Object.entries(porData).sort((a,b)=>a[0].localeCompare(b[0]));
  if(CHARTS.despDia) CHARTS.despDia.destroy();
  CHARTS.despDia = new Chart(document.getElementById('cDespDia'),{
    type:'bar',
    data:{
      labels:diasDesp.map(d=>d[0].substring(0,5)),
      datasets:[{
        data:diasDesp.map(d=>d[1]),
        backgroundColor:diasDesp.map(d=>d[1]>20000?'rgba(184,50,50,.7)':d[1]>10000?'rgba(196,122,0,.7)':'rgba(184,134,11,.55)'),
        borderColor:diasDesp.map(d=>d[1]>20000?'#B83232':d[1]>10000?'#C47A00':'#B8860B'),
        borderWidth:1,borderRadius:3,
      }]
    },
    options:{responsive:true,maintainAspectRatio:false,
      plugins:{legend:{display:false},tooltip:{callbacks:{label:c=>' '+fmt(c.parsed.y)}}},
      scales:{
        x:{ticks:{color:'#9B7A3A',font:{size:9},maxRotation:45,autoSkip:false},grid:{display:false}},
        y:{ticks:{color:'#9B7A3A',font:{size:9},callback:v=>'R$'+(v/1000).toFixed(0)+'k'},grid:{color:'rgba(184,134,11,.08)'}}
      }}
  });

  // Categorias
  const catTot = {};
  list.forEach(d=>{ catTot[d.cat]=(catTot[d.cat]||0)+d.val; });
  document.getElementById('catDesp').innerHTML = Object.entries(catTot).sort((a,b)=>b[1]-a[1]).map(([cat,v])=>
    `<div class="cat-item">
      <div class="cat-dot" style="background:${catCores[cat]||'#999'}"></div>
      <div class="cat-name">${cat}</div>
      <div class="cat-val">${fmtK(v)}</div>
      <div class="cat-pct">${fmtP(v/total)}</div>
    </div>`).join('');

  renderRanking(fornList, total);
  document.getElementById('cntForn').textContent = fornList.length+' fornecedores';
}

function renderRanking(lista, total) {
  const maxV = lista[0]?.val||1;
  const top  = lista.slice(0,15);
  document.getElementById('subtRanking').textContent = `${lista.length} fornecedores · ${fmtK(lista.reduce((a,f)=>a+f.val,0))} total`;
  document.getElementById('cntForn').textContent = lista.length+' fornecedores';

  document.getElementById('tbForn').innerHTML = top.map((f,i)=>`
    <tr>
      <td style="color:var(--text3);font-size:10px">${i+1}</td>
      <td class="tdn">${f.nome}</td>
      <td><span style="background:${catBgs[f.cat]||'#eee'};color:${catCores[f.cat]||'#333'};font-size:10px;padding:2px 7px;border-radius:20px;font-weight:600">${f.cat}</span></td>
      <td class="tdv">${fmtK(f.val)}</td>
      <td class="tdr" style="color:var(--text3)">${fmtP(f.val/total)}</td>
      <td style="padding:9px 12px"><div class="rb-track"><div class="rb-fill" style="width:${(f.val/maxV*100).toFixed(0)}%"></div></div></td>
    </tr>`).join('');

  const top8 = lista.slice(0,8);
  const cores8 = ['#B8860B','#C47A00','#D4A017','#8B6914','#B83232','#6B4E1A','#2E7D4F','#2563A8'];
  if(CHARTS.fornRosca) CHARTS.fornRosca.destroy();
  CHARTS.fornRosca = new Chart(document.getElementById('cFornRosca'),{
    type:'doughnut',
    data:{labels:top8.map(f=>f.nome),datasets:[{data:top8.map(f=>f.val),backgroundColor:cores8,borderColor:'#FFF',borderWidth:2}]},
    options:{responsive:true,maintainAspectRatio:false,cutout:'60%',
      plugins:{legend:{display:false},tooltip:{callbacks:{label:c=>' '+fmt(c.parsed)+' ('+fmtP(c.parsed/total)+')'}}}
    }
  });
  document.getElementById('legForn').innerHTML = top8.map((f,i)=>
    `<span class="li"><span class="ld" style="background:${cores8[i]}"></span>${f.nome.split(' ')[0]}</span>`).join('');
}

function filtrarForn() {
  const busca = '';
  const cat   = document.getElementById('filtCat').value;
  const base  = Object.values(STATE.despesas.reduce((m,d)=>{
    if(!m[d.forn]) m[d.forn]={nome:d.forn,val:0,cat:d.cat};
    m[d.forn].val+=d.val; return m;
  },{})).sort((a,b)=>b.val-a.val);
  const filt = base.filter(f=>(!busca||f.nome.toLowerCase().includes(busca))&&(cat==='todos'||f.cat===cat));
  renderRanking(filt, base.reduce((a,f)=>a+f.val,0));
}

// ── RENDERIZAR CONSOLIDADO ───────────────────────────────────
function renderConsolidado(rows) {
  if(!rows || rows.length === 0) return;

  // Converte array de objetos SheetDB em mapa por indicador
  const dados = {};
  rows.forEach(row => {
    const ind = String(row['INDICADOR']||'').trim();
    if(!ind) return;
    dados[ind] = {
      frei:  num(row['FREI SERAFIM']),
      elis:  num(row['ELISEU MARTINS']),
      pet:   num(row['PETRÔNIO']),
      total: num(row['CONSOLIDADO']),
    };
  });

  const get = (ind, filial='total') => dados[ind]?.[filial] || 0;

  // Dados por filial
  const filiais = [
    { key:'frei',  label:'Frei Serafim',   cor:'rgba(184,134,11,.8)', borda:'#B8860B' },
    { key:'elis',  label:'Eliseu Martins',  cor:'rgba(26,107,58,.8)',  borda:'#1A6B3A' },
    { key:'pet',   label:'Petrônio',        cor:'rgba(107,26,26,.8)',  borda:'#6B1A1A' },
  ];

  const totR   = get('Receita Bruta','total');
  const totDC  = get('Desp. Caixa','total');
  const totDE  = get('Desp. Escritório','total');
  const pctCons = get('% de Meta','total');
  const lucro  = get('Lucro Operacional','total');

  // Análise
  document.getElementById('txt-analise-cons').innerHTML =
    `Receita consolidada: <strong>${fmtK(totR)}</strong> · `+
    `Lucro: <strong style="color:${lucro>=0?'var(--green)':'var(--red)'}">${fmtK(lucro)}</strong> · `+
    `% meta: <strong style="color:${(pctCons>1?pctCons:pctCons*100)>=20?'var(--green)':'var(--red)'}">${fmtP(pctCons)}</strong>.<br>`+
    filiais.map(f=>`<span style="color:${f.borda};font-weight:600">${f.label}</span>: `+
      `R$ ${fmtK(get('Receita Bruta',f.key)).replace('R$','')} · `+
      `meta ${(v=>v>1?v.toFixed(1).replace('.',',')+' %':fmtP(v))(get('% de Meta',f.key))}`
    ).join(' &nbsp;|&nbsp; ');

  // Badge
  const pctConsNorm = pctCons>1 ? pctCons/100 : pctCons;
  atualizarBadgeMeta(pctConsNorm, FILIAL_ATIVA==='todas'?'Consolidado':ABAS[FILIAL_ATIVA]?.label||'—');

  const scOpts = (yCb) => ({
    x:{ticks:{color:'#9B7A3A',font:{size:9,family:'Roboto'},maxRotation:0},grid:{display:false}},
    y:{ticks:{color:'#9B7A3A',font:{size:9,family:'Roboto'},callback:yCb},grid:{color:'rgba(184,134,11,.06)'}}
  });

  const labels = filiais.map(f=>f.label);

  // Gráfico 1: Receita por filial
  if(CHARTS.consR) CHARTS.consR.destroy();
  CHARTS.consR = new Chart(document.getElementById('cCompReceita'),{
    type:'bar',
    data:{labels,datasets:[{
      label:'Receita',
      data: filiais.map(f=>get('Receita Bruta',f.key)||null),
      backgroundColor:filiais.map(f=>f.cor), borderColor:filiais.map(f=>f.borda),
      borderWidth:1, borderRadius:5,
    }]},
    options:{responsive:true,maintainAspectRatio:false,
      plugins:{legend:{display:false},tooltip:{callbacks:{label:c=>c.parsed.y?' '+fmtK(c.parsed.y):' Sem dados'}}},
      scales:scOpts(v=>'R$'+(v/1000).toFixed(0)+'k')}
  });

  // Gráfico 2: % Meta por filial
  if(CHARTS.consD) CHARTS.consD.destroy();
  CHARTS.consD = new Chart(document.getElementById('cCompMeta'),{
    type:'bar',
    data:{labels,datasets:[{
      label:'% Meta',
      data: filiais.map(f=>{ const v=get('% de Meta',f.key); if(!v) return null; return v>1 ? +v.toFixed(1) : +(v*100).toFixed(1); }),
      backgroundColor: filiais.map(f=>{ const v=get('% de Meta',f.key); const pct=v>1?v:v*100; return pct>=20?'rgba(46,125,79,.75)':'rgba(184,50,50,.75)'; }),
      borderColor:     filiais.map(f=>{ const v=get('% de Meta',f.key); const pct=v>1?v:v*100; return pct>=20?'#2E7D4F':'#B83232'; }),
      borderWidth:1, borderRadius:5,
    }]},
    options:{responsive:true,maintainAspectRatio:false,
      plugins:{legend:{display:false},tooltip:{callbacks:{label:c=>c.parsed.y!=null?' '+c.parsed.y.toFixed(1)+'%':' Sem dados'}}},
      scales:{
        x:{ticks:{color:'#9B7A3A',font:{size:9,family:'Roboto'}},grid:{display:false}},
        y:{ticks:{color:'#9B7A3A',font:{size:9},callback:v=>v+'%'},grid:{color:'rgba(184,134,11,.06)'},min:0,
           afterDataLimits(s){s.max=Math.max(s.max||0,35);}}
      }}
  });

  // Gráfico 3: Despesas por filial
  if(CHARTS.consC) CHARTS.consC.destroy();
  CHARTS.consC = new Chart(document.getElementById('cCompDesp'),{
    type:'bar',
    data:{labels,datasets:[
      {label:'Desp. Escrit.', data:filiais.map(f=>get('Desp. Escritório',f.key)||null), backgroundColor:'rgba(184,50,50,.75)', borderColor:'#B83232',borderWidth:1,borderRadius:3},
      {label:'Desp. Caixa',  data:filiais.map(f=>get('Desp. Caixa',f.key)||null),       backgroundColor:'rgba(196,122,0,.7)',  borderColor:'#C47A00',borderWidth:1,borderRadius:3},
    ]},
    options:{responsive:true,maintainAspectRatio:false,
      plugins:{legend:{labels:{color:'#6B4E1A',font:{size:10,family:'Roboto'}}}},
      scales:scOpts(v=>'R$'+(v/1000).toFixed(0)+'k')}
  });

  // Top dias — usa dados do STATE.dias (filial ativa ou todas)
  const todasDias = FILIAL_ATIVA==='todas' ? [
    ...STATE.filiais.frei.dias.map(d=>({...d,filial:'Frei Serafim',corF:'#B8860B'})),
    ...STATE.filiais.eliseu.dias.map(d=>({...d,filial:'Eliseu Martins',corF:'#1A6B3A'})),
    ...STATE.filiais.petronio.dias.map(d=>({...d,filial:'Petrônio',corF:'#6B1A1A'})),
  ] : STATE.dias.map(d=>({...d,filial:ABAS[FILIAL_ATIVA]?.label||'—',corF:'#B8860B'}));

  const topCons = [...todasDias].sort((a,b)=>b.receita-a.receita).slice(0,10);
  const tbCons  = document.getElementById('tbTopDiasCons');
  if(tbCons){
    tbCons.innerHTML = topCons.length>0 ? topCons.map((d,i)=>{
      const diff=d.receita-d.metaDia;
      return `<tr>
        <td style="color:var(--text3);font-size:10px">${i+1}</td>
        <td class="tdn">${d.data.substring(0,5)}</td>
        <td style="color:var(--text3)">${d.dia}</td>
        <td><span style="background:${d.corF}22;color:${d.corF};font-size:10px;padding:2px 8px;border-radius:20px;font-weight:700">${d.filial}</span></td>
        <td class="tdv">${fmt(d.receita)}</td>
        <td class="tdr" style="color:var(--text3)">${fmt(d.metaDia)}</td>
        <td class="tdr"><span class="pill ${d.bateu?'pill-g':'pill-r'}">${d.bateu?'▲':'▼'} ${fmt(Math.abs(diff))}</span></td>
      </tr>`;
    }).join('') :
    '<tr><td colspan="7" style="text-align:center;color:var(--text3);padding:20px">Selecione "Todas as filiais" para ver o comparativo completo</td></tr>';
  }
}

// ── ABAS ─────────────────────────────────────────────────────
function trocarAba(id, btn){
  document.querySelectorAll('.tab-pane').forEach(t=>t.classList.remove('active'));
  document.querySelectorAll('.tab-btn').forEach(b=>b.classList.remove('active'));
  document.getElementById('tab-'+id).classList.add('active');
  btn.classList.add('active');
}

// ── BADGE META ───────────────────────────────────────────────
function atualizarBadgeMeta(pct, label) {
  const bdg = document.getElementById('bdg-meta');
  bdg.textContent = `${label}: ${fmtP(pct)}`;
  bdg.style.color = pct >= 0.2 ? 'var(--green)' : 'var(--red)';
  bdg.style.borderColor = pct >= 0.2 ? 'rgba(46,125,79,.3)' : 'rgba(184,50,50,.3)';
  bdg.style.background  = pct >= 0.2 ? 'rgba(46,125,79,.1)' : 'rgba(184,50,50,.1)';
}

// ── TROCAR ABA (atualiza badge conforme contexto) ─────────────
function trocarAbaComBadge(abaId, btn) {
  trocarAba(abaId, btn);
  if(abaId === 'consolidado') {
    // Calcular % meta consolidado: (recTotal - despCxTotal) / recTotal
    const todasFiliais = [
      ...STATE.filiais.frei.dias,
      ...STATE.filiais.eliseu.dias,
      ...STATE.filiais.petronio.dias,
    ];
    if(todasFiliais.length > 0) {
      const totR  = todasFiliais.reduce((a,d)=>a+d.receita,0);
      const totDC = todasFiliais.reduce((a,d)=>a+d.despCx,0);
      const pct   = totR>0?(totR-totDC)/totR:0;
      atualizarBadgeMeta(pct, 'Consolidado');
    } else {
      // Usa só os dados da filial ativa como proxy
      const totR  = STATE.dias.reduce((a,d)=>a+d.receita,0);
      const totDC = STATE.dias.reduce((a,d)=>a+d.despCx,0);
      atualizarBadgeMeta(totR>0?(totR-totDC)/totR:0, 'Consolidado');
    }
  } else if(abaId === 'vendas' || abaId === 'despesas') {
    const totR  = STATE.dias.reduce((a,d)=>a+d.receita,0);
    const totDC = STATE.dias.reduce((a,d)=>a+d.despCx,0);
    atualizarBadgeMeta(totR>0?(totR-totDC)/totR:0, ABAS[FILIAL_ATIVA].label);
  }
}

// ── TROCAR FILIAL ────────────────────────────────────────────
function trocarFilial(filial) {
  FILIAL_ATIVA = filial;
  if(filial === 'todas') {
    document.getElementById('bdg-periodo').textContent = 'Todas as filiais';
  } else if(ABAS[filial]) {
    document.getElementById('bdg-periodo').textContent = ABAS[filial].label;
  }
  carregarTudo(true);
}

// ── CARREGAR TUDO ────────────────────────────────────────────
async function carregarTudo(manual=false) {
  const btn = document.querySelector('.btn-refresh');
  btn.classList.add('spinning');
  if(manual){
    document.getElementById('loading').style.display='flex';
    document.getElementById('content').style.display='none';
  }

  let erros = [];

  if(FILIAL_ATIVA === 'todas') {
    // Carrega as 3 filiais em paralelo
    const fl = ['frei','eliseu','petronio'];
    const [dF,dE,dP] = await Promise.all(fl.map(f=>fetchAba(ABAS[f].vendas).then(r=>parseVendas(r)).catch(()=>[])));
    STATE.filiais.frei.dias=dF; STATE.filiais.eliseu.dias=dE; STATE.filiais.petronio.dias=dP;
    STATE.dias = [...dF,...dE,...dP];
    if(STATE.dias.length>0) renderVendas(STATE.dias);
    else erros.push('Vendas: sem dados');

    const [despF,despE,despP] = await Promise.all(fl.map(f=>fetchAba(ABAS[f].despesas).then(r=>parseDespesas(r)).catch(()=>[])));
    STATE.filiais.frei.despesas=despF; STATE.filiais.eliseu.despesas=despE; STATE.filiais.petronio.despesas=despP;
    STATE.despesas = [...despF,...despE,...despP];
    if(STATE.despesas.length>0) renderDespesas(STATE.despesas);

  } else {
    try {
      const rowsV = await fetchAba(ABAS[FILIAL_ATIVA]?.vendas||'FREI SERAFIM');
      STATE.dias = parseVendas(rowsV);
      STATE.filiais[FILIAL_ATIVA].dias = STATE.dias;
      renderVendas(STATE.dias);
    } catch(e) { erros.push('Vendas: '+e.message); }

    try {
      const rowsD = await fetchAba(ABAS[FILIAL_ATIVA]?.despesas||'DESP. FREI SERAFIM');
      STATE.despesas = parseDespesas(rowsD);
      STATE.filiais[FILIAL_ATIVA].despesas = STATE.despesas;
      renderDespesas(STATE.despesas);
    } catch(e) { erros.push('Despesas: '+e.message); }
  }

  // Aba CONSOLIDADO — via SheetDB igual às outras abas
  try {
    const rowsCons = await fetchAba(ABA_CONSOLIDADO);
    STATE.consolidado = rowsCons;
    renderConsolidado(rowsCons);
  } catch(e) { erros.push('Histórico: '+e.message); }

  // Mostra conteúdo mesmo se alguma aba falhou
  const temDados = STATE.dias.length>0 || STATE.despesas.length>0 || STATE.historico.length>0;
  if(temDados){
    document.getElementById('loading').style.display='none';
    document.getElementById('content').style.display='block';
    document.getElementById('upd-time').textContent =
      'Atualizado '+new Date().toLocaleTimeString('pt-BR',{hour:'2-digit',minute:'2-digit'});
    if(erros.length){
      // Aviso discreto no rodapé se alguma aba falhou
      document.querySelector('.footer').innerHTML +=
        ' <span style="color:var(--red);font-size:10px">⚠ '+erros.join(' · ')+'</span>';
    }
  } else {
    document.getElementById('loading').querySelector('p').style.display='none';
    document.querySelector('.spinner').style.display='none';
    const e = document.getElementById('err-msg');
    e.style.display='block';
    e.innerHTML='Erro ao carregar os dados.<br><br>'+erros.join('<br>')+'<br><br>Verifique se as abas estão publicadas no Google Sheets.';
  }

  btn.classList.remove('spinning');
}

carregarTudo();
</script>
</body>
</html>
