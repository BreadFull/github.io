<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Chá de Fraldas</title>
<style>
:root{--bg:#fff8f3;--card:#fff;--tx:#3b2f2f;--mu:#85736c;--ac:#e8836b;--ac2:#fde4dc;--bd:#f0ddd4;--ok:#3f8f5f;
box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#201a19;--card:#2b2322;--tx:#f3e8e4;--mu:#b5a39c;--ac:#f0957e;--ac2:#4a2f29;--bd:#4a3b37;--ok:#6fcf97}}
:root[data-theme="dark"]{--bg:#201a19;--card:#2b2322;--tx:#f3e8e4;--mu:#b5a39c;--ac:#f0957e;--ac2:#4a2f29;--bd:#4a3b37;--ok:#6fcf97}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--tx);font-family:system-ui,-apple-system,"Segoe UI",Roboto,sans-serif;line-height:1.45}
main{max-width:640px;margin:0 auto;padding:18px 16px 110px}
h1{font-size:1.6rem;margin:6px 0 2px;text-align:center}
.sub{text-align:center;color:var(--mu);margin:0 0 16px}
.tabs{display:flex;gap:6px;margin-bottom:14px}
.tabs button{flex:1;padding:10px 4px;border:1px solid var(--bd);background:var(--card);color:var(--tx);border-radius:10px;font-size:.95rem;cursor:pointer}
.tabs button.on{background:var(--ac);color:#fff;border-color:var(--ac)}
.card{background:var(--card);border:1px solid var(--bd);border-radius:14px;padding:14px;margin-bottom:10px}
.row{display:flex;align-items:center;justify-content:space-between;gap:10px}
.nm{font-weight:600}.lim{font-size:.85rem;color:var(--mu)}
.info{font-size:.82rem;color:var(--mu);margin-top:8px;border-top:1px dashed var(--bd);padding-top:8px}
.step{display:flex;align-items:center;gap:8px}
.step button{width:34px;height:34px;border-radius:50%;border:1px solid var(--ac);background:var(--ac2);color:var(--tx);font-size:1.2rem;cursor:pointer}
.step b{min-width:22px;text-align:center}
.full{opacity:.55}
input,select,textarea{width:100%;padding:11px;border:1px solid var(--bd);border-radius:10px;background:var(--card);color:var(--tx);font:inherit;margin-top:6px}
.btn{display:block;width:100%;padding:13px;border:0;border-radius:12px;background:var(--ac);color:#fff;font-size:1rem;font-weight:600;cursor:pointer;margin-top:10px}
.btn.sec{background:var(--ac2);color:var(--tx)}
.bar{position:fixed;left:0;right:0;bottom:0;background:var(--card);border-top:1px solid var(--bd);padding:10px 16px calc(10px + env(safe-area-inset-bottom,0px))}
.bar .in{max-width:640px;margin:0 auto}
.modal{position:fixed;inset:0;background:var(--bg);display:flex;align-items:center;justify-content:center;padding:20px;z-index:5}
.modal .box{max-width:380px;width:100%;text-align:center}
.ov{overflow-x:auto}table{width:100%;border-collapse:collapse;font-size:.88rem}td,th{padding:6px;border-bottom:1px solid var(--bd);text-align:left}
.foot{text-align:center;margin-top:24px}.foot a{color:var(--mu);font-size:.8rem;cursor:pointer;text-decoration:underline}
.msg{white-space:pre-wrap;background:var(--ac2);border-radius:10px;padding:10px;font-size:.9rem;text-align:left}
[hidden]{display:none!important}
</style>
</head>
<body>
<div class="modal" id="gate"><div class="box">
<div style="font-size:3rem">🍼</div>
<h1>Chá de Fraldas</h1>
<p class="sub">Para escolher o que vai doar, diga seu nome:</p>
<input id="nome" placeholder="Seu nome completo" autocomplete="name">
<button class="btn" id="entrar">Entrar</button>
</div></div>

<main id="app" hidden>
<h1>🍼 Chá de Fraldas</h1>
<p class="sub" id="ola"></p>
<div class="tabs" id="tabs"></div>
<div id="view"></div>
<div class="foot"><a id="adm">Área do organizador</a></div>
</main>

<div class="bar" id="bar" hidden><div class="in"><button class="btn" id="enviar" style="margin:0">Confirmar doação</button></div></div>

<script>
// ===== CONFIGURAÇÃO =====
var WHATS = "5553997135792";
var LOGIN = "Bread", PIN = "1660";
var FORM_URL = "";       // link do Google Forms (preencher depois)
var TAKEN = {};          // já reservado por outras pessoas: {id: quantidade}
var ITEMS = [
 {id:"rn",c:"f",n:"Fralda RN",l:4,u:"pacotes",i:"Até 3,5 kg · 8 a 12 trocas/dia · 150 a 250 fraldas no total (2 a 4 semanas). Dica: poucos pacotes, o bebê pode crescer rápido."},
 {id:"p",c:"f",n:"Fralda P",l:10,u:"pacotes",i:"3,5 a 6 kg · 8 a 10 trocas/dia · 400 a 600 fraldas (até 2 ou 3 meses)."},
 {id:"m",c:"f",n:"Fralda M",l:18,u:"pacotes",i:"5 a 10 kg · 6 a 9 trocas/dia · 800 a 1.100 fraldas (cerca de 2 a 7 meses)."},
 {id:"g",c:"f",n:"Fralda G",l:20,u:"pacotes",i:"9 a 13 kg · 5 a 7 trocas/dia · 1.500 a 2.000 fraldas (7 a 18 meses). Tamanho mais usado!"},
 {id:"xg",c:"f",n:"Fralda XG / XGG / XXG",l:12,u:"pacotes",i:"12 a 15 kg até o desfralde · 4 a 6 trocas/dia · 1.000 a 1.500 fraldas."},
 {id:"len",c:"i",n:"Lenços umedecidos",l:20,u:"pacotes"},
 {id:"pom",c:"i",n:"Pomada para assadura",l:10,u:"tubos"},
 {id:"alg",c:"i",n:"Algodão em discos ou bolas",l:8,u:"pacotes"},
 {id:"has",c:"i",n:"Hastes flexíveis",l:6,u:"pacotes"},
 {id:"sab",c:"i",n:"Sabonete líquido infantil",l:8,u:"frascos"},
 {id:"sha",c:"i",n:"Shampoo e condicionador infantil",l:8,u:"frascos"},
 {id:"alc",c:"i",n:"Álcool 70% e gaze (cuidado do umbigo)",l:5,u:"kits"},
 {id:"bod",c:"i",n:"Bodies e mijões (RN e P)",l:12,u:"peças"},
 {id:"mei",c:"i",n:"Meias, luvas e toucas",l:12,u:"kits"},
 {id:"toa",c:"i",n:"Toalha de banho com capuz",l:6,u:"unidades"},
 {id:"fra",c:"i",n:"Fraldas de pano (boca)",l:10,u:"pacotes"},
 {id:"man",c:"i",n:"Mantas e cobertores",l:6,u:"unidades"},
 {id:"bab",c:"i",n:"Babadores",l:10,u:"unidades"},
 {id:"mam",c:"i",n:"Mamadeira e chupeta",l:5,u:"kits"},
 {id:"ter",c:"i",n:"Termômetro digital",l:3,u:"unidades"}
];
// ========================
var $=function(i){return document.getElementById(i)};
var esc=function(s){return String(s).replace(/[&<>"]/g,function(c){return {"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;"}[c]})};
var user="",tab="f",sel={},outros="",done=null;
function rem(it){return Math.max(0,it.l-(TAKEN[it.id]||0))}
function getD(){try{return JSON.parse(localStorage.getItem("don")||"[]")}catch(e){return[]}}
function setD(d){try{localStorage.setItem("don",JSON.stringify(d))}catch(e){}}
try{user=sessionStorage.getItem("nome")||""}catch(e){}
function start(){ if(!user){$("gate").hidden=false;$("app").hidden=true;return}
 $("gate").hidden=true;$("app").hidden=false;$("ola").textContent="Olá, "+user+"! Escolha o que você quer doar (pode marcar vários itens).";draw()}
$("entrar").onclick=function(){var v=$("nome").value.trim();if(v.length<2){$("nome").focus();return}
 user=v;try{sessionStorage.setItem("nome",v)}catch(e){}start()};
$("nome").onkeydown=function(e){if(e.key==="Enter")$("entrar").click()};
function total(){var n=0;for(var k in sel)n+=sel[k];return n+(outros.trim()?1:0)}
function draw(){
 var T=[["f","Fraldas"],["i","Higiene e enxoval"],["o","Outros"]];
 $("tabs").innerHTML=T.map(function(t){return '<button data-t="'+t[0]+'" class="'+(tab===t[0]?"on":"")+'">'+t[1]+'</button>'}).join("");
 [].forEach.call($("tabs").children,function(b){b.onclick=function(){tab=b.dataset.t;draw()}});
 var v=$("view");
 if(done){v.innerHTML='<div class="card"><h2 style="margin-top:0">Obrigado, '+esc(user)+'! 💛</h2><p>Envie sua escolha ao organizador para ela ser registrada:</p><div class="msg">'+esc(done)+'</div>'+
  ''+(FORM_URL?'<button class="btn" id="fm">Registrar no formulário</button>':'')+'<button class="btn'+(FORM_URL?' sec':'')+'" id="wa">Enviar pelo WhatsApp</button><button class="btn sec" id="cp">Copiar mensagem</button><button class="btn sec" id="nv">Fazer outra escolha</button></div>';
  if(FORM_URL)$("fm").onclick=function(){window.open(FORM_URL,"_blank")};
  $("wa").onclick=function(){window.open("https://wa.me/"+WHATS+"?text="+encodeURIComponent(done),"_blank")};
  $("cp").onclick=function(){try{navigator.clipboard.writeText(done);$("cp").textContent="Copiado ✓"}catch(e){$("cp").textContent="Selecione e copie o texto acima"}};
  $("nv").onclick=function(){done=null;sel={};outros="";draw()};
  $("bar").hidden=true;return}
 if(tab==="o"){v.innerHTML='<div class="card"><div class="nm">Quer doar outra coisa?</div><div class="lim">Ex.: carrinho, banheira, berço, cadeirinha, kit de enfermagem, roupinhas, brinquedos, ou um valor para ajudar. Sem limite.</div><textarea id="ot" rows="4" placeholder="Descreva o que gostaria de doar">'+esc(outros)+'</textarea></div>';
  $("ot").oninput=function(){outros=this.value;bar()}}
 else v.innerHTML=ITEMS.filter(function(x){return x.c===tab}).map(function(x){var r=rem(x),q=sel[x.id]||0;
  return '<div class="card'+(r===0?" full":"")+'"><div class="row"><div><div class="nm">'+x.n+'</div><div class="lim">'+(r===0?"Esgotado":"Restam "+r+" de "+x.l+" "+x.u)+'</div></div>'+
  (r===0?"":'<div class="step"><button data-i="'+x.id+'" data-d="-1">−</button><b>'+q+'</b><button data-i="'+x.id+'" data-d="1">+</button></div>')+'</div>'+(x.i?'<div class="info">'+x.i+'</div>':"")+'</div>'}).join("");
 [].forEach.call(v.querySelectorAll(".step button"),function(b){b.onclick=function(){var it=ITEMS.filter(function(x){return x.id===b.dataset.i})[0];
  var q=(sel[it.id]||0)+Number(b.dataset.d);q=Math.max(0,Math.min(rem(it),q));if(q)sel[it.id]=q;else delete sel[it.id];draw()}});
 bar()}
function bar(){var n=total();$("bar").hidden=!n;$("enviar").textContent="Confirmar doação ("+n+(n===1?" item":" itens")+")"}
$("enviar").onclick=function(){var L=[];ITEMS.forEach(function(x){if(sel[x.id])L.push("• "+sel[x.id]+" "+x.u+" de "+x.n)});
 if(outros.trim())L.push("• Outro: "+outros.trim());
 done="Olá! Sou "+user+" e vou doar no Chá de Fraldas:\n"+L.join("\n");draw();window.scrollTo(0,0)};

// ===== ÁREA DO ORGANIZADOR =====
$("adm").onclick=function(){var l=prompt("Login do organizador:");if(l===null)return;var p=prompt("Senha:");if(p===null)return;if(l.trim()===LOGIN&&p===PIN)admin();else alert("Login ou senha incorretos.")};
function admin(){
 $("bar").hidden=true;$("tabs").hidden=true;$("ola").textContent="Área do organizador";
 var d=getD();
 var tot={};d.forEach(function(r){tot[r.id]=(tot[r.id]||0)+r.q});
 var opts=ITEMS.map(function(x){return '<option value="'+x.id+'">'+x.n+'</option>'}).join("")+'<option value="out">Outro</option>';
 var h='<div class="card"><div class="nm">Registrar doação recebida</div><input id="an" placeholder="Nome de quem vai doar"><select id="ai">'+opts+'</select><input id="aq" type="number" min="1" value="1"><input id="ao" placeholder="Se “Outro”, descreva"><button class="btn" id="ad">Adicionar</button></div>';
 h+='<div class="card"><div class="nm">Quem vai doar o quê</div><div class="ov"><table><tr><th>Nome</th><th>Item</th><th>Qtd</th><th></th></tr>'+
  (d.length?d.map(function(r,i){return '<tr><td>'+esc(r.n)+'</td><td>'+esc(r.t)+'</td><td>'+r.q+'</td><td><a data-x="'+i+'" style="cursor:pointer">✕</a></td></tr>'}).join(""):'<tr><td colspan="4">Nada registrado ainda.</td></tr>')+'</table></div></div>';
 h+='<div class="card"><div class="nm">Total por item</div><div class="ov"><table><tr><th>Item</th><th>Doado</th><th>Limite</th></tr>'+
  ITEMS.filter(function(x){return tot[x.id]}).map(function(x){return '<tr><td>'+x.n+'</td><td>'+tot[x.id]+'</td><td>'+x.l+'</td></tr>'}).join("")+'</table></div></div>';
 h+='<button class="btn sec" id="rs">Copiar resumo</button><button class="btn sec" id="vt">Voltar ao site</button>';
 $("view").innerHTML=h;
 $("ad").onclick=function(){var n=$("an").value.trim(),id=$("ai").value,q=parseInt($("aq").value,10);if(!n||!(q>0))return;
  var it=ITEMS.filter(function(x){return x.id===id})[0];
  d.push({n:n,id:id,q:q,t:it?it.n+" ("+it.u+")":"Outro: "+($("ao").value.trim()||"—")});setD(d);admin()};
 [].forEach.call($("view").querySelectorAll("[data-x]"),function(a){a.onclick=function(){d.splice(Number(a.dataset.x),1);setD(d);admin()}});
 $("rs").onclick=function(){var t=d.map(function(r){return r.n+": "+r.q+" × "+r.t}).join("\n");try{navigator.clipboard.writeText(t);$("rs").textContent="Copiado ✓"}catch(e){alert(t)}};
 $("vt").onclick=function(){$("tabs").hidden=false;start()};
}
start();
</script>
</body>
</html>
