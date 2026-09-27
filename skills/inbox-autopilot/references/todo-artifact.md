# The interactive to-do board (artifact)

The board is a self-contained HTML artifact published on claude.ai. Setup creates it once and records its
URL in `autopilot_config.json`. Each daily run updates ONLY the `state` JSON and republishes to the same URL
with `capabilities {"artifact": {}}`, preserving the page's HTML/CSS/JS exactly (doctype first, complete
document). The page saves the viewer's checkbox ticks back to itself.

## Updating without breaking the page

1. Read the published `index.html` to a local file.
2. Extract the `<script id="state" type="application/json">…</script>` content and `JSON.parse` it.
3. Change only `state.updated`, `state.lastRun`, and merge into `state.items`. Never resurrect a `done:true`
   item.
4. `JSON.stringify` and splice it back into the exact same spot; leave every other byte of the document
   unchanged.
5. Republish to the same URL.

## `lastRun` schema

```json
{"when":"Wed, Sep 24, 2026 · 7:00 AM CT",
 "note":"<1–2 sentence plain-English recap of exactly what THIS run did; use <strong>, <em>, and <span class='amt'>$X</span> for amounts; write literal ampersands as &amp;>",
 "tone":"good"}
```
`tone` is "good" if anything happened this run, else "quiet". If nothing was actionable, the note must say so
explicitly (e.g. "All quiet — no new recruiter mail or action items since the last run.").

## Item schema (use these exact fields)

```json
{"id":"<stable-slug>","cat":"<Finance|Immigration|School|Job|Health|Family|Personal>",
 "priority":"<high|med|low>","title":"<short title>",
 "sub":"<one-line detail; amounts in <span class='amt'>$X</span>; italics via <em>…</em>>",
 "added":"<Mon D — date first spotted>","due":"<YYYY-MM-DD or null>","done":false}
```

Rules: keep `id` stable across runs so ticks persist. Always set `due` to the real deadline when the email
states one — the page turns an item red once its due date passes; use `null` only when there is genuinely no
date. Priority: high = bills, immigration/legal, hard near-term deadlines; med = follow-ups, verifications,
recurring bills not yet near due; low = informational/optional. School items name the child in `sub`.

## Board HTML template (for setup — publish this, then fill `state`)

```html
<!doctype html><html><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>Deadlines & To-Do</title><link id="fonts" rel="stylesheet" href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500;600&family=IBM+Plex+Sans:wght@400;500;600&family=IBM+Plex+Serif:wght@500;600&display=swap"><style id="sty">
:root{--bg:#f6f7f9;--surface:#fff;--surface-2:#eef1f5;--ink:#182230;--ink-soft:#43505f;--ink-faint:#6a7686;--line:#dde2ea;--accent:#0e6e78;--accent-ink:#0b565e;--good:#2c7a54;--warn:#a65c00;--crit:#a8323f;--shadow:0 1px 2px rgba(24,34,48,.05),0 8px 24px rgba(24,34,48,.06)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#0e131a;--surface:#161d27;--surface-2:#1d2632;--ink:#e7ecf3;--ink-soft:#b0bccb;--ink-faint:#7d8a9b;--line:#27313e;--accent:#35b3bf;--accent-ink:#6fd3dc;--good:#4fbe8a;--warn:#e0a54e;--crit:#e2707c}}
:root[data-theme="dark"]{--bg:#0e131a;--surface:#161d27;--surface-2:#1d2632;--ink:#e7ecf3;--ink-soft:#b0bccb;--ink-faint:#7d8a9b;--line:#27313e;--accent:#35b3bf;--accent-ink:#6fd3dc;--good:#4fbe8a;--warn:#e0a54e;--crit:#e2707c}
*{box-sizing:border-box}body{margin:0;background:var(--bg);color:var(--ink);font-family:"IBM Plex Sans",system-ui,sans-serif;font-size:16px;line-height:1.5}
.wrap{max-width:780px;margin:0 auto;padding:0 20px 80px}h1{font-family:"IBM Plex Serif",Georgia,serif;font-size:clamp(1.9rem,5vw,2.5rem);letter-spacing:-.02em;margin:44px 0 8px}
.eyebrow{font-family:"IBM Plex Mono",monospace;font-size:.72rem;font-weight:500;letter-spacing:.14em;text-transform:uppercase;color:var(--accent-ink)}
.runbar{margin-top:14px;background:var(--surface-2);border:1px solid var(--line);border-left:4px solid var(--accent);border-radius:12px;padding:11px 15px}.runbar.good{border-left-color:var(--good)}.runbar.quiet{border-left-color:var(--ink-faint)}
.runbar .rl{font-family:"IBM Plex Mono",monospace;font-size:.68rem;letter-spacing:.08em;text-transform:uppercase;color:var(--accent-ink)}.runbar .rw{font-family:"IBM Plex Mono",monospace;font-size:.74rem;color:var(--ink-faint);margin:2px 0 4px}.runbar .rn{font-size:.92rem}
.totbar{display:flex;flex-wrap:wrap;gap:10px;margin-top:16px}.tot{background:var(--surface);border:1px solid var(--line);border-radius:12px;padding:10px 15px;box-shadow:var(--shadow)}.tot .n{font-family:"IBM Plex Serif",serif;font-size:1.4rem;font-weight:600}.tot.over .n{color:var(--crit)}.tot .l{font-size:.72rem;color:var(--ink-faint);margin-top:5px;font-family:"IBM Plex Mono",monospace;text-transform:uppercase}
h2{font-family:"IBM Plex Serif",serif;font-size:1.1rem;margin:26px 0 11px}
.item{display:grid;grid-template-columns:26px 1fr auto;gap:4px 12px;align-items:start;background:var(--surface);border:1px solid var(--line);border-left-width:5px;border-radius:12px;padding:13px 16px;margin-bottom:9px;box-shadow:var(--shadow)}
.item.over{border-left-color:var(--crit)}.item.soon{border-left-color:var(--warn)}.item.later{border-left-color:var(--accent)}.item.none{border-left-color:var(--ink-faint)}.item.done{border-left-color:var(--good);opacity:.72}
.chk{grid-column:1;width:20px;height:20px;margin-top:1px;cursor:pointer;accent-color:var(--accent)}.item .title{grid-column:2;font-weight:600}.item.done .title{text-decoration:line-through;color:var(--ink-faint)}
.chips{grid-column:3;justify-self:end;display:flex;flex-wrap:wrap;gap:5px;justify-content:flex-end;max-width:210px}.chip{font-family:"IBM Plex Mono",monospace;font-size:.64rem;font-weight:600;text-transform:uppercase;padding:3px 8px;border-radius:999px;white-space:nowrap;border:1px solid}
.item .sub{grid-column:2;color:var(--ink-soft);font-size:.9rem}.dates{grid-column:2;display:flex;flex-wrap:wrap;gap:5px 14px;margin-top:5px;font-family:"IBM Plex Mono",monospace;font-size:.74rem}.dates .added{color:var(--ink-faint)}.dates .due{color:var(--ink-soft)}.dates .due.over{color:var(--crit);font-weight:600}.dates .due.soon{color:var(--warn);font-weight:600}
.donehdr{cursor:pointer;user-select:none;color:var(--ink-faint);font-family:"IBM Plex Mono",monospace;font-size:.8rem;text-transform:uppercase;margin:24px 0 10px}.foot{margin-top:30px;padding-top:16px;border-top:1px solid var(--line);color:var(--ink-faint);font-size:.82rem}.amt{font-family:"IBM Plex Mono",monospace;font-variant-numeric:tabular-nums}em{color:var(--ink-faint);font-style:italic}.save{font-family:"IBM Plex Mono",monospace;font-size:.72rem;color:var(--ink-faint);margin-left:8px}
</style></head><body><div class="wrap"><div id="app"></div><p class="foot">Refreshed from your inbox; the banner up top shows what the last run did. <strong>Ticks are saved to the page itself</strong> — completed items drop into "Done". Overdue items turn red once their due date passes.</p></div>
<script id="state" type="application/json">{"updated":"","lastRun":{"when":"","note":"Set up — the first scheduled run will fill this in.","tone":"quiet"},"items":[]}</script>
<script id="boot">
(function(){var BOOT=document.getElementById('boot').textContent,FONTS=document.getElementById('fonts').outerHTML,STY=document.getElementById('sty').outerHTML,state=JSON.parse(document.getElementById('state').textContent),showDone=false,saveEl;
var CATS={Finance:'#2c9d63',Immigration:'#c0433f',School:'#12a594',Job:'#3b7dd8',Health:'#1499b8',Family:'#9b6cd6',Personal:'#7d8a9b',Other:'#7d8a9b'},PRIO={high:'High',med:'Medium',low:'Low'},MON=['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
function today(){var d=new Date();return new Date(d.getFullYear(),d.getMonth(),d.getDate());}function parseDue(s){if(!s)return null;var p=s.split('-');if(p.length!==3)return null;return new Date(+p[0],+p[1]-1,+p[2]);}function daysUntil(d){return Math.round((d-today())/86400000);}function fmtDue(d){return MON[d.getMonth()]+' '+d.getDate();}
function urg(it){var d=parseDue(it.due);if(!d)return 'none';var n=daysUntil(d);return n<0?'over':n<=7?'soon':'later';}
function catChip(it){var c=CATS[it.cat]||CATS.Other;return '<span class="chip" style="color:'+c+';background:'+c+'1f;border-color:'+c+'66">'+(it.cat||'Other')+'</span>';}
function prioChip(it){var lv=it.priority||'low',col=lv==='high'?'var(--crit)':lv==='med'?'var(--warn)':'var(--accent)';return '<span class="chip" style="color:'+col+';background:transparent;border-color:'+col+'">'+(PRIO[lv]||'Low')+'</span>';}
function dueLine(it){var d=parseDue(it.due);if(!d)return '<span class="due">No date</span>';var n=daysUntil(d),cls=n<0?'over':n<=7?'soon':'',txt;if(it.done)txt='Due '+fmtDue(d);else if(n<0)txt='⚠ Overdue — was due '+fmtDue(d)+' ('+(-n)+'d ago)';else if(n===0)txt='Due today · '+fmtDue(d);else if(n===1)txt='Due tomorrow · '+fmtDue(d);else txt='Due '+fmtDue(d)+' · in '+n+'d';return '<span class="due '+(it.done?'':cls)+'">'+txt+'</span>';}
function itemHTML(it){return '<div class="item '+(it.done?'done':urg(it))+'" data-id="'+it.id+'"><input type="checkbox" class="chk" data-id="'+it.id+'"'+(it.done?' checked':'')+'><div class="title">'+it.title+'</div><div class="chips">'+catChip(it)+prioChip(it)+'</div><div class="sub">'+it.sub+'</div><div class="dates"><span class="added">Added '+(it.added||'—')+'</span>'+dueLine(it)+'</div></div>';}
var GROUPS=[['over','🔴 Overdue'],['soon','🟠 Due this week'],['later','🔵 Upcoming'],['none','⚪ No date set']];
function render(){var active=state.items.filter(function(i){return !i.done;}),done=state.items.filter(function(i){return i.done;}),buckets={over:[],soon:[],later:[],none:[]};active.forEach(function(i){buckets[urg(i)].push(i);});var over=buckets.over.length,lr=state.lastRun;
var html='<span class="eyebrow">Daily brief · pulled from your inbox</span><h1>Deadlines &amp; To-Do</h1><div class="meta" style="font-family:\'IBM Plex Mono\',monospace;font-size:.78rem;color:var(--ink-faint);margin-top:12px">tick to complete — done items move below<span class="save" id="save"></span></div>'+(lr?('<div class="runbar '+(lr.tone||'')+'"><div class="rl">Last run</div><div class="rw">'+lr.when+'</div><div class="rn">'+lr.note+'</div></div>'):'')+'<div class="totbar"><div class="tot"><div class="n amt">'+active.length+'</div><div class="l">open</div></div><div class="tot'+(over?' over':'')+'"><div class="n amt">'+over+'</div><div class="l">overdue</div></div><div class="tot"><div class="n amt">'+done.length+'</div><div class="l">done</div></div></div>';
GROUPS.forEach(function(g){var rows=buckets[g[0]];if(rows.length)html+='<h2>'+g[1]+'</h2>'+rows.map(itemHTML).join('');});
if(!active.length)html+='<h2>✅ All clear</h2><div class="item done" style="opacity:1"><div class="title" style="grid-column:2;text-decoration:none;color:var(--ink)">Nothing open right now.</div><div class="sub">New deadlines appear here as they arrive by email.</div></div>';
if(done.length){html+='<div class="donehdr" id="donehdr">'+(showDone?'▾':'▸')+' Done ('+done.length+')</div>';if(showDone)html+=done.map(itemHTML).join('');}
document.getElementById('app').innerHTML=html;saveEl=document.getElementById('save');
document.querySelectorAll('.chk').forEach(function(b){b.addEventListener('change',function(){toggle(b.getAttribute('data-id'),b.checked);});});var dh=document.getElementById('donehdr');if(dh)dh.addEventListener('click',function(){showDone=!showDone;render();});}
function buildDoc(){return '<!doctype html><html><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>Deadlines & To-Do</title>'+FONTS+STY+'</head><body><div class="wrap"><div id="app"></div><p class="foot">Refreshed from your inbox; the banner up top shows what the last run did. <strong>Ticks are saved to the page itself<\/strong> — completed items drop into "Done". Overdue items turn red once their due date passes.<\/p><\/div><script id="state" type="application/json">'+JSON.stringify(state)+'<\/script><script id="boot">'+BOOT+'<\/script><\/body><\/html>';}
async function persist(){try{localStorage.setItem('autopilot-todo',JSON.stringify(state));}catch(e){}try{var a=await claude.use('artifact');if(a){if(saveEl)saveEl.textContent='· saving…';await a.publish(buildDoc());if(saveEl)saveEl.textContent='· saved';return;}}catch(e){if(e&&e.code==='conflict')return;}if(saveEl)saveEl.textContent='· saved on this device';}
function toggle(id,val){for(var i=0;i<state.items.length;i++){if(state.items[i].id===id){state.items[i].done=val;break;}}render();persist();}
try{var ls=localStorage.getItem('autopilot-todo');if(ls){var s=JSON.parse(ls);if(s&&s.items){var m={};s.items.forEach(function(i){m[i.id]=i.done;});state.items.forEach(function(i){if(Object.prototype.hasOwnProperty.call(m,i.id))i.done=m[i.id];});}}}catch(e){}
render();})();
</script></body></html>
```
