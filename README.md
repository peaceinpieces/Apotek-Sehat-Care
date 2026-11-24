<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Apotek SehatCare</title>
<script src="https://cdn.tailwindcss.com"></script>
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
<style>
body{font-family:'Plus Jakarta Sans',system-ui,sans-serif;background:#F8FAFC;color:#0F172A}
.btn{display:inline-flex;align-items:center;justify-content:center;gap:.4rem;font-weight:700;font-size:.8rem;padding:.55rem .9rem;border-radius:.75rem;transition:.15s;cursor:pointer}
.btn:focus-visible,input:focus-visible,select:focus-visible,textarea:focus-visible{outline:2px solid #0EA5E9;outline-offset:2px}
.btn-red{background:#DC143C;color:#fff}.btn-red:hover{background:#B91032}
.btn-blue{background:#0EA5E9;color:#fff}.btn-blue:hover{background:#0284C7}
.btn-ghost{background:#fff;color:#334155;border:1px solid #CBD5E1}.btn-ghost:hover{background:#F1F5F9}
.btn[disabled]{opacity:.45;cursor:not-allowed}
.inp{width:100%;border:1px solid #CBD5E1;border-radius:.75rem;padding:.55rem .8rem;font-size:.875rem;background:#fff}
.card{background:#fff;border:1px solid #E2E8F0;border-radius:1rem}
.rule{background:#F0F9FF;border:1px solid #BAE6FD;border-radius:.75rem}
@keyframes tin{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:none}}
.toast{animation:tin .2s ease}
@media print{body *{visibility:hidden}#modalBox,#modalBox *{visibility:visible}#modalBox{position:absolute;left:0;top:0;width:100%;max-height:none!important;box-shadow:none!important}.noprint{display:none!important}}
</style>
</head>
<body>
<div id="app"></div><div id="drawer"></div><div id="modal"></div>
<div id="toasts" class="fixed bottom-4 right-4 z-[100] flex flex-col gap-2 noprint"></div>
<script>
'use strict';
const KEY='sehatcare_v1';
const CAT={Batuk:'#0EA5E9',Pilek:'#14B8A6',Meriang:'#F59E0B',Diare:'#8B5CF6',Vitamin:'#F97316',Keras:'#DC143C'};
const SEED=[
['m1','OBH Combi','Batuk','terbatas','sirup','Sirup 100ml',25,18500,'3x1 sendok takar sesudah makan',3],
['m2','Siladex','Batuk','terbatas','sirup','Sirup 60ml',18,21000,'3x1 sendok takar sesudah makan',3],
['m3','Decolgen','Pilek','terbatas','strip','Strip 4 tab',40,4500,'3x1 tablet sesudah makan',3],
['m4','Sanmol 500mg','Meriang','bebas','strip','Strip 10 tab',50,5000,'3x1 tablet bila demam',3],
['m5','Panadol Extra','Meriang','bebas','strip','Strip 10 kaplet',32,14500,'3x1 kaplet sesudah makan',3],
['m6','New Diatabs','Diare','bebas','strip','Strip 4 tab',28,4000,'2 tablet tiap BAB',0],
['m7','Entrostop','Diare','bebas','strip','Strip 12 tab',8,9500,'2 tablet tiap BAB',0],
['m8','Imboost Force','Vitamin','bebas','vit','Strip 10 kaplet',20,78000,'1x1 kaplet sesudah makan',1],
['m9','Amoxicillin 500mg','Keras','keras','kapsul','Strip 10 kaplet',15,12000,'3x1 kaplet tiap 8 jam - wajib dihabiskan',3]
].map(a=>({id:a[0],name:a[1],cat:a[2],gol:a[3],type:a[4],pack:a[5],stock:a[6],price:a[7],rule:a[8],freq:a[9]}));
function load(){try{const d=JSON.parse(localStorage.getItem(KEY));if(d&&Array.isArray(d.meds))return d}catch(e){}
return{meds:SEED,cart:{},orders:[],chat:[],seq:1}}
let D=load();
const U={mode:'pasien',tab:'katalog',atab:'stok',filter:'Semua',q:'',cartOpen:false,method:'ambil',addr:'',rx:null,pos:{},buyer:'',sched:null,modal:null};
function save(){try{localStorage.setItem(KEY,JSON.stringify(D))}catch(e){toast('Penyimpanan penuh, data tidak tersimpan','err')}}
const $=s=>document.querySelector(s);
const rp=n=>'Rp'+Number(n||0).toLocaleString('id-ID');
const esc=s=>String(s==null?'':s).replace(/[&<>"']/g,c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c]));
const med=id=>D.meds.find(m=>m.id===id);
const now=()=>new Date().toLocaleString('id-ID',{dateStyle:'medium',timeStyle:'short'});
const GOL={bebas:['Obat Bebas','#16A34A'],terbatas:['Bebas Terbatas','#1D4ED8'],keras:['Obat Keras','#DC143C']};
function toast(msg,type){const t=document.createElement('div');t.className='toast px-4 py-3 rounded-xl text-sm font-semibold text-white shadow-lg '+(type==='err'?'bg-[#DC143C]':'bg-[#0EA5E9]');t.textContent=msg;$('#toasts').appendChild(t);setTimeout(()=>t.remove(),2800)}

/* ---------- Ilustrasi ---------- */
function golIcon(g,s){s=s||20;const c=GOL[g][1];
return `<svg width="${s}" height="${s}" viewBox="0 0 20 20" aria-hidden="true"><circle cx="10" cy="10" r="8.5" fill="${c}" stroke="#0F172A" stroke-width="1.2"/>${g==='keras'?'<text x="10" y="14.2" text-anchor="middle" font-size="11" font-weight="800" fill="#fff" font-family="sans-serif">K</text>':''}</svg>`}
function golBadge(g){return `<span class="inline-flex items-center gap-1 bg-white/95 border border-slate-200 rounded-full pl-1 pr-2 py-0.5 text-[11px] font-bold ${g==='keras'?'text-[#DC143C]':'text-slate-700'}">${golIcon(g,16)}${GOL[g][0]}</span>`}
function pack(m){const c=CAT[m.cat]||'#0EA5E9',t=m.type;
if(t==='sirup')return `<svg viewBox="0 0 120 120" class="h-28"><rect x="40" y="6" width="40" height="16" rx="3" fill="#DC143C"/><rect x="46" y="22" width="28" height="12" fill="#BAE6FD"/><rect x="28" y="34" width="64" height="78" rx="12" fill="#F59E0B" opacity=".85"/><rect x="28" y="52" width="64" height="40" fill="#fff"/><rect x="28" y="52" width="64" height="9" fill="${c}"/><text x="60" y="80" text-anchor="middle" font-size="12" font-weight="800" fill="#0F172A" font-family="sans-serif">SIRUP</text><path d="M36 42v64" stroke="#fff" opacity=".45" stroke-width="3"/></svg>`;
if(t==='vit')return `<svg viewBox="0 0 120 120" class="h-28"><rect x="36" y="8" width="48" height="20" rx="4" fill="#F97316"/><rect x="30" y="28" width="60" height="84" rx="10" fill="#FFEDD5" stroke="#FDBA74" stroke-width="2"/><rect x="30" y="50" width="60" height="42" fill="#fff"/><circle cx="60" cy="71" r="13" fill="#F97316"/><text x="60" y="76" text-anchor="middle" font-size="14" font-weight="800" fill="#fff" font-family="sans-serif">C</text></svg>`;
if(t==='kapsul')return `<svg viewBox="0 0 120 120" class="h-28"><rect x="14" y="30" width="92" height="64" rx="8" fill="#fff" stroke="#CBD5E1" stroke-width="2"/><rect x="14" y="30" width="92" height="14" rx="8" fill="#DC143C"/><g transform="rotate(-25 45 72)"><rect x="25" y="62" width="40" height="18" rx="9" fill="#DC143C"/><rect x="45" y="62" width="20" height="18" rx="9" fill="#FDE68A"/></g><g transform="rotate(20 80 72)"><rect x="60" y="62" width="40" height="18" rx="9" fill="#DC143C"/><rect x="80" y="62" width="20" height="18" rx="9" fill="#FDE68A"/></g></svg>`;
let p='';for(let r=0;r<2;r++)for(let i=0;i<5;i++)p+=`<circle cx="${25+i*17}" cy="${58+r*26}" r="6.5" fill="#fff" stroke="#94A3B8"/><circle cx="${25+i*17}" cy="${58+r*26}" r="3.5" fill="${c}" opacity=".8"/>`;
return `<svg viewBox="0 0 120 120" class="h-28"><rect x="8" y="26" width="104" height="76" rx="8" fill="#E2E8F0" stroke="#94A3B8"/><rect x="8" y="26" width="104" height="18" rx="8" fill="${c}"/>${p}</svg>`}
function rxSample(){const s=`<svg xmlns="http://www.w3.org/2000/svg" width="320" height="220"><rect width="320" height="220" fill="#fff" stroke="#0EA5E9" stroke-width="3"/><text x="16" y="30" font-size="15" font-weight="700" font-family="sans-serif" fill="#0EA5E9">dr. Andi Pratama - SIP 123/2026</text><line x1="16" x2="304" y1="42" y2="42" stroke="#CBD5E1"/><text x="16" y="80" font-size="26" font-weight="700" fill="#DC143C" font-family="serif">R/</text><text x="50" y="80" font-size="14" font-family="sans-serif">Amoxicillin 500mg  No. X</text><text x="50" y="102" font-size="14" font-family="sans-serif">S 3 dd 1 tab p.c.</text><text x="190" y="190" font-size="12" font-family="sans-serif" fill="#64748B">Contoh resep (uji coba)</text></svg>`;return 'data:image/svg+xml;charset=utf-8,'+encodeURIComponent(s)}

/* ---------- Render utama ---------- */
function render(){
$('#app').innerHTML=header()+`<main class="max-w-6xl mx-auto px-4 py-6">${U.mode==='pasien'?pasien():admin()}</main>`;
$('#drawer').innerHTML=U.mode==='pasien'&&U.cartOpen?drawer():'';
$('#modal').innerHTML=modal();
const c=$('#chatbox');if(c)c.scrollTop=c.scrollHeight}
function cartItems(){return Object.entries(D.cart).map(([id,q])=>({m:med(id),q})).filter(x=>x.m&&x.q>0)}
function header(){const n=cartItems().reduce((a,x)=>a+x.q,0);
const sw=(m,l)=>`<button class="btn ${U.mode===m?'bg-white text-[#0EA5E9]':'text-white hover:bg-white/15'}" onclick="setMode('${m}')">${l}</button>`;
return `<header class="bg-[#0EA5E9] text-white sticky top-0 z-30 noprint"><div class="max-w-6xl mx-auto px-4 py-3 flex flex-wrap items-center justify-between gap-3">
<div class="flex items-center gap-2"><svg width="34" height="34" viewBox="0 0 34 34"><rect width="34" height="34" rx="10" fill="#fff"/><path d="M14 7h6v7h7v6h-7v7h-6v-7H7v-6h7z" fill="#DC143C"/></svg><div><div class="font-extrabold leading-tight">Apotek SehatCare</div><div class="text-[11px] opacity-90">Obat tepat, aturan minum jelas</div></div></div>
<div class="flex items-center gap-2"><div class="flex bg-white/20 rounded-xl p-1">${sw('pasien','Mode Pasien')}${sw('admin','Mode Admin &amp; Kasir')}</div>
${U.mode==='pasien'?`<button class="btn btn-red" onclick="U.cartOpen=true;render()">${cartSvg()} Keranjang${n?` <span class="bg-white text-[#DC143C] rounded-full px-1.5 text-[11px]">${n}</span>`:''}</button>`:''}</div></div></header>`}
function cartSvg(){return '<svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"><circle cx="9" cy="20" r="1.5"/><circle cx="18" cy="20" r="1.5"/><path d="M2 3h3l2.7 12.4a2 2 0 0 0 2 1.6h7.7a2 2 0 0 0 2-1.5L21 8H6"/></svg>'}
function setMode(m){U.mode=m;U.cartOpen=false;render()}
function tabs(list,cur,fn){return `<div class="flex flex-wrap gap-2 my-5 noprint">${list.map(([k,l])=>`<button class="btn ${cur===k?'btn-blue':'btn-ghost'}" onclick="${fn}('${k}')">${l}</button>`).join('')}</div>`}
const setTab=k=>{U.tab=k;render()},setATab=k=>{U.atab=k;render()};

/* ---------- Pasien ---------- */
function pasien(){
const chips=['Semua','Batuk','Pilek','Meriang','Diare','Vitamin','Keras'].map(f=>`<button class="btn ${U.filter===f?'bg-white text-[#DC143C]':'bg-white/20 text-white hover:bg-white/30'}" onclick="U.filter='${f}';render()">${f}</button>`).join('');
const banner=U.tab==='katalog'?`<section class="rounded-3xl p-6 md:p-9 text-white bg-gradient-to-r from-[#0EA5E9] to-[#DC143C]"><h1 class="text-2xl md:text-3xl font-extrabold">Cari obat sesuai keluhan Anda</h1><p class="text-sm opacity-95 mt-1 mb-5">Lengkap dengan aturan pakai dan konsultasi langsung dengan apoteker.</p><input id="q" class="inp max-w-xl text-slate-800" placeholder="Cari nama obat atau keluhan..." value="${esc(U.q)}" oninput="U.q=this.value;$('#grid').innerHTML=grid()"><div class="flex flex-wrap gap-2 mt-4">${chips}</div></section>`:'';
let body='';
if(U.tab==='katalog')body=`<div id="grid" class="grid sm:grid-cols-2 lg:grid-cols-3 gap-5">${grid()}</div>`;
else if(U.tab==='konsul')body=konsul();else body=riwayat();
return banner+tabs([['katalog','Katalog Obat'],['konsul','Konsultasi Apoteker'],['riwayat','Riwayat &amp; E-Faktur']],U.tab,'setTab')+body}
function grid(){const q=U.q.trim().toLowerCase();
const list=D.meds.filter(m=>(U.filter==='Semua'||(U.filter==='Keras'?m.gol==='keras':m.cat===U.filter))&&(!q||(m.name+' '+m.cat).toLowerCase().includes(q)));
if(!list.length)return '<div class="col-span-full card p-8 text-center text-slate-500">Obat tidak ditemukan. Coba kata kunci atau filter lain.</div>';
return list.map(m=>`<article class="card p-4 flex flex-col"><div class="relative bg-slate-50 rounded-xl h-36 flex items-center justify-center"><div class="absolute top-2 left-2">${golBadge(m.gol)}</div>${m.stock<=10?'<span class="absolute top-2 right-2 bg-[#DC143C] text-white text-[11px] font-bold rounded-full px-2 py-0.5">Stok menipis</span>':''}${pack(m)}</div>
<div class="mt-3 flex items-start justify-between gap-2"><div><h3 class="font-extrabold">${esc(m.name)}</h3><p class="text-xs text-slate-500">${esc(m.cat)} &bull; ${esc(m.pack)}</p></div><div class="text-right"><div class="font-extrabold text-[#DC143C]">${rp(m.price)}</div><div class="text-[11px] text-slate-500">Stok ${m.stock}</div></div></div>
<div class="rule p-3 mt-3 text-xs"><div class="font-bold text-[#0284C7] mb-0.5">Aturan pakai</div>${esc(m.rule)}</div>
<div class="grid grid-cols-2 gap-2 mt-3"><button class="btn btn-blue" onclick="askMed('${m.id}')">Tanya Cara Minum</button><button class="btn btn-red" ${m.stock<1?'disabled':''} onclick="addCart('${m.id}')">${cartSvg()} Keranjang</button></div></article>`).join('')}
function addCart(id){const m=med(id);if(!m)return;const c=D.cart[id]||0;if(c>=m.stock){toast('Stok '+m.name+' tidak mencukupi','err');return}D.cart[id]=c+1;save();render();toast(m.name+' masuk keranjang')}
function qty(id,d){const m=med(id);const n=(D.cart[id]||0)+d;if(n<=0)delete D.cart[id];else if(n>m.stock){toast('Stok hanya '+m.stock,'err');return}else D.cart[id]=n;save();render()}
function drawer(){const it=cartItems();const sub=it.reduce((a,x)=>a+x.q*x.m.price,0);const keras=it.some(x=>x.m.gol==='keras');const fee=U.method==='antar'&&it.length?5000:0;
return `<div class="fixed inset-0 z-40 flex justify-end noprint"><div class="absolute inset-0 bg-slate-900/40" onclick="U.cartOpen=false;render()"></div><aside class="relative bg-white w-full max-w-md h-full overflow-y-auto p-5 shadow-2xl">
<div class="flex justify-between items-center mb-4"><h2 class="text-lg font-extrabold">Keranjang Anda</h2><button class="btn btn-ghost" onclick="U.cartOpen=false;render()">Tutup</button></div>
${it.length?it.map(x=>`<div class="flex items-center gap-3 py-3 border-b border-slate-100"><div class="flex-1"><div class="font-bold text-sm">${esc(x.m.name)}</div><div class="text-xs text-slate-500">${rp(x.m.price)} &bull; ${esc(x.m.pack)}</div></div><div class="flex items-center gap-2"><button class="btn btn-ghost !px-2.5" onclick="qty('${x.m.id}',-1)" aria-label="Kurangi">-</button><span class="w-5 text-center font-bold text-sm">${x.q}</span><button class="btn btn-ghost !px-2.5" onclick="qty('${x.m.id}',1)" aria-label="Tambah">+</button></div></div>`).join(''):'<div class="text-center text-slate-500 py-10">Keranjang masih kosong. Pilih obat dari katalog.</div>'}
${it.length?`<div class="mt-4 space-y-2"><div class="font-bold text-sm">Metode pengambilan</div>
${[['ambil','Ambil di Apotek'],['antar','Antar ke Alamat (+Rp5.000)']].map(([k,l])=>`<label class="flex items-center gap-2 card p-3 text-sm cursor-pointer"><input type="radio" name="mt" ${U.method===k?'checked':''} onchange="U.method='${k}';render()"> ${l}</label>`).join('')}
${U.method==='antar'?`<textarea class="inp" rows="2" placeholder="Alamat lengkap pengantaran" oninput="U.addr=this.value">${esc(U.addr)}</textarea>`:''}</div>
${keras?`<div class="mt-4 rule p-3"><div class="font-bold text-sm text-[#DC143C]">Ada Obat Keras - resep dokter wajib</div><p class="text-xs text-slate-600 mb-2">Unggah foto resep agar apoteker dapat memverifikasi.</p>
${U.rx?`<img src="${U.rx}" alt="Resep" class="rounded-lg border max-h-40 mb-2">`:''}
<input type="file" accept="image/*" class="text-xs w-full" onchange="onRx(this)"><button class="btn btn-ghost mt-2 w-full" onclick="U.rx=rxSample();render()">Gunakan Contoh Resep</button></div>`:''}
<div class="mt-5 space-y-1 text-sm"><div class="flex justify-between"><span>Subtotal</span><b>${rp(sub)}</b></div><div class="flex justify-between"><span>Ongkir</span><b>${rp(fee)}</b></div><div class="flex justify-between text-base"><span>Total</span><b class="text-[#DC143C]">${rp(sub+fee)}</b></div></div>
<button class="btn btn-red w-full mt-4 !py-3" onclick="checkout()">Buat Pesanan</button>`:''}</aside></div>`}
function onRx(inp){const f=inp.files&&inp.files[0];if(!f)return;if(!f.type.startsWith('image/')){toast('File harus berupa gambar','err');return}
const r=new FileReader();r.onload=()=>{const im=new Image();im.onload=()=>{const k=Math.min(1,600/im.width);const c=document.createElement('canvas');c.width=Math.max(1,im.width*k);c.height=Math.max(1,im.height*k);c.getContext('2d').drawImage(im,0,0,c.width,c.height);U.rx=c.toDataURL('image/jpeg',.7);render()};im.onerror=()=>toast('Gambar tidak dapat dibaca','err');im.src=r.result};r.readAsDataURL(f)}
function checkout(){const it=cartItems();if(!it.length){toast('Keranjang kosong','err');return}
for(const x of it)if(x.q>x.m.stock){toast('Stok '+x.m.name+' tidak mencukupi','err');return}
const keras=it.some(x=>x.m.gol==='keras');
if(keras&&!U.rx){toast('Obat Keras wajib melampirkan resep dokter','err');return}
if(U.method==='antar'&&!U.addr.trim()){toast('Isi alamat pengantaran','err');return}
const sub=it.reduce((a,x)=>a+x.q*x.m.price,0),fee=U.method==='antar'?5000:0;
mkOrder({src:'online',buyer:'Pasien',items:it.map(x=>({id:x.m.id,name:x.m.name,qty:x.q,price:x.m.price,rule:x.m.rule,gol:x.m.gol})),fee,total:sub+fee,method:U.method,addr:U.method==='antar'?U.addr.trim():'',rx:keras?U.rx:null,status:'Menunggu Verifikasi'});
D.cart={};U.rx=null;U.cartOpen=false;U.tab='riwayat';save();render();toast('Pesanan dikirim, menunggu verifikasi apoteker')}
function mkOrder(o){o.id='ORD-'+String(D.seq++).padStart(4,'0');o.date=now();D.orders.unshift(o);return o}
function statusBadge(s){const c=s==='Menunggu Verifikasi'?'bg-amber-100 text-amber-800':s==='Ditolak'?'bg-red-100 text-[#DC143C]':'bg-sky-100 text-[#0284C7]';return `<span class="text-[11px] font-bold rounded-full px-2 py-0.5 ${c}">${esc(s)}</span>`}
function riwayat(){const L=D.orders.filter(o=>o.src==='online');
if(!L.length)return '<div class="card p-8 text-center text-slate-500">Belum ada pesanan. Pilih obat di katalog lalu tekan Buat Pesanan.</div>';
return `<div class="space-y-3">${L.map(o=>`<div class="card p-4 flex flex-wrap items-center justify-between gap-3"><div><div class="font-extrabold">${o.id} ${statusBadge(o.status)}</div><div class="text-xs text-slate-500">${esc(o.date)} &bull; ${o.method==='antar'?'Antar alamat':'Ambil di apotek'}</div><div class="text-sm mt-1">${o.items.map(i=>esc(i.name)+' x'+i.qty).join(', ')}</div></div><div class="text-right"><div class="font-extrabold text-[#DC143C]">${rp(o.total)}</div>${o.inv?`<button class="btn btn-blue mt-2" onclick="U.modal={t:'inv',id:'${o.id}'};render()">Lihat E-Faktur</button>`:`<div class="text-xs text-slate-500 mt-2">${o.status==='Ditolak'?'Pesanan ditolak apoteker':'Faktur terbit setelah verifikasi'}</div>`}</div></div>`).join('')}</div>`}

/* ---------- Chat & jadwal ---------- */
function bubbles(me){return D.chat.length?D.chat.map(c=>{const mine=c.f===me;return `<div class="flex ${mine?'justify-end':'justify-start'}"><div class="max-w-[80%] rounded-2xl px-3 py-2 text-sm ${mine?'bg-[#0EA5E9] text-white':'bg-slate-100'}">${c.auto?'<div class="text-[10px] opacity-70">Balasan otomatis</div>':''}${esc(c.t)}<div class="text-[10px] opacity-60 mt-0.5">${esc(c.ts)}</div></div></div>`}).join(''):'<p class="text-center text-sm text-slate-500 py-10">Belum ada percakapan. Tanyakan cara minum obat Anda.</p>'}
function konsul(){const id=U.sched||(D.meds[0]&&D.meds[0].id);const m=med(id)||D.meds[0];
const sl=[['Pagi','07.00'],['Siang','14.00'],['Malam','21.00']];
const on=!m?[]:m.freq===1?[0]:m.freq===2?[0,2]:m.freq>=3?[0,1,2]:[];
return `<div class="grid lg:grid-cols-2 gap-5"><section class="card p-4 flex flex-col"><h2 class="font-extrabold mb-3">Chat dengan Apoteker</h2><div id="chatbox" class="h-80 overflow-y-auto space-y-2 mb-3 pr-1">${bubbles('p')}</div><div class="flex gap-2"><input id="cin" class="inp" placeholder="Tulis pertanyaan..." onkeydown="if(event.key==='Enter')sendP()"><button class="btn btn-blue" onclick="sendP()">Kirim</button></div></section>
<section class="card p-4"><h2 class="font-extrabold mb-3">Jadwal Minum Obat</h2><select class="inp" onchange="U.sched=this.value;render()">${D.meds.map(x=>`<option value="${x.id}" ${m&&x.id===m.id?'selected':''}>${esc(x.name)}</option>`).join('')}</select>
${m?`<div class="rule p-3 mt-3 text-sm"><b class="text-[#0284C7]">Aturan:</b> ${esc(m.rule)}</div><div class="grid grid-cols-3 gap-2 mt-3">${sl.map(([n,t],i)=>`<div class="rounded-xl p-3 text-center border ${on.includes(i)?'bg-[#F0F9FF] border-[#0EA5E9]':'bg-slate-50 border-slate-200 opacity-60'}"><div class="font-bold text-sm">${n}</div><div class="text-xs text-slate-500">${t}</div><div class="text-xs font-bold mt-1 ${on.includes(i)?'text-[#0284C7]':'text-slate-400'}">${on.includes(i)?'Minum':'-'}</div></div>`).join('')}</div>${m.freq===0?'<p class="text-xs text-slate-500 mt-2">Obat ini diminum sesuai kebutuhan, tidak terjadwal.</p>':''}`:''}</section></div>`}
function pushChat(f,t,auto){D.chat.push({f,t,ts:now(),auto:!!auto})}
function autoReply(text){const low=text.toLowerCase();const m=D.meds.find(x=>low.includes(x.name.toLowerCase().split(' ')[0]));
pushChat('a',m?`Aturan minum ${m.name}: ${m.rule}. Jika keluhan tidak membaik, segera hubungi dokter.`:'Terima kasih, apoteker kami akan segera membalas pertanyaan Anda.',true)}
function sendP(){const i=$('#cin');const t=i.value.trim();if(!t)return;pushChat('p',t);autoReply(t);save();render();const e=$('#cin');if(e)e.focus()}
function askMed(id){const m=med(id);pushChat('p','Bagaimana cara minum '+m.name+'?');autoReply(m.name);U.tab='konsul';U.sched=id;save();render();toast('Pertanyaan terkirim ke apoteker')}

/* ---------- Admin ---------- */
function admin(){const low=D.meds.filter(m=>m.stock<=10).length,nw=D.orders.filter(o=>o.status==='Menunggu Verifikasi').length,inc=D.orders.filter(o=>o.inv).reduce((a,o)=>a+o.total,0);
const k=(l,v,c)=>`<div class="card p-4"><div class="text-xs text-slate-500">${l}</div><div class="text-2xl font-extrabold ${c}">${v}</div></div>`;
return `<div class="grid grid-cols-2 lg:grid-cols-4 gap-4">${k('Total Merek',D.meds.length,'text-[#0EA5E9]')}${k('Stok Menipis',low,'text-[#DC143C]')}${k('Pesanan Baru',nw,'text-[#0EA5E9]')}${k('Pendapatan',rp(inc),'text-[#0F172A]')}</div>`+
tabs([['stok','Manajemen Stok'],['layanan','Pelayanan Online &amp; POS Kasir'],['chat','Konsol Chat Pasien']],U.atab,'setATab')+(U.atab==='stok'?stok():U.atab==='layanan'?layanan():konsol())}
function stok(){return `<div class="flex justify-between items-center mb-3"><h2 class="font-extrabold">Daftar Obat</h2><button class="btn btn-red" onclick="U.modal={t:'add'};render()">+ Tambah Merek Obat</button></div>
<div class="card overflow-x-auto"><table class="w-full text-sm"><thead class="bg-slate-50 text-left text-xs text-slate-500"><tr><th class="p-3">Obat</th><th>Golongan</th><th>Kemasan</th><th>Harga</th><th>Stok</th><th></th></tr></thead><tbody>${D.meds.map(m=>`<tr class="border-t border-slate-100"><td class="p-3 font-bold">${esc(m.name)}<div class="text-xs font-normal text-slate-500">${esc(m.cat)}</div></td><td><span class="inline-flex items-center gap-1">${golIcon(m.gol,16)}${GOL[m.gol][0]}</span></td><td>${esc(m.pack)}</td><td>${rp(m.price)}</td><td><div class="flex items-center gap-2"><input type="number" min="0" value="${m.stock}" class="inp !w-20 !py-1" onchange="setStock('${m.id}',this.value)">${m.stock<=10?'<span class="bg-[#DC143C] text-white text-[11px] font-bold rounded-full px-2 py-0.5">Menipis</span>':''}</div></td><td class="pr-3 text-right"><button class="btn btn-ghost" onclick="delMed('${m.id}')">Hapus</button></td></tr>`).join('')}</tbody></table></div>`}
function setStock(id,v){const n=parseInt(v,10);if(isNaN(n)||n<0){toast('Stok harus angka 0 atau lebih','err');render();return}med(id).stock=n;save();render();toast('Stok diperbarui')}
function delMed(id){const m=med(id);D.meds=D.meds.filter(x=>x.id!==id);delete D.cart[id];delete U.pos[id];save();render();toast(m.name+' dihapus dari katalog')}
function layanan(){const pend=D.orders.filter(o=>o.src==='online'&&o.status==='Menunggu Verifikasi');
const pi=Object.entries(U.pos).map(([id,q])=>({m:med(id),q})).filter(x=>x.m);const tot=pi.reduce((a,x)=>a+x.q*x.m.price,0);
return `<div class="grid lg:grid-cols-2 gap-5"><section><h2 class="font-extrabold mb-3">Pesanan Online Masuk</h2>${pend.length?pend.map(o=>`<div class="card p-4 mb-3"><div class="flex justify-between"><b>${o.id}</b>${statusBadge(o.status)}</div><div class="text-xs text-slate-500">${esc(o.date)} &bull; ${o.method==='antar'?'Antar: '+esc(o.addr):'Ambil di apotek'}</div><ul class="text-sm my-2">${o.items.map(i=>`<li class="flex items-center gap-1">${golIcon(i.gol||'bebas',14)} ${esc(i.name)} x${i.qty}</li>`).join('')}</ul>${o.rx?`<img src="${o.rx}" alt="Foto resep" class="rounded-lg border max-h-32 cursor-zoom-in mb-2" onclick="U.modal={t:'img',id:'${o.id}'};render()">`:''}<div class="flex justify-between items-center"><b class="text-[#DC143C]">${rp(o.total)}</b><div class="flex gap-2"><button class="btn btn-ghost" onclick="rejectOrder('${o.id}')">Tolak</button><button class="btn btn-red" onclick="issue('${o.id}')">Terbitkan Faktur</button></div></div></div>`).join(''):'<div class="card p-6 text-center text-slate-500 text-sm">Tidak ada pesanan yang menunggu verifikasi.</div>'}</section>
<section><h2 class="font-extrabold mb-3">POS Kasir</h2><div class="card p-4"><div class="flex gap-2"><select id="pm" class="inp">${D.meds.map(m=>`<option value="${m.id}">${esc(m.name)} (stok ${m.stock})</option>`).join('')}</select><input id="pq" type="number" min="1" value="1" class="inp !w-20"><button class="btn btn-blue" onclick="posAdd()">Tambah</button></div>
<div class="my-3 divide-y divide-slate-100">${pi.length?pi.map(x=>`<div class="py-2 flex justify-between text-sm"><span>${esc(x.m.name)} x${x.q}</span><span>${rp(x.q*x.m.price)} <button class="text-[#DC143C] font-bold ml-2" onclick="delete U.pos['${x.m.id}'];render()" aria-label="Hapus item">x</button></span></div>`).join(''):'<p class="text-sm text-slate-500 py-3">Belum ada item. Pilih obat lalu tekan Tambah.</p>'}</div>
<input class="inp mb-3" placeholder="Nama pembeli (opsional)" value="${esc(U.buyer)}" oninput="U.buyer=this.value"><div class="flex justify-between items-center"><b class="text-lg">${rp(tot)}</b><button class="btn btn-red" onclick="posIssue()">Terbitkan Faktur</button></div></div></section></div>
<h2 class="font-extrabold my-3">Faktur Terbit</h2>${D.orders.some(o=>o.inv)?`<div class="card overflow-x-auto"><table class="w-full text-sm"><tbody>${D.orders.filter(o=>o.inv).map(o=>`<tr class="border-t first:border-0 border-slate-100"><td class="p-3 font-bold">${o.inv}</td><td>${o.src==='kasir'?'Kasir':'Online'}</td><td>${esc(o.date)}</td><td>${rp(o.total)}</td><td class="pr-3 text-right"><button class="btn btn-ghost" onclick="U.modal={t:'inv',id:'${o.id}'};render()">Lihat</button></td></tr>`).join('')}</tbody></table></div>`:'<div class="card p-6 text-center text-slate-500 text-sm">Belum ada faktur.</div>'}`}
function posAdd(){const id=$('#pm').value,q=parseInt($('#pq').value,10);const m=med(id);if(!m||isNaN(q)||q<1){toast('Pilih obat dan jumlah yang valid','err');return}
const n=(U.pos[id]||0)+q;if(n>m.stock){toast('Stok '+m.name+' hanya '+m.stock,'err');return}U.pos[id]=n;render()}
function posIssue(){const pi=Object.entries(U.pos).map(([id,q])=>({m:med(id),q})).filter(x=>x.m);if(!pi.length){toast('Tambahkan item terlebih dahulu','err');return}
for(const x of pi)if(x.q>x.m.stock){toast('Stok '+x.m.name+' tidak cukup','err');return}
const o=mkOrder({src:'kasir',buyer:U.buyer.trim()||'Pembeli umum',items:pi.map(x=>({id:x.m.id,name:x.m.name,qty:x.q,price:x.m.price,rule:x.m.rule,gol:x.m.gol})),fee:0,total:pi.reduce((a,x)=>a+x.q*x.m.price,0),method:'ambil',addr:'',rx:null,status:'Draft'});
U.pos={};U.buyer='';issue(o.id)}
function issue(id){const o=D.orders.find(x=>x.id===id);if(!o)return;
for(const it of o.items){const m=med(it.id);if(!m||m.stock<it.qty){toast('Stok '+it.name+' tidak cukup','err');return}}
o.items.forEach(it=>{med(it.id).stock-=it.qty});o.inv='INV-'+o.id.slice(4);o.status=o.src==='kasir'?'Selesai':'Faktur Terbit';save();U.modal={t:'inv',id:o.id};render();toast('Faktur '+o.inv+' terbit, stok terpotong')}
function rejectOrder(id){const o=D.orders.find(x=>x.id===id);if(!o)return;o.status='Ditolak';save();render();toast('Pesanan '+id+' ditolak')}
function konsol(){const chips=[...D.meds.map(m=>[`Aturan ${m.name}`,`Aturan minum ${m.name}: ${m.rule}.`]),['Sesudah makan','Minum obat sesudah makan dengan air putih.'],['Ke dokter','Jika gejala berlanjut lebih dari 3 hari, segera periksa ke dokter.']];
return `<section class="card p-4 max-w-2xl"><h2 class="font-extrabold mb-3">Konsol Chat Pasien</h2><div id="chatbox" class="h-80 overflow-y-auto space-y-2 mb-3 pr-1">${bubbles('a')}</div>
<div class="flex flex-wrap gap-1.5 mb-3">${chips.map((c,i)=>`<button class="btn btn-ghost !py-1 !text-xs" onclick="quick(${i})">${esc(c[0])}</button>`).join('')}</div>
<div class="flex gap-2"><input id="ain" class="inp" placeholder="Balas pasien..." onkeydown="if(event.key==='Enter')sendA()"><button class="btn btn-blue" onclick="sendA()">Kirim</button></div></section>`}
function sendA(){const t=$('#ain').value.trim();if(!t)return;pushChat('a',t);save();render();const e=$('#ain');if(e)e.focus()}
function quick(i){const m=D.meds[i];const g=i<D.meds.length?`Aturan minum ${m.name}: ${m.rule}.`:i===D.meds.length?'Minum obat sesudah makan dengan air putih.':'Jika gejala berlanjut lebih dari 3 hari, segera periksa ke dokter.';pushChat('a',g);save();render()}

/* ---------- Modal ---------- */
function closeM(){U.modal=null;render()}
function modal(){const M=U.modal;if(!M)return '';let inner='';
if(M.t==='img'){const o=D.orders.find(x=>x.id===M.id);inner=o&&o.rx?`<h3 class="font-extrabold mb-3">Foto Resep ${o.id}</h3><img src="${o.rx}" alt="Resep" class="rounded-lg border w-full">`:'<p>Resep tidak ditemukan.</p>'}
else if(M.t==='add')inner=addForm();
else{const o=D.orders.find(x=>x.id===M.id);if(!o)return '';
inner=`<div class="text-center border-b border-dashed border-slate-300 pb-3 mb-3"><div class="font-extrabold text-lg text-[#0EA5E9]">Apotek SehatCare</div><div class="text-xs text-slate-500">Faktur ${o.inv} &bull; ${esc(o.date)}</div><div class="text-xs text-slate-500">Pembeli: ${esc(o.buyer)}${o.method==='antar'?' &bull; Antar: '+esc(o.addr):''}</div></div>
${o.items.map(i=>`<div class="mb-3"><div class="flex justify-between text-sm font-bold"><span>${esc(i.name)} x${i.qty}</span><span>${rp(i.qty*i.price)}</span></div><div class="rule p-2 text-xs mt-1"><b class="text-[#0284C7]">Aturan minum:</b> ${esc(i.rule)}</div></div>`).join('')}
<div class="border-t border-dashed border-slate-300 pt-2 text-sm space-y-1">${o.fee?`<div class="flex justify-between"><span>Ongkir</span><span>${rp(o.fee)}</span></div>`:''}<div class="flex justify-between text-base font-extrabold"><span>Total</span><span class="text-[#DC143C]">${rp(o.total)}</span></div></div>
${o.rx?'<p class="text-xs text-slate-500 mt-2">Resep dokter telah diverifikasi apoteker.</p>':''}<p class="text-center text-xs text-slate-500 mt-4">Semoga lekas sembuh. Habiskan obat sesuai anjuran.</p>
<div class="flex gap-2 mt-4 noprint"><button class="btn btn-blue flex-1" onclick="window.print()">Cetak Faktur</button><button class="btn btn-ghost flex-1" onclick="closeM()">Tutup</button></div>`}
return `<div class="fixed inset-0 z-50 flex items-center justify-center p-4"><div class="absolute inset-0 bg-slate-900/50 noprint" onclick="closeM()"></div><div id="modalBox" class="relative bg-white rounded-2xl p-5 w-full max-w-md max-h-[90vh] overflow-y-auto shadow-2xl">${inner}</div></div>`}
function addForm(){const o=a=>a.map(x=>`<option value="${x[0]}">${x[1]}</option>`).join('');
const f=(l,h)=>`<label class="block text-xs font-bold text-slate-600 mb-1">${l}${h}</label>`;
return `<h3 class="font-extrabold mb-3">Tambah Merek Obat</h3><div class="space-y-3">
<div>${f('Nama obat','')}<input id="n_name" class="inp"></div>
<div class="grid grid-cols-2 gap-3"><div>${f('Keluhan','')}<select id="n_cat" class="inp">${Object.keys(CAT).map(c=>`<option>${c}</option>`).join('')}</select></div><div>${f('Golongan','')}<select id="n_gol" class="inp">${o([['bebas','Obat Bebas'],['terbatas','Bebas Terbatas'],['keras','Obat Keras']])}</select></div></div>
<div class="grid grid-cols-2 gap-3"><div>${f('Bentuk kemasan','')}<select id="n_type" class="inp">${o([['sirup','Botol Sirup'],['strip','Strip Tablet'],['kapsul','Kapsul'],['vit','Botol Vitamin']])}</select></div><div>${f('Ukuran','')}<input id="n_pack" class="inp" placeholder="Strip 10 tab"></div></div>
<div class="grid grid-cols-2 gap-3"><div>${f('Stok','')}<input id="n_stock" type="number" min="0" class="inp"></div><div>${f('Harga (Rp)','')}<input id="n_price" type="number" min="0" class="inp"></div></div>
<div>${f('Aturan pakai','')}<input id="n_rule" class="inp" placeholder="3x1 tablet sesudah makan"></div>
<div>${f('Frekuensi per hari (0 = bila perlu)','')}<input id="n_freq" type="number" min="0" max="3" value="3" class="inp"></div>
<div class="flex gap-2"><button class="btn btn-ghost flex-1" onclick="closeM()">Batal</button><button class="btn btn-red flex-1" onclick="addMed()">Simpan Obat</button></div></div>`}
function addMed(){const v=i=>$('#n_'+i).value.trim();const name=v('name'),stock=parseInt(v('stock'),10),price=parseInt(v('price'),10),rule=v('rule'),pk=v('pack');
if(!name||!pk||!rule||isNaN(stock)||isNaN(price)||stock<0||price<0){toast('Lengkapi semua kolom dengan benar','err');return}
D.meds.push({id:'m'+Date.now(),name,cat:v('cat'),gol:v('gol'),type:v('type'),pack:pk,stock,price,rule,freq:Math.min(3,Math.max(0,parseInt(v('freq'),10)||0))});
U.modal=null;save();render();toast(name+' ditambahkan dan tampil di katalog pasien')}

window.addEventListener('storage',e=>{if(e.key===KEY){D=load();const a=document.activeElement;if(!a||!/INPUT|TEXTAREA|SELECT/.test(a.tagName))render()}});
document.addEventListener('keydown',e=>{if(e.key==='Escape'){if(U.modal)closeM();else if(U.cartOpen){U.cartOpen=false;render()}}});
render();
</script>
</body>
</html>
