
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<meta name="referrer" content="no-referrer">
<meta http-equiv="Content-Security-Policy"
content="default-src 'none'; script-src 'self' 'unsafe-inline' 'wasm-unsafe-eval' https://cdn.jsdelivr.net blob:; worker-src blob: 'self' https://cdn.jsdelivr.net; connect-src 'self' https://cdn.jsdelivr.net; style-src 'unsafe-inline'; img-src data: blob:; font-src data: blob:; object-src 'none'; base-uri 'none'; form-action 'none'">
<title>施工管理 AI Studio</title>
<style>
:root{
 --nav:#203447;--accent:#176477;
 --bg:#f2f5f8;--line:#dce3e9;
 --text:#192b3a;--muted:#657585
}
*{box-sizing:border-box}
body{
 margin:0;background:var(--bg);color:var(--text);
 font-family:"Yu Gothic UI",Meiryo,sans-serif;font-size:14px
}
header{
 background:var(--nav);color:white;
 padding:20px 28px;display:flex;
 justify-content:space-between;align-items:center
}
header h1{font-size:22px;margin:0 0 6px}
header small{color:#c4d6e4}
.layout{display:grid;grid-template-columns:230px 1fr;min-height:90vh}
aside{background:#293e51;padding:18px 12px;color:white}
aside button{
 display:block;width:100%;text-align:left;
 background:transparent;color:white;
 border:0;margin-bottom:6px
}
aside button:hover,aside button.active{background:#426176}
main{padding:25px;min-width:0}
.panel{
 background:white;border:1px solid var(--line);
 border-radius:10px;padding:20px;margin-bottom:16px
}
h2{font-size:23px;margin:0 0 14px}
h3{font-size:16px;margin:0 0 12px}
p{line-height:1.7}
small,.muted{color:var(--muted);font-size:12px}
button,input,textarea,select{font:inherit}
button{
 padding:10px 16px;border-radius:6px;
 border:1px solid #cad6df;background:white;
 cursor:pointer;color:var(--text);font-weight:600
}
button.primary{background:var(--accent);color:white;border-color:var(--accent)}
button:disabled{opacity:.45;cursor:not-allowed}
input:not([type=checkbox]),textarea,select{
 width:100%;padding:10px;border:1px solid #cbd5df;
 border-radius:5px;background:white
}
textarea{resize:vertical;line-height:1.8}
label{display:block;margin-bottom:10px}
label span{display:block;font-size:12px;margin-bottom:5px;color:var(--muted)}
.grid{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:12px}
.cards{display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-bottom:16px}
.metric{background:white;border:1px solid var(--line);padding:16px;border-radius:8px}
.metric strong{display:block;font-size:26px;margin-top:8px}
.row{display:flex;align-items:center;gap:8px;flex-wrap:wrap}
.page{display:none}.page.active{display:block}
.drop{
 border:2px dashed #a6bbc7;border-radius:10px;
 padding:30px;text-align:center;background:#f8fbfc
}
.drop.over{background:#e0f2f4;border-color:var(--accent)}
.scroll{overflow:auto;max-height:420px}
table{border-collapse:collapse;width:100%;font-size:12px}
td,th{padding:10px;border-bottom:1px solid var(--line);text-align:left;overflow-wrap:anywhere}
th{background:#edf2f5;position:sticky;top:0}
.warning{white-space:pre-wrap;color:#85500b;background:#fff6e4;padding:12px;border-radius:6px}
.docgrid{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:10px}
.doc{
 border:1px solid var(--line);border-radius:7px;
 padding:12px;background:white;display:flex;
 align-items:flex-start;gap:8px;min-height:65px
}
.doc input{accent-color:var(--accent)}
.doc:has(input:checked){background:#edf7f7;border-color:var(--accent)}
.group{margin:20px 0}
#editor{min-height:600px;font-size:14px}
pre{white-space:pre-wrap;overflow-wrap:anywhere;line-height:1.7;font-size:12px}
progress{width:100%;height:12px;accent-color:var(--accent)}
#status{white-space:pre-wrap}
@media(max-width:850px){
 .layout{grid-template-columns:1fr}
 aside{display:flex;overflow:auto}
 aside button{min-width:145px}
 main{padding:12px}
 .docgrid,.grid,.cards{grid-template-columns:repeat(2,minmax(0,1fr))}
 header{padding:14px}
}
@media print{
 header,aside,.no-print{display:none!important}
 .layout{display:block}main{padding:0}
 .panel{border:0;padding:0}
}
</style>
</head>
<body>
<header>
 <div>
  <h1>施工管理 AI Studio</h1>
  <small>設計図書から完成検査まで ｜ Ver.2.0</small>
 </div>
 <small>ブラウザ内解析</small>
</header>

<div class="layout">
<aside>
 <button class="nav active" data-page="register">01　設計図書登録</button>
 <button class="nav" data-page="analysis">02　解析結果</button>
 <button class="nav" data-page="project">03　工事情報確認</button>
 <button class="nav" data-page="create">04　書類選択・作成</button>
 <button class="nav" data-page="review">05　確認・出力</button>
 <button id="reset">新規現場</button>
</aside>

<main>
<section class="page active" id="register">
 <h2>設計図書の登録</h2>
 <div class="cards">
  <div class="metric">登録資料<strong id="fileCount">0</strong></div>
  <div class="metric">本文読取済<strong id="readCount">0</strong></div>
  <div class="metric">取得ページ等<strong id="pageCount">0</strong></div>
  <div class="metric">作成書類<strong id="draftCount">0</strong></div>
 </div>
 <div class="panel">
  <h3>新現場の設計図書</h3>
  <div class="drop" id="drop">
   <h3>PDF・ZIP・Word・Excelを投入</h3>
   <p>複数ファイルをまとめて登録できます。</p>
   <button class="primary" id="pick">ファイルを選択</button>
   <input type="file" id="files" multiple hidden
    accept=".pdf,.zip,.docx,.xlsx,.txt,.csv,.xml">
  </div>
  <p class="muted">
   ファイル登録後、自動で本文解析を行います。
   分割ZIPは同じ現場として順次登録してください。
  </p>
  <label><span>PDF解析ページ上限（1冊）</span>
   <input type="number" id="maxPages" value="300" min="1" max="3000">
  </label>
  <progress id="progress" value="0" max="100"></progress>
  <p id="status" role="status">解析エンジン準備中</p>
 </div>
 <div class="panel">
  <h3>登録資料一覧</h3>
  <div class="scroll">
   <table>
    <thead><tr><th>資料名</th><th>本文数</th><th>解析状況</th></tr></thead>
    <tbody id="fileList"></tbody>
   </table>
  </div>
 </div>
 <div class="warning">
  設計図書の原本はリポジトリへ保存しないでください。
  PDFの画像ページ・CAD図形は自動解析対象外です。
 </div>
</section>

<section class="page" id="analysis">
 <h2>本文解析・根拠資料</h2>
 <div class="panel">
  <label><span>原文検索</span>
   <input id="query" placeholder="例：ICT、施工条件、安全教育、掘削">
  </label>
  <div class="row">
   <button id="search" class="primary">検索</button>
   <button id="saveText">全解析本文を保存</button>
  </div>
  <div id="hits"></div>
 </div>
 <div class="panel">
  <h3>設計数量・工種の候補</h3>
  <p class="muted">数量表の原文行を抽出します。列・単位・数値の対応は原表を確認してください。</p>
  <pre id="quantities"></pre>
 </div>
</section>

<section class="page" id="project">
 <h2>工事情報の確認</h2>
 <div class="panel">
  <div class="grid" id="fields"></div>
  <button id="extract" class="primary">PDF等から情報を再抽出</button>
  <pre id="candidates"></pre>
 </div>
</section>

<section class="page" id="create">
 <h2>書類選択・作成</h2>
 <div class="panel">
  <div class="grid">
   <label><span>作成日</span><input type="date" id="date"></label>
   <label><span>発議区分</span>
    <select id="kind">
     <option>協議</option><option>提出</option>
     <option>報告</option><option>承諾</option>
     <option>確認</option><option>通知</option>
    </select>
   </label>
  </div>
  <label><span>件名・対象工種</span><input id="subject"></label>
  <label><span>現場条件・協議内容・実際の確認事項</span>
   <textarea id="condition" rows="4"></textarea>
  </label>
  <div class="row">
   <button id="selectStart">着手前書類を選択</button>
   <button id="selectAll">全選択</button>
   <button id="clearAll">全解除</button>
  </div>
 </div>
 <div id="catalog"></div>
 <div class="panel">
  <button class="primary" id="generate">選択した書類を自動作成</button>
 </div>
</section>

<section class="page" id="review">
 <h2>書類確認・出力</h2>
 <div class="panel">
  <label><span>作成済み書類</span><select id="draftSelect"></select></label>
  <div class="row">
   <button class="primary" id="word">Word出力</button>
   <button id="excel">Excel管理一覧</button>
   <button id="zip">全書類ZIP出力</button>
   <button id="prompt">AI文章作成用データ出力</button>
  </div>
  <p class="muted">AI用データはテキストとして保存します。外部への自動送信は行いません。</p>
 </div>
 <div class="panel">
  <textarea id="editor" placeholder="作成された下書きが表示されます"></textarea>
 </div>
</section>
</main>
</div>

<script type="module">
const $=id=>document.getElementById(id);
const MiB=1024*1024;
const MAX_FILE=100*MiB;
const MAX_ZIP=300*MiB;
const MAX_TEXT=30*1000*1000;
let pdfjs,JSZip,ready=false,busy=false;
let records=[],docs=[],drafts={},current='',warnings=[];
let textSize=0;

const fields={
 name:["工事名","工事名称"],
 client:["発注者","発注者名"],
 contractor:["受注者","受注者名"],
 place:["工事場所","施工場所","工事箇所"],
 period:["工期","契約工期"],
 number:["工事番号","契約番号"],
 work:["工事内容","工事概要"],
 agent:["現場代理人"],
 engineer:["監理技術者","主任技術者"]
};

for(const [key,names] of Object.entries(fields)){
 const label=document.createElement("label");
 const span=document.createElement("span");
 span.textContent=names[0];
 const input=document.createElement("input");
 input.id="f_"+key;
 label.append(span,input);
 $("fields").append(label);
}

const planSections=[
 ["工事概要","工事概要 工事内容 工事名 工事場所 工期 数量","契約図書に示された施工範囲と数量を確認し、施工対象を確定する。"],
 ["計画工程","工程 工期 施工時期 週休 休日","施工順序、準備工、制約条件、休日を考慮して工程を設定する。"],
 ["現場組織","現場代理人 監理技術者 施工体制","責任者と役割分担を確定し、組織表を整備する。"],
 ["施工方法","掘削 盛土 護岸 工種 施工方法 施工条件 仮設","工種ごとの施工手順、使用機械及び確認時点を整理する。"],
 ["主要機械・資材","機械 資材 材料 規格","必要な機械能力、資材規格、使用承認の要否を確認する。"],
 ["施工管理","出来形 品質 写真 段階確認 試験 規格値","適用基準、管理項目、測定頻度、規格値を原典で照合する。"],
 ["安全管理","安全 教育 交通 出水 避難 熱中症 緊急","現場条件に応じた危険防止措置と緊急連絡体制を確定する。"],
 ["環境対策","濁水 環境 騒音 振動 建設副産物","施工による環境影響を確認し、必要な対策と記録方法を定める。"],
 ["提出・納品","電子納品 提出 協議 様式","必要な添付書類、承認資料及び納品方法を確認する。"]
];

const catalog=[
 ["着手前","施工計画書（総合）","施工計画 工種 数量","plan"],
 ["着手前","準備工施工計画書","準備工 測量 仮設 道路","plan"],
 ["着手前","ICT施工計画書","ICT 3次元 精度 座標 掘削","plan"],
 ["着手前","設計図書照査報告書","照査 設計図書 施工条件","check"],
 ["着手前","事前協議書","着手 協議 施工条件","meeting"],
 ["着手前","施工体制確認表","施工体制 下請 技術者","check"],
 ["着手前","計画工程表・工程管理計画","工程 工期 休日","plan"],
 ["着手前","安全管理計画書","安全 緊急 出水 交通","plan"],
 ["着手前","品質管理計画書","品質 規格値 試験 材料","plan"],
 ["着手前","出来形管理計画書","出来形 測定 規格値","plan"],
 ["着手前","環境対策計画書","環境 濁水 騒音 振動","plan"],
 ["着手前","緊急時対応計画書","緊急 避難 出水 連絡","plan"],
 ["着手前","建設副産物管理計画書","副産物 廃棄物 再生資源","plan"],
 ["着手前","電子納品事前協議書","電子納品 ASP 電子小黒板","meeting"],
 ["施工中","工事打合せ簿","協議 提出 報告 確認","meeting"],
 ["施工中","段階確認・立会願い","段階確認 立会 検査","meeting"],
 ["施工中","材料承認・使用確認書","材料 規格 承認","check"],
 ["施工中","月次履行報告書","履行 工程 進捗","check"],
 ["施工中","週休二日実施報告書","週休 休日 閉所","check"],
 ["施工中","安全教育記録","安全教育 訓練","check"],
 ["施工中","安全パトロール記録","パトロール 点検 是正","check"],
 ["施工中","ICT精度確認報告書","ICT 精度 校正 3次元","check"],
 ["変更・協議","設計変更協議書","変更 数量 工期 費用","meeting"],
 ["変更・協議","変更施工計画書","変更 施工計画 指示","plan"],
 ["変更・協議","現場条件相違・支障物協議書","支障 埋設 現場条件","meeting"],
 ["完成検査","出来形数量確認書","数量 出来形 算出","check"],
 ["完成検査","品質・出来形管理総括書","品質 出来形 試験","check"],
 ["完成検査","安全・工程管理総括書","安全 工程 実績","check"],
 ["完成検査","創意工夫実施報告書","創意工夫 新技術 社会性","check"],
 ["完成検査","完成検査チェックリスト","完成検査 完成図書","check"],
 ["完成検査","完成通知・電子納品確認書","完成通知 電子納品","check"]
];

function go(id){
 document.querySelectorAll(".page").forEach(p=>p.classList.toggle("active",p.id===id));
 document.querySelectorAll(".nav").forEach(p=>p.classList.toggle("active",p.dataset.page===id));
 window.scrollTo(0,0);
}
document.querySelectorAll(".nav").forEach(b=>b.onclick=()=>go(b.dataset.page));

function showStatus(s){$("status").textContent=s}
function norm(s){return String(s??"").normalize("NFKC").trim()}
function compact(s){return norm(s).replace(/\s/g,"").toLowerCase()}
function esc(s){
 return String(s??"").replace(/[&<>"']/g,c=>({
 "&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"
 }[c]))
}
function ext(s){return s.split(".").pop().toLowerCase()}
function refresh(){
 $("fileCount").textContent=docs.length;
 $("readCount").textContent=docs.filter(d=>d.count>0).length;
 $("pageCount").textContent=records.length;
 $("draftCount").textContent=Object.keys(drafts).length;
 $("fileList").innerHTML=docs.map(d=>
 `<tr><td>${esc(d.name)}</td><td>${d.count}</td><td>${esc(d.status)}</td></tr>`
 ).join("");
}
function addRecord(d,loc,text){
 text=String(text||"").trim();
 if(!text)return;
 if(textSize+text.length>MAX_TEXT){
  throw Error("本文保存上限に達しました。現場資料を絞ってください。");
 }
 records.push({name:d.name,loc,text});
 textSize+=text.length;
 d.count++;
}

async function boot(){
 try{
  const base="https://cdn.jsdelivr.net/npm/pdfjs-dist@6.5.438/";
  pdfjs=await import(base+"build/pdf.mjs");
  pdfjs.GlobalWorkerOptions.workerSrc=base+"build/pdf.worker.mjs";
  await new Promise((resolve,reject)=>{
   const s=document.createElement("script");
   s.src="https://cdn.jsdelivr.net/npm/jszip@3.10.1/dist/jszip.min.js";
   s.onload=resolve;
   s.onerror=()=>reject(Error("ZIPライブラリの取得失敗"));
   document.head.append(s);
  });
  JSZip=window.JSZip;
  ready=true;
  showStatus("準備完了。設計図書を登録してください。");
 }catch(e){
  showStatus("初期化エラー："+e.message+
   "\n通信環境とChrome・Edgeのバージョンを確認してください。");
 }
}
boot();

function decode(bytes){
 for(const encoding of ["utf-8","shift_jis"]){
  try{return new TextDecoder(encoding,{fatal:true}).decode(bytes)}
  catch{}
 }
 return new TextDecoder().decode(bytes);
}

async function readPDF(bytes,d){
 const base="https://cdn.jsdelivr.net/npm/pdfjs-dist@6.5.438/";
 const task=pdfjs.getDocument({
  data:bytes,
  cMapUrl:base+"cmaps/",
  cMapPacked:true,
  standardFontDataUrl:base+"standard_fonts/",
  wasmUrl:base+"wasm/",
  useWasm:false,
  isEvalSupported:false
 });
 let missing=[];
 try{
  const pdf=await task.promise;
  const limit=Math.min(pdf.numPages,
   Math.max(1,Math.min(3000,Number($("maxPages").value)||300)));
  for(let p=1;p<=limit;p++){
   showStatus(d.name+"\nPDF解析 "+p+" / "+pdf.numPages);
   try{
    const page=await pdf.getPage(p);
    const content=await page.getTextContent();
    let lines=[],line="",lastY=null,lastEnd=null;
    for(const item of content.items){
     if(typeof item.str!=="string")continue;
     const t=item.transform||[];
     const y=t[5],x=t[4];
     if(line&&lastY!==null&&Math.abs(y-lastY)>3){
      lines.push(line);line="";lastEnd=null;
     }
     if(line&&lastEnd!==null&&x-lastEnd>3)line+=" ";
     line+=item.str;
     lastY=y;lastEnd=x+(item.width||0);
     if(item.hasEOL){
      if(line.trim())lines.push(line);
      line="";lastY=null;lastEnd=null;
     }
    }
    if(line.trim())lines.push(line);
    const text=lines.join("\n").trim();
    if(text)addRecord(d,"p."+p,text);
    else missing.push(p+"（文字なし）");
    page.cleanup();
   }catch(e){missing.push(p+"（"+e.message+"）")}
   await new Promise(r=>setTimeout(r,0));
  }
  if(limit<pdf.numPages)missing.push((limit+1)+"以降（上限）");
  d.status=missing.length
   ?"要確認：全"+pdf.numPages+"ページ／未取得 "+missing.join("、")
   :"PDF本文解析済・全"+pdf.numPages+"ページ";
  if(!d.count)d.status="本文取得不可："+d.status;
 }finally{
  await task.destroy().catch(()=>{});
 }
}

function parseXML(text){
 if(/<!DOCTYPE|<!ENTITY/i.test(text))throw Error("DTD・ENTITYは解析対象外");
 const doc=new DOMParser().parseFromString(text,"application/xml");
 if(doc.getElementsByTagName("parsererror").length)throw Error("XML解析エラー");
 return doc;
}
const nodes=(doc,name)=>Array.from(doc.getElementsByTagNameNS("*",name));

async function readOffice(bytes,d,type){
 const z=await JSZip.loadAsync(bytes);
 if(type==="docx"){
  const f=z.file("word/document.xml");
  if(!f)throw Error("Word本文なし");
  const x=parseXML(await f.async("string"));
  const text=nodes(x,"p").map(p=>
   nodes(p,"t").map(t=>t.textContent).join("")
  ).join("\n");
  addRecord(d,"Word本文",text);
 }else{
  let shared=[];
  const sf=z.file("xl/sharedStrings.xml");
  if(sf)shared=nodes(parseXML(await sf.async("string")),"si")
   .map(n=>nodes(n,"t").map(t=>t.textContent).join(""));
  const sheets=Object.values(z.files).filter(f=>
   /^xl\/worksheets\/sheet\d+\.xml$/.test(f.name));
  for(const f of sheets){
   const rows=nodes(parseXML(await f.async("string")),"row");
   const text=rows.map(r=>nodes(r,"c").map(c=>{
    const value=nodes(c,"v")[0]?.textContent||"";
    if(c.getAttribute("t")==="s")return shared[Number(value)]||"";
    if(c.getAttribute("t")==="inlineStr")
     return nodes(c,"t").map(t=>t.textContent).join("");
    return value;
   }).join("\t")).join("\n");
   addRecord(d,f.name,text);
  }
 }
 d.status=d.count?"本文解析済":"本文なし";
}

async function readFile(name,bytes){
 const d={name,count:0,status:"解析中"};
 docs.push(d);
 const type=ext(name);
 try{
  if(bytes.length>MAX_FILE)throw Error("単一資料100MB上限");
  if(type==="pdf")await readPDF(bytes,d);
  else if(["docx","xlsx"].includes(type))await readOffice(bytes,d,type);
  else if(["txt","csv","xml"].includes(type)){
   const text=decode(bytes);
   if(type==="xml")parseXML(text);
   addRecord(d,"本文",text);
   d.status="本文解析済";
  }else d.status="対象外（原本確認）";
 }catch(e){
  d.status="読取エラー："+e.message;
  warnings.push(name+"："+e.message);
 }
 refresh();
}

async function register(files){
 if(!ready){alert("解析エンジンの準備状況を確認してください");return}
 if(busy)return;
 if(Object.keys(drafts).length&&
 !confirm("資料を追加するため、作成済み下書きを消去します。続けますか？"))return;
 drafts={};current="";updateDrafts();
 busy=true;
 try{
  for(let i=0;i<files.length;i++){
   const f=files[i];
   showStatus("登録中："+f.name);
   if(ext(f.name)==="zip"){
    try{
     if(f.size>MAX_ZIP)throw Error("ZIPは300MB以下に分割してください");
     const z=await JSZip.loadAsync(await f.arrayBuffer(),{
      decodeFileName:b=>decode(Uint8Array.from(b))
     });
     const list=Object.values(z.files).filter(e=>!e.dir);
     for(let n=0;n<list.length;n++){
      const entry=list[n];
      const name=f.name+" / "+entry.name;
      if(!["pdf","docx","xlsx","txt","xml","csv"].includes(ext(entry.name))){
       docs.push({name,count:0,status:"対象外"});
       continue;
      }
      try{
       const size=entry._data?.uncompressedSize;
       if(!Number.isFinite(size)||size>MAX_FILE)
        throw Error("展開サイズ不明または100MB上限");
       showStatus("ZIP解析 "+(n+1)+"/"+list.length+"\n"+name);
       await readFile(name,await entry.async("uint8array"));
      }catch(e){
       docs.push({name,count:0,status:"読取不可："+e.message});
      }
     }
    }catch(e){
     docs.push({name:f.name,count:0,status:"ZIP読取エラー："+e.message});
    }
   }else if(f.size>MAX_FILE){
    docs.push({name:f.name,count:0,status:"100MB上限超過"});
   }else await readFile(f.name,new Uint8Array(await f.arrayBuffer()));
   $("progress").value=(i+1)/files.length*100;
   refresh();
  }
  extractFields();
  $("quantities").textContent=quantityRows().join("\n")||"数量候補未取得";
  showStatus("解析完了："+records.length+"件の本文を取得。\n未解析・対象外・警告を確認してください。");
 }finally{
  busy=false;refresh();
 }
}
$("pick").onclick=()=>$("files").click();
$("files").onchange=async e=>{
 const files=Array.from(e.target.files);
 e.target.value="";
 await register(files);
};
const drop=$("drop");
drop.ondragover=e=>{e.preventDefault();drop.classList.add("over")};
drop.ondragleave=()=>drop.classList.remove("over");
drop.ondrop=e=>{
 e.preventDefault();drop.classList.remove("over");
 register(Array.from(e.dataTransfer.files));
};

function extractFields(){
 let report=[];
 for(const [key,names] of Object.entries(fields)){
  const found=new Map();
  for(const r of records){
   const text=norm(r.text);
   for(const name of names){
    const pattern=name.split("").join("[ \\t]*");
    const re=new RegExp(
     "^[ \\t]*"+pattern+"(?:[ \\t]*[:：][ \\t]*|[ \\t]+)([^\\r\\n]{2,180})","gm");
    for(const m of text.matchAll(re)){
     const value=m[1].trim();
     if(value)found.set(value,r.name+" "+r.loc);
    }
   }
  }
  if(found.size===1&&!$("f_"+key).value)
   $("f_"+key).value=[...found.keys()][0];
  for(const [value,source] of found)
   report.push(names[0]+"："+value+"\n出典："+source+
    (found.size>1?"（複数候補・要確認）":""));
 }
 $("candidates").textContent=report.join("\n\n")||
  "自動抽出できなかった項目は入力してください。";
}
$("extract").onclick=extractFields;

function evidence(keys,limit=6){
 keys=String(keys).split(/\s+/).filter(Boolean).map(compact);
 let results=[];
 if(!keys.length)return results;
 for(const r of records){
  const lines=r.text.split(/\r?\n/);
  for(let i=0;i<lines.length;i++){
   const s=compact(lines[i]);
   const matches=keys.filter(k=>s.includes(k)).length;
   if(!matches)continue;
   const text=lines.slice(Math.max(0,i-1),i+3).join(" ").trim();
   if(text.length<5)continue;
   results.push({name:r.name,loc:r.loc,text:text.slice(0,900),
    score:matches*3+(/特記仕様|数量総括|設計図書/.test(r.name)?2:0)});
  }
 }
 const seen=new Set();
 return results.sort((a,b)=>b.score-a.score).filter(r=>{
  const key=r.name+r.loc+r.text;
  if(seen.has(key))return false;seen.add(key);return true;
 }).slice(0,limit);
}

function quantityRows(){
 const rows=[];
 for(const r of records){
  if(!/数量総括|数量表|工事数量|数量計算/.test(r.name))continue;
  for(const line of r.text.split(/\r?\n/)){
   const s=norm(line);
   if(/\d/.test(s)&&/m[23]|㎥|㎡|m³|m²|箇所|本|基|式|t\b|台|トン/.test(s))
    rows.push("["+r.name+" "+r.loc+"] "+line.trim());
  }
 }
 return [...new Set(rows)].slice(0,150);
}

$("search").onclick=()=>{
 const q=$("query").value.trim();
 const list=q?evidence(q,50):[];
 $("hits").innerHTML=list.map(r=>
  `<details class="panel"><summary>${esc(r.name)} ｜ ${esc(r.loc)}</summary><pre>${esc(r.text)}</pre></details>`
 ).join("")||"<p>一致する本文がありません。</p>";
};

function download(content,name,type="text/plain;charset=utf-8"){
 const blob=content instanceof Blob?content:new Blob([content],{type});
 const url=URL.createObjectURL(blob);
 const a=document.createElement("a");a.href=url;a.download=name;
 document.body.append(a);a.click();a.remove();
 setTimeout(()=>URL.revokeObjectURL(url),10000);
}
$("saveText").onclick=()=>download(
 records.map(r=>"【"+r.name+" "+r.loc+"】\n"+r.text).join("\n\n"),
 "設計図書_解析本文.txt");

for(const group of [...new Set(catalog.map(d=>d[0]))]){
 const area=document.createElement("div");
 area.className="group";
 const h=document.createElement("h3");h.textContent=group;
 const grid=document.createElement("div");grid.className="docgrid";
 catalog.forEach((d,i)=>{
  if(d[0]!==group)return;
  const label=document.createElement("label");label.className="doc";
  const cb=document.createElement("input");cb.type="checkbox";cb.dataset.doc=i;
  const name=document.createElement("strong");name.textContent=d[1];
  label.append(cb,name);grid.append(label);
 });
 area.append(h,grid);$("catalog").append(area);
}
$("selectStart").onclick=()=>document.querySelectorAll("[data-doc]").forEach(
 b=>b.checked=catalog[Number(b.dataset.doc)][0]==="着手前");
$("selectAll").onclick=()=>document.querySelectorAll("[data-doc]").forEach(b=>b.checked=true);
$("clearAll").onclick=()=>document.querySelectorAll("[data-doc]").forEach(b=>b.checked=false);

function commonInfo(){
 return Object.entries(fields).map(([k,n])=>
 n[0]+"："+($("f_"+k).value.trim()||"［要確認］")).join("\n");
}

function makeDraft(i){
 const [stage,title,keys,type]=catalog[i];
 const body=[
 title,"【下書き・原本照合前】","",
 "■ 工事基本情報",commonInfo(),
 "作成日："+($("date").value||"［要入力］"),""
 ];
 const subject=$("subject").value.trim();
 const condition=$("condition").value.trim();
 if(subject)body.push("■ 件名",subject,"");
 if(type==="meeting")body.push("発議区分："+$("kind").value,"");
 if(condition)body.push("■ 担当者入力事項",condition,"");
 const appendSection=(heading,k,action)=>{
  body.push("■ "+heading);
  const hits=evidence(k,4);
  if(hits.length){
   body.push("設計図書等に示された関連条件（原本照合前）：");
   for(const h of hits)
    body.push("・"+h.text+"\n　出典："+h.name+" "+h.loc);
  }else body.push("［関連する本文を取得できていません］");
  body.push("対応方針案："+action,"");
 };

 if(type==="plan"){
  const sections=[0,1,2,5,7,23].includes(i)
   ?planSections
   :planSections.filter(s=>compact(s[1]).includes(compact(keys.split(" ")[0]))||
     ["工事概要","提出・納品"].includes(s[0]));
  appendSection("対象工種・適用条件",keys,
   "以下の条件を原本と照合し、対象範囲及び具体的な方法を確定する。");
  for(const [heading,k,action] of sections)appendSection(heading,k,action);
  const qty=quantityRows();
  if(qty.length)body.push("■ 設計数量候補",...qty,
   "数量・単位の対応は原表にて確認する。","");
 }else if(type==="meeting"){
  appendSection("協議・提出の根拠",keys+" "+subject,
   "該当条項、図面番号及び適用条件を照合する。");
  body.push("■ 現状・発議理由",condition||"［実際の現場条件を記入］","",
   "■ 受注者の提案","［提案内容・施工方法を確定後に記入］","");
  appendSection("数量・工期・品質への影響","数量 工期 品質 安全 "+keys,
   "変更前後の数量、工期、費用及び施工上の影響を確認する。");
  body.push("■ 発注者回答欄","回答：","処理日：","確認者：","");
 }else{
  appendSection("適用基準・確認対象",keys,
   "関連条件と対象資料を確認し、実績記録に基づいて確認する。");
  body.push("■ 実施・確認記録","実施日：","実測値・実績：","判定：",
   "是正事項：","現場代理人：","監理技術者：","");
 }

 const incomplete=docs.filter(d=>/エラー|要確認|不可|上限/.test(d.status));
 if(incomplete.length){
  body.push("■ 解析上の未確認資料",
   ...incomplete.slice(0,50).map(d=>d.name+"："+d.status),"");
 }
 body.push("■ 提出前確認",
  "数値、仕様、工法、実績及び発注者指定様式を担当者が照合する。");
 return body.join("\n");
}

$("generate").onclick=()=>{
 if(busy)return;
 const ids=Array.from(document.querySelectorAll("[data-doc]:checked"))
  .map(b=>Number(b.dataset.doc));
 if(!ids.length){alert("作成する書類を選択してください");return}
 if(!records.length){
  alert("本文解析ができていません。資料の読取状況を確認してください。");return;
 }
 const pdfs=docs.filter(d=>ext(d.name)==="pdf");
 if(pdfs.length&&!pdfs.some(d=>d.count>0)){
  alert("PDF本文が1ページも取得できていません。");return;
 }
 if(docs.some(d=>/エラー|要確認|不可|上限/.test(d.status))&&
 !confirm("未解析・部分解析の資料があります。取得済み本文だけで下書きを作成しますか？"))return;
 if(ids.some(i=>drafts[i])&&!confirm("作成済み下書きを上書きしますか？"))return;
 for(const i of ids)drafts[i]=makeDraft(i);
 current=String(ids[0]);
 updateDrafts();refresh();go("review");
};

function updateDrafts(){
 $("draftSelect").innerHTML=Object.keys(drafts).map(i=>
 `<option value="${i}">${esc(catalog[i][1])}</option>`).join("");
 if(!Object.hasOwn(drafts,current))current=Object.keys(drafts)[0]??"";
 $("draftSelect").value=current;
 $("editor").value=drafts[current]||"";
}
$("draftSelect").onchange=e=>{current=e.target.value;updateDrafts()};
$("editor").oninput=()=>{if(current!=="")drafts[current]=$("editor").value};

const xmlHeader='<?xml version="1.0" encoding="UTF-8" standalone="yes"?>';
const relNS="http://schemas.openxmlformats.org/package/2006/relationships";
const officeNS="http://schemas.openxmlformats.org/officeDocument/2006/relationships";
function xe(s){return esc(String(s).replace(/[\u0000-\u0008\u000B\u000C\u000E-\u001F]/g,""))}

function packageBase(z,main,type,extra=""){
 z.file("[Content_Types].xml",xmlHeader+
 `<Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
 <Default Extension="rels" ContentType="application/vnd.openxmlformats-package.relationships+xml"/>
 <Default Extension="xml" ContentType="application/xml"/>
 <Override PartName="/${main}" ContentType="${type}"/>${extra}</Types>`);
 z.file("_rels/.rels",xmlHeader+
 `<Relationships xmlns="${relNS}"><Relationship Id="rId1" Type="${officeNS}/officeDocument" Target="${main}"/></Relationships>`);
}

async function makeWord(i,text){
 const z=new JSZip();
 packageBase(z,"word/document.xml",
 "application/vnd.openxmlformats-officedocument.wordprocessingml.document.main+xml");
 const paras=text.split("\n").map((line,n)=>{
  const heading=n===0||line.startsWith("■");
  const size=n===0?34:heading?25:21;
  return `<w:p><w:pPr><w:spacing w:after="100"/>${n===0?'<w:jc w:val="center"/>':heading?'<w:keepNext/>':''}</w:pPr>
  <w:r><w:rPr><w:rFonts w:eastAsia="Yu Gothic" w:ascii="Yu Gothic"/>
  <w:color w:val="000000"/><w:sz w:val="${size}"/>${heading?"<w:b/>":""}</w:rPr>
  <w:t xml:space="preserve">${xe(line)}</w:t></w:r></w:p>`;
 }).join("");
 z.file("word/document.xml",xmlHeader+
 `<w:document xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main">
 <w:body>${paras}<w:sectPr><w:pgSz w:w="11906" w:h="16838"/>
 <w:pgMar w:top="1134" w:right="1134" w:bottom="1134" w:left="1134" w:header="0" w:footer="0"/>
 </w:sectPr></w:body></w:document>`);
 return z.generateAsync({type:"uint8array",compression:"DEFLATE"});
}

async function makeExcel(){
 const z=new JSZip();
 const sheets=[
  ["作成管理",[
   ["段階","書類名","作成状況","担当者確認","備考"],
   ...catalog.map((d,i)=>[d[0],d[1],Object.hasOwn(drafts,i)?"下書き済":"未作成","",""])
  ]],
  ["資料索引",[
   ["資料名","本文取得数","解析状況"],
   ...docs.map(d=>[d.name,d.count,d.status])
  ]],
  ["数量候補",[["設計数量・工種の原文候補"],...quantityRows().map(s=>[s])]]
 ];
 const ns="http://schemas.openxmlformats.org/spreadsheetml/2006/main";
 let extra="",sheetXML="",rels="";
 sheets.forEach(([name,rows],i)=>{
  const n=i+1;
  extra+=`<Override PartName="/xl/worksheets/sheet${n}.xml" ContentType="application/vnd.openxmlformats-officedocument.spreadsheetml.worksheet+xml"/>`;
  sheetXML+=`<sheet name="${xe(name)}" sheetId="${n}" r:id="rId${n}"/>`;
  rels+=`<Relationship Id="rId${n}" Type="${officeNS}/worksheet" Target="worksheets/sheet${n}.xml"/>`;
  const content=rows.map((r,j)=>`<row r="${j+1}">${r.map((c,k)=>
   `<c r="${String.fromCharCode(65+k)}${j+1}" t="inlineStr"><is><t xml:space="preserve">${xe(String(c).slice(0,32000))}</t></is></c>`
  ).join("")}</row>`).join("");
  z.file(`xl/worksheets/sheet${n}.xml`,xmlHeader+
   `<worksheet xmlns="${ns}"><cols><col min="1" max="1" width="45" customWidth="1"/>
   <col min="2" max="5" width="32" customWidth="1"/></cols>
   <sheetData>${content}</sheetData></worksheet>`);
 });
 packageBase(z,"xl/workbook.xml",
 "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet.main+xml",extra);
 z.file("xl/workbook.xml",xmlHeader+
 `<workbook xmlns="${ns}" xmlns:r="${officeNS}"><sheets>${sheetXML}</sheets></workbook>`);
 z.file("xl/_rels/workbook.xml.rels",xmlHeader+
 `<Relationships xmlns="${relNS}">${rels}</Relationships>`);
 return z.generateAsync({type:"uint8array",compression:"DEFLATE"});
}

const safeName=s=>s.replace(/[\\/:*?"<>|]/g,"_");
async function exportRun(fn){
 if(!ready||busy)return;
 busy=true;
 try{await fn()}catch(e){alert("出力エラー："+e.message)}
 finally{busy=false}
}
$("word").onclick=()=>exportRun(async()=>{
 if(current===""){alert("書類を作成してください");return}
 download(await makeWord(current,drafts[current]),
  safeName(catalog[current][1])+".docx",
  "application/vnd.openxmlformats-officedocument.wordprocessingml.document");
});
$("excel").onclick=()=>exportRun(async()=>{
 download(await makeExcel(),"施工管理_作成管理一覧.xlsx",
  "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet");
});
$("zip").onclick=()=>exportRun(async()=>{
 if(!Object.keys(drafts).length){alert("下書きがありません");return}
 const z=new JSZip();
 for(const [i,text] of Object.entries(drafts)){
  z.file(catalog[i][0]+"/"+safeName(catalog[i][1])+".docx",
   await makeWord(i,text));
 }
 z.file("作成管理一覧.xlsx",await makeExcel());
 download(await z.generateAsync({type:"blob",compression:"DEFLATE"}),
  "施工管理書類一式.zip");
});
$("prompt").onclick=()=>{
 if(current===""){alert("対象書類を選択してください");return}
 download([
 "以下は施工管理書類の下書きと設計図書の抜粋です。",
 "記載された根拠を用いて具体的な施工管理文章に整えてください。",
 "不明な条件、数量、実績、承認、検査結果は捏造せず要確認としてください。",
 "根拠ファイル名とページ番号を維持してください。",
 "A4縦、白黒、ヘッダー・フッターなしのWord文書を想定します。",
 "",drafts[current]
 ].join("\n"),"AI文章作成指示.txt");
};
$("reset").onclick=()=>{
 if(busy)return;
 if(confirm("現在の現場データを消去します。原本は変更しません。")){
  location.reload();
 }
};
refresh();
</script>
</body>
</html>
