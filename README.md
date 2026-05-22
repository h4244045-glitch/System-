<!DOCTYPE html>
<html lang="ta">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="theme-color" content="#05080f">
<title>SL Dev System V4 - Hunter Evolution</title>
<link href="https://fonts.googleapis.com/css2?family=Rajdhani:wght@400;500;600;700&family=Orbitron:wght@400;700;900&display=swap" rel="stylesheet">
<style>
*{box-sizing:border-box;margin:0;padding:0;-webkit-tap-highlight-color:transparent}
html,body{height:100%;overflow:hidden;background:#05080f}
:root{
  --bg:#05080f;--panel:#0a0f1e;--border:#1a2a4a;
  --accent:#00d4ff;--gold:#ffd700;--green:#00ff88;
  --red:#e74c3c;--text:#c8d8f0;--dim:#4a6080;
  --purple:#9b59b6;--matrix:rgba(0, 212, 255, 0.15);
}
body{font-family:'Rajdhani',sans-serif;color:var(--text);background:var(--bg);display:flex;flex-direction:column;height:100dvh;overflow:hidden}
.bg-grid{position:fixed;inset:0;background-image:linear-gradient(rgba(0,212,255,0.03) 1px,transparent 1px),linear-gradient(90deg,rgba(0,212,255,0.03) 1px,transparent 1px);background-size:40px 40px;pointer-events:none;z-index:0}

/* MATRIX LINE GLOW EFFECT */
.bg-grid::before {
  content: ''; position: absolute; top: 0; left: 0; width: 100%; height: 100%;
  background: linear-gradient(180deg, transparent 0%, var(--matrix) 50%, transparent 100%);
  animation: scanline 6s linear infinite; pointer-events: none;
}
@keyframes scanline { 0% { transform: translateY(-100%); } 100% { transform: translateY(100%); } }

/* TOP BAR */
.top-bar{flex-shrink:0;background:var(--panel);border-bottom:1px solid var(--border);padding:12px 16px;position:relative;z-index:10;box-shadow: 0 4px 20px rgba(0,0,0,0.5)}
.top-inner{display:flex;align-items:center;justify-content:space-between}
.sys-label{font-family:'Orbitron',monospace;font-size:9px;letter-spacing:4px;color:var(--accent);animation:pulse 2s infinite;display:flex;align-items:center;gap:5px}
.hunter-title{font-family:'Orbitron',monospace;font-size:16px;font-weight:900;color:#fff;text-shadow: 0 0 10px rgba(0,212,255,0.5)}
.hunter-title span{color:var(--accent)}
.rank-pill{font-family:'Orbitron',monospace;font-size:10px;font-weight:700;padding:4px 12px;border:1px solid var(--gold);color:var(--gold);letter-spacing:2px;background:rgba(255,215,0,0.05);animation:rglow 3s infinite}

@keyframes rglow{0%,100%{box-shadow:0 0 6px rgba(255,215,0,0.2), inset 0 0 4px rgba(255,215,0,0.1)}50%{box-shadow:0 0 16px rgba(255,215,0,0.6), inset 0 0 8px rgba(255,215,0,0.3)}}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:0.6}}

/* VOICE BAR */
.voice-bar{flex-shrink:0;background:rgba(0,212,255,0.02);border-bottom:1px solid var(--border);padding:8px 16px;display:flex;align-items:center;gap:10px;position:relative;z-index:10}
.voice-btn{width:36px;height:36px;border:1px solid var(--accent);background:rgba(0,212,255,0.1);color:var(--accent);font-size:16px;cursor:pointer;display:flex;align-items:center;justify-content:center;flex-shrink:0;border-radius:50%;transition:all 0.3s}
.voice-btn.speaking{background:rgba(0,212,255,0.2);box-shadow: 0 0 12px var(--accent);animation:vblink 1s infinite}
@keyframes vblink{0%,100%{transform:scale(1)}50%{transform:scale(1.05);opacity:0.8}}
.voice-status{flex:1;font-size:12px;color:var(--dim);letter-spacing:0.5px}
.voice-lang-toggle{font-family:'Orbitron',monospace;font-size:9px;padding:4px 8px;border:1px solid var(--border);color:var(--dim);background:transparent;cursor:pointer}
.voice-lang-toggle.ta{border-color:var(--gold);color:var(--gold);background:rgba(255,215,0,0.05)}

/* STATS */
.stats-bar{flex-shrink:0;background:rgba(10,15,30,0.95);border-bottom:1px solid var(--border);padding:8px 16px;display:grid;grid-template-columns:repeat(3,1fr);position:relative;z-index:10}
.stat-item{text-align:center}
.stat-item+.stat-item{border-left:1px solid var(--border)}
.stat-lbl{font-size:9px;letter-spacing:2px;color:var(--dim);text-transform:uppercase;margin-bottom:2px}
.stat-val{font-family:'Orbitron',monospace;font-size:18px;font-weight:700;color:var(--accent)}

/* XP TRACKER */
.xp-wrap{flex-shrink:0;padding:6px 16px;background:var(--bg);border-bottom:1px solid var(--border);position:relative;z-index:10}
.xp-labels{display:flex;justify-content:space-between;font-size:9px;letter-spacing:1px;color:var(--dim);margin-bottom:4px}
.xp-track{height:6px;background:var(--panel);border:1px solid var(--border);overflow:hidden}
.xp-fill{height:100%;background:linear-gradient(90deg,#0055aa,var(--accent));transition:width 0.5s ease}

/* TABS */
.tabs{flex-shrink:0;display:flex;background:var(--panel);border-bottom:1px solid var(--border);position:relative;z-index:10}
.tab{flex:1;padding:12px 2px;text-align:center;font-family:'Orbitron',monospace;font-size:9px;letter-spacing:1.5px;color:var(--dim);cursor:pointer;transition:all 0.2s;border-bottom:2px solid transparent}
.tab.active{color:var(--accent);border-bottom-color:var(--accent);background:rgba(0,212,255,0.03);font-weight:700}

/* CONTENT CONTAINER */
.content{flex:1;overflow-y:auto;overflow-x:hidden;-webkit-overflow-scrolling:touch;position:relative;z-index:1;padding-bottom:20px}
.tab-panel{display:none;padding:14px}
.tab-panel.active{display:block}
.section-title{font-family:'Orbitron',monospace;font-size:10px;letter-spacing:3px;color:var(--accent);text-transform:uppercase;margin-bottom:12px;display:flex;align-items:center;gap:8px}
.section-title::after{content:'';flex:1;height:1px;background:linear-gradient(90deg,var(--border),transparent)}

/* ACTIVE QUEST CARD V4 */
.quest-card{background:var(--panel);border:1px solid var(--border);padding:14px;margin-bottom:12px;position:relative;box-shadow: 0 4px 15px rgba(0,0,0,0.3)}
.quest-header{display:flex;align-items:center;gap:8px;margin-bottom:6px}
.rank-tag{font-family:'Orbitron',monospace;font-size:8px;font-weight:700;padding:2px 8px;letter-spacing:1px;border-radius:2px}
.rE{background:rgba(100,100,100,0.15);color:#aaa;border:1px solid #555}
.rD{background:rgba(0,150,200,0.15);color:#4bc;border:1px solid #1a6a8a}
.rC{background:rgba(100,200,100,0.15);color:#4dc;border:1px solid #2a7a4a}
.rB{background:rgba(150,100,200,0.15);color:#b6f;border:1px solid #6a3a8a}
.rA{background:rgba(255,150,0,0.15);color:var(--gold);border:1px solid #9a6a00}
.rS{background:rgba(255,50,50,0.15);color:#ff5555;border:1px solid #b31a1a;text-shadow:0 0 5px #ff0000}

.quest-name{font-size:14px;font-weight:700;color:#fff;flex:1}
.quest-xp{font-family:'Orbitron',monospace;font-size:11px;color:var(--gold)}
.quest-desc{font-size:12px;color:var(--text);opacity:0.85;margin-bottom:10px;line-height:1.4}

/* SUB TASKS CHECKLIST */
.task-checklist-box{background:rgba(0,0,0,0.3);border:1px solid var(--border);padding:10px;margin-bottom:12px}
.checklist-title{font-family:'Orbitron',monospace;font-size:8px;letter-spacing:1px;color:var(--dim);margin-bottom:6px;text-transform:uppercase}
.task-item{display:flex;align-items:flex-start;gap:8px;font-size:12px;color:var(--text);margin-bottom:6px}
.task-item input[type="checkbox"]{margin-top:3px;accent-color:var(--accent)}
.task-item.checked-done {text-decoration: line-through; color: var(--dim)}

/* SCREENSHOT VERIFICATION BOX */
.screenshot-zone{border:1px dashed rgba(0,212,255,0.3);background:rgba(0,212,255,0.02);padding:12px;text-align:center;margin-top:10px;position:relative;transition:all 0.3s}
.screenshot-zone:hover{border-color:var(--accent);background:rgba(0,212,255,0.05)}
.ss-label{font-size:11px;color:var(--accent);letter-spacing:0.5px;cursor:pointer;display:block}
.ss-input{display:none}
.preview-img{max-width:100%;max-height:120px;margin-top:8px;border:1px solid var(--border);display:none}
.verify-btn{width:100%;background:linear-gradient(90deg, #005577, #00aaaa);border:1px solid var(--accent);color:#fff;padding:8px;font-family:'Orbitron',monospace;font-size:9px;letter-spacing:2px;cursor:pointer;margin-top:8px;font-weight:700;display:none}
.verify-btn:active{opacity:0.8}

/* YOUTUBE SECTION */
.yt-quest-section{margin-top:12px;border-top:1px dashed var(--border);padding-top:10px}
.yt-quest-label{font-family:'Orbitron',monospace;font-size:8px;letter-spacing:2px;color:#ff6644;margin-bottom:6px}
.yt-quest-card{background:rgba(255,40,0,0.03);border:1px solid rgba(255,68,68,0.15);padding:8px 10px;margin-bottom:6px;display:flex;align-items:center;gap:10px;cursor:pointer}
.yt-quest-emoji{font-size:18px;width:28px;height:28px;display:flex;align-items:center;justify-content:center;background:rgba(255,0,0,0.05);border:1px solid rgba(255,68,68,0.2)}
.yt-quest-title{font-size:12px;font-weight:600;color:#fff}
.yt-quest-sub{font-size:9px;color:var(--dim)}

/* NOTES STUDY MATERIAL */
.notes-section{margin-top:10px}
.notes-toggle{width:100%;background:rgba(0,212,255,0.03);border:1px solid rgba(0,212,255,0.1);color:var(--dim);padding:6px 10px;font-family:'Orbitron',monospace;font-size:8px;letter-spacing:1px;cursor:pointer;display:flex;align-items:center;justify-content:space-between}
.notes-body{display:none;padding-top:8px}
.notes-body.open{display:block}
.note-card{background:rgba(5,10,20,0.6);border-left:2px solid var(--accent);padding:8px;margin-bottom:6px;font-size:11px}
.note-title{font-weight:700;color:var(--accent);margin-bottom:2px}
.note-card code{background:rgba(0,212,255,0.08);color:var(--accent);padding:2px 4px;font-family:monospace;display:block;margin-top:4px;white-space:pre-wrap;word-break:break-all}

/* SKILLS TREE V4 */
.skill-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:8px}
.skill-card{background:var(--panel);border:1px solid var(--border);padding:12px 4px;text-align:center;cursor:pointer;transition:all 0.2s}
.skill-card.unlocked{border-color:rgba(0,212,255,0.3);box-shadow:inset 0 0 8px rgba(0,212,255,0.05)}
.skill-card.locked{opacity:0.25;cursor:not-allowed}
.skill-icon{font-size:22px;margin-bottom:4px}
.skill-name{font-size:11px;font-weight:600;color:#fff}
.skill-status{font-family:'Orbitron',monospace;font-size:7px;color:var(--dim);margin-top:2px}
.skill-card.unlocked .skill-status{color:var(--accent)}

/* AI CHAT GUIDE */
.ai-msgs{overflow-y:auto;margin-bottom:10px;max-height:calc(100dvh - 380px);padding-right:4px}
.ai-msg{padding:10px;margin-bottom:8px;font-size:12px;line-height:1.5;border-left:2px solid;animation:fadeUp 0.2s ease}
@keyframes fadeUp{from{opacity:0;transform:translateY(4px)}to{opacity:1;transform:translateY(0)}}
.ai-msg.sys{border-color:var(--accent);background:rgba(0,212,255,0.03);color:var(--accent)}
.ai-msg.usr{border-color:var(--dim);color:var(--text);background:rgba(255,255,255,0.01)}
.ai-msg.load{border-color:var(--dim);color:var(--dim);font-style:italic}
.quick-chips{display:flex;flex-wrap:wrap;gap:4px;margin-bottom:8px}
.chip{font-size:10px;padding:4px 8px;border:1px solid var(--border);color:var(--text);opacity:0.7;cursor:pointer;background:var(--panel)}
.ai-row{display:flex;gap:6px}
.ai-in{flex:1;background:var(--bg);border:1px solid var(--border);color:var(--text);padding:10px;font-family:inherit;font-size:13px}
.ai-btn{background:rgba(0,212,255,0.1);border:1px solid var(--accent);color:var(--accent);padding:0 14px;font-family:'Orbitron',monospace;font-size:9px;letter-spacing:1px;cursor:pointer}

/* STATS MATRIX BIG CARD */
.stat-big-grid{display:grid;grid-template-columns:1fr 1fr;gap:8px;margin-bottom:12px}
.stat-big-card{background:var(--panel);border:1px solid var(--border);padding:12px;text-align:center}
.sbc-label{font-size:9px;color:var(--dim);letter-spacing:1px;text-transform:uppercase}
.sbc-value{font-family:'Orbitron',monospace;font-size:20px;font-weight:700;color:var(--accent)}
.rank-journey{background:var(--panel);border:1px solid var(--border);padding:12px}
.rank-steps{display:flex;justify-content:space-between;align-items:center;margin-top:6px}
.rank-step{text-align:center;flex:1}
.rank-step-badge{font-family:'Orbitron',monospace;font-size:11px;font-weight:700;width:30px;height:30px;display:flex;align-items:center;justify-content:center;margin:0 auto;border:1px solid var(--border)}
.rank-step-badge.current{border-color:var(--gold);color:var(--gold);box-shadow:0 0 10px rgba(255,215,0,0.3)}
.rank-step-badge.done{border-color:var(--green);color:var(--green)}
.rank-connector{width:1px;height:12px;background:var(--border);margin:0 auto}

/* MODALS & NOTIFICATIONS */
.notif{position:fixed;left:50%;transform:translateX(-50%) translateY(-100px);background:var(--panel);border:1px solid var(--gold);padding:8px 16px;font-family:'Orbitron',monospace;font-size:10px;color:var(--gold);z-index:1000;transition:transform 0.4s ease;box-shadow:0 0 15px rgba(255,215,0,0.2)}
.notif.show{transform:translateX(-50%) translateY(16px)}

.lvlup{display:none;position:fixed;inset:0;background:rgba(3,5,10,0.95);z-index:500;align-items:center;justify-content:center}
.lvlup.show{display:flex}
.lvlup-box{border:2px solid var(--gold);background:var(--panel);padding:30px;text-align:center;box-shadow:0 0 30px rgba(255,215,0,0.3);max-width:280px;width:90%}
.lvlup-num{font-family:'Orbitron',monospace;font-size:48px;color:var(--gold);font-weight:900}
.lvlup-btn{margin-top:16px;background:transparent;border:1px solid var(--gold);color:var(--gold);padding:8px 16px;font-family:'Orbitron',monospace;cursor:pointer;width:100%}

/* SYSTEM MODAL FOR QUEST COMPLETE */
.qmodal{display:none;position:fixed;inset:0;background:rgba(0,0,0,0.9);z-index:600;align-items:center;justify-content:center;padding:16px}
.qmodal.show{display:flex}
.qmodal-box{background:var(--panel);border:2px solid var(--green);padding:24px;text-align:center;max-width:320px;width:100%;position:relative;box-shadow:0 0 30px rgba(0,255,136,0.2)}
.qmodal-name{font-family:'Orbitron',monospace;font-size:15px;color:#fff;margin:8px 0}
.qmodal-xp{font-family:'Orbitron',monospace;font-size:24px;color:var(--green);font-weight:900}
.qmodal-btn{width:100%;padding:10px;background:transparent;border:1px solid var(--green);color:var(--green);font-family:'Orbitron',monospace;font-size:10px;cursor:pointer;margin-top:12px}

/* CELEBRATION PARTICLES */
.particle-container { position: absolute; inset: 0; pointer-events: none; overflow: hidden; }
.p-dot { position: absolute; width: 4px; height: 4px; background: var(--accent); border-radius: 50%; animation: explode 1s ease-out forwards; }
@keyframes explode { 0% { transform: translate(0,0) scale(1); opacity: 1; } 100% { transform: translate(var(--tx), var(--ty)) scale(0); opacity: 0; } }
</style>
</head>
<body>
<div class="bg-grid"></div>

<div class="top-bar">
  <div class="top-inner">
    <div>
      <div class="sys-label">⬡ SYSTEM EVOLUTION V4</div>
      <div class="hunter-title">Full Stack <span>Hunter</span></div>
    </div>
    <div class="rank-pill" id="rank-pill">◈ RANK E ◈</div>
  </div>
</div>

<div class="voice-bar">
  <button class="voice-btn" id="voice-btn" onclick="toggleVoice()">🎙️</button>
  <div class="voice-status" id="voice-status">Tamil System Engine Ready — Tap mic to sync status</div>
  <button class="voice-lang-toggle ta" id="lang-toggle" onclick="toggleLang()">தமிழ்</button>
</div>

<div class="stats-bar">
  <div class="stat-item"><div class="stat-lbl">Level</div><div class="stat-val" id="s-lvl">01</div></div>
  <div class="stat-item"><div class="stat-lbl">XP</div><div class="stat-val" id="s-xp">0</div></div>
  <div class="stat-item"><div class="stat-lbl">Cleared</div><div class="stat-val" id="s-done">0</div></div>
</div>

<div class="xp-wrap">
  <div class="xp-labels"><span>EVOLUTION MEMORY</span><span id="xp-pct">0%</span></div>
  <div class="xp-track"><div class="xp-fill" id="xp-fill" style="width:0%"></div></div>
</div>

<div class="tabs">
  <div class="tab active" onclick="switchTab('quests',this)">QUESTS</div>
  <div class="tab" onclick="switchTab('skills',this)">SKILLS</div>
  <div class="tab" onclick="switchTab('guide',this)">SYSTEM AI</div>
  <div class="tab" onclick="switchTab('stats',this)">PROFILE</div>
</div>

<div class="content">
  <div id="tab-quests" class="tab-panel active">
    <div class="section-title">Current Objective</div>
    <div id="quest-list"></div>
  </div>

  <div id="tab-skills" class="tab-panel">
    <div class="section-title">Matrix Skill Tree</div>
    <div class="skill-grid" id="skill-grid"></div>
  </div>

  <div id="tab-guide" class="tab-panel">
    <div class="section-title">System Core AI</div>
    <div class="quick-chips" id="quick-chips"></div>
    <div class="ai-msgs" id="ai-msgs">
      <div class="ai-msg sys">[ SYSTEM ] Hunter, நான் உன்னுடைய Tamil Dev Engine. டாஸ்க்குகள் அல்லது டெக்னாலஜி டவுட்களை இங்கே கேளுங்கள்!</div>
    </div>
    <div class="ai-row">
      <input class="ai-in" id="ai-in" placeholder="Ask System... (Tamil / English)" />
      <button class="ai-btn" id="ai-btn" onclick="askAI()">SYNC</button>
    </div>
  </div>

  <div id="tab-stats" class="tab-panel">
    <div class="section-title">Hunter Diagnostics</div>
    <div class="stat-big-grid">
      <div class="stat-big-card"><div class="sbc-label">Level</div><div class="sbc-value" id="p-lvl">01</div></div>
      <div class="stat-big-card"><div class="sbc-label">Total Power XP</div><div class="sbc-value" id="p-totalxp">0</div></div>
    </div>
    <div class="rank-journey"><div class="section-title">Rank Tiers</div><div class="rank-steps" id="rank-steps"></div></div>
    <button onclick="resetConfirm()" style="width:100%;padding:10px;background:transparent;border:1px solid var(--red);color:var(--red);font-family:'Orbitron',monospace;font-size:9px;cursor:pointer;margin-top:15px">WIPE SYSTEM MEMORY (RESET)</button>
  </div>
</div>

<div class="notif" id="notif"></div>

<div class="lvlup" id="lvlup">
  <div class="lvlup-box">
    <div class="lvlup-label" style="font-family:'Orbitron'; color:var(--gold); letter-spacing:3px">⚠️ LIMIT BREAKER ACTIVATED</div>
    <div class="lvlup-num" id="lvlup-num">02</div>
    <div style="color:#fff; font-size:14px; margin:5px 0" id="lvlup-rank">RANK UP</div>
    <button class="lvlup-btn" onclick="closeLvlUp()">CONTINUE INTEGRATION →</button>
  </div>
</div>

<div class="qmodal" id="qcomplete-modal">
  <div class="qmodal-box">
    <div class="particle-container" id="particle-container"></div>
    <div style="font-size:32px; color:var(--green)">⬡</div>
    <div style="font-family:'Orbitron'; font-size:10px; color:var(--green); letter-spacing:2px">QUEST VERIFIED SUCCESSFULLY</div>
    <div class="qmodal-name" id="qc-name">Quest Name</div>
    <div class="qmodal-xp" id="qc-xp">+50 XP</div>
    <div style="font-size:12px; margin:8px 0; color:var(--text)" id="qc-msg">System has verified your screenshot data.</div>
    <button class="qmodal-btn" onclick="closeQComplete()">UNLOCK NEXT OBJECTIVE</button>
  </div>
</div>

<script>
// ── EXTENDED EXPANDED QUESTS DATA V4 ──
const QUESTS=[
  {id:1,rank:'E',name:'Python Fundamentals Core',desc:'Python இன்ஸ்டால் பண்ணி, Variables, Loops மற்றும் List Comprehension பிராக்டிஸ் பண்ணு.',xp:30,field:'Python Core',
   tasks:['Python 3 இன்ஸ்டால் செய்ய வேண்டும்','for loop பயன்படுத்தி 1 முதல் 100 வரை பிரிண்ட் செய்ய வேண்டும்','ஒரு தனிப்பயன் List comprehension கோட் எழுத வேண்டும்'],
   videos:[{emoji:'🐍',title:'Python முழு Course தமிழில்',channel:'Tamil Coding',url:'https://www.youtube.com/results?search_query=python+full+course+tamil'}],
   notes:[{title:'Variables & Loops',body:'Python-இல் variables-க்கு type தேவை இல்லை. `for i in range(1, 101): print(i)` என்பது 1-100 பிரிண்ட் செய்யும்.'}]},
  
  {id:2,rank:'E',name:'HTML & CSS Structural layout',desc:'Flexbox அல்லது Grid பயன்படுத்தி ஒரு முழு ரெஸ்பான்சிவ் லேஅவுட் உருவாக்கவும்.',xp:35,field:'Frontend',
   tasks:['Flexbox பயன்படுத்தி Navbar செய்ய வேண்டும்','Grid பயன்படுத்தி 3-Column Card Layout அமைக்க வேண்டும்','ஒரே மொபைல் வியூ ரெஸ்பான்சிவ் செக் செய்ய வேண்டும்'],
   videos:[{emoji:'🎨',title:'Flexbox & Grid தமிழ்',channel:'Code with Tamil',url:'https://www.youtube.com/results?search_query=CSS+flexbox+grid+tamil'}],
   notes:[{title:'Centering Formula',body:'`display: flex; justify-content: center; align-items: center;` எந்த எலிமெண்ட்டையும் நடுவில் கொண்டுவரும்.'}]},

  {id:3,rank:'D',name:'JavaScript Interaction Engine',desc:'JS variables, Arrow functions, மற்றும் DOM manipulation பயன்படுத்தி ஒரு dynamic ஆப் செய்.',xp:40,field:'Core',
   tasks:['addEventListener கொண்டு Click Handler உருவாக்கவும்','ஒரு Dynamic Array மேலாண்மை செய்ய வேண்டும்','DOM மூலம் UI-இல் டேட்டாவை மாற்ற வேண்டும்'],
   videos:[{emoji:'📜',title:'JS DOM Manipulation தமிழ்',channel:'Tamil Dev',url:'https://www.youtube.com/results?search_query=javascript+DOM+manipulation+tamil'}],
   notes:[{title:'DOM Logic',body:'`document.getElementById("id").textContent = "New Value"` மூலம் UI மாற்றலாம்.'}]},

  {id:4,rank:'D',name:'EcoTwin Base Simulator Architecture',desc:'ஸ்மார்ட் சிட்டி எனர்ஜி ஆப்டிமைசேஷனுக்கான அடிப்படை ஆர்க்கிடெக்சர் மற்றும் சிமுலேஷன் பிளான் ரெடி செய்.',xp:45,field:'Data Science / AI',
   tasks:['EcoTwin சிமுலேட்டருக்கான ப்ராஜெக்ட் ஸ்ட்ரக்சர் டிசைன் செய்ய வேண்டும்','பவர் கன்சம்ப்சன் கணக்கிட ஒரு கணித ஃபார்முலா லாஜிக் ரெடி செய்ய வேண்டும்','ப்ராஜெக்டின் மைல்கல் டாக்குமெண்ட் ரெடி செய்ய வேண்டும்'],
   videos:[{emoji:'🌆',title:'Smart City Simulation Ideas',channel:'Tech Tamil',url:'https://www.youtube.com/results?search_query=smart+city+simulation+data+science'}],
   notes:[{title:'Simulation Framework',body:'டேட்டா சிமுலேஷனுக்கு முதலில் மேத்தமேட்டிக்கல் மாடலிங் மற்றும் இன்புட் வேரியபிள்களை செட் செய்ய வேண்டும்.'}]},

  {id:5,rank:'C',name:'React UI Component Framework',desc:'React JS-இல் Hooks (useState, useEffect) மற்றும் Props பயன்படுத்தி மாடுலர் கூறுகளை உருவாக்கு.',xp:50,field:'Frontend',
   tasks:['useState கொண்டு State Counter உருவாக்கவும்','useEffect பயன்படுத்தி ஒரு வெளி API கால் செய்ய வேண்டும்','மாற்றக்கூடிய Props கொண்ட Card Component செய்ய வேண்டும்'],
   videos:[{emoji:'⚛️',title:'React JS தமிழில்',channel:'Tamil Dev',url:'https://www.youtube.com/results?search_query=react+js+full+course+tamil'}],
   notes:[{title:'React Lifecycle',body:'useEffect இன் இறுதியிலுள்ள `[]` டிபென்டென்சி அரே, காம்போனென்ட் லோட் ஆகும்போது ஒருமுறை மட்டுமே இயங்கும்.'}]},

  {id:6,rank:'C',name:'Node.js REST API Server',desc:'Express framework பயன்படுத்தி முழுமையான CRUD மெத்தட்கள் கொண்ட பேக்எண்ட் சர்வர் பில்ட் செய்.',xp:55,field:'Backend',
   tasks:['Express சர்வர் செட்டப் செய்ய வேண்டும் (Port 3000)','GET மற்றும் POST எண்ட்பாயிண்ட்டுகள் உருவாக்க வேண்டும்','URL Params மூலம் தரவை பில்டர் செய்ய வேண்டும்'],
   videos:[{emoji:'🔧',title:'Node.js Express API தமிழ்',channel:'Backend Tamil',url:'https://www.youtube.com/results?search_query=nodejs+express+REST+API+tamil'}],
   notes:[{title:'CRUD Protocol',body:'GET (வாசித்தல்), POST (உருவாக்குதல்), PUT (புதுப்பித்தல்), DELETE (நீக்குதல்) ஆகியவையே அடிப்படை.'}]},

  {id:7,rank:'B',name:'Secure SSH Server Configuration',desc:'பாதுகாப்பான முறையில் ரிமோட் லாகின் செய்ய SSH சர்வர் மற்றும் கீ-பேர் கான்பிகரேஷன் உருவாக்கவும்.',xp:60,field:'DevOps / Security',
   tasks:['ssh-keygen பயன்படுத்தி பொது மற்றும் தனிப்பட்ட சாவிகளை உருவாக்க வேண்டும்','சர்வரின் SSH போர்ட் மாற்றி ரூட் லாகினை டிசேபிள் செய்ய வேண்டும்','கீ ஜோடி மூலம் ரிமோட் கனெக்ஷன் வெற்றிகரமாக டெஸ்ட் செய்ய வேண்டும்'],
   videos:[{emoji:'🔐',title:'SSH Server Configurations Setup',channel:'Tamil DevOps',url:'https://www.youtube.com/results?search_query=ssh+server+setup+explained+tamil'}],
   notes:[{title:'SSH Security',body:'`/etc/ssh/sshd_config` ஃபைலில் `PermitRootLogin no` என மாற்றுவது சர்வர் பாதுகாப்பை அதிகரிக்கும்.'}]},

  {id:8,rank:'B',name:'NoSQL MongoDB Connection Database',desc:'Mongoose பயன்படுத்தி அப்ளிகேஷனை கிளவுட் அல்லது லோக்கல் டேட்டாபேஸ் உடன் இணைக்கவும்.',xp:70,field:'Backend DB',
   tasks:['MongoDB Atlas கிளவுட் அக்கவுண்ட் அல்லது லோக்கல் கம்பாஸ் செட் செய்ய வேண்டும்','ஒரு குறிப்பிட்ட தரவுக்கான Schema மற்றும் Model டிசைன் செய்ய வேண்டும்','டேட்டாபேஸில் ஒரு புதிய டாக்குமெண்ட் சேவ் செய்ய வேண்டும்'],
   videos:[{emoji:'🍃',title:'MongoDB முழு கோர்ஸ் தமிழ்',channel:'DB Tamil',url:'https://www.youtube.com/results?search_query=mongodb+tutorial+tamil'}],
   notes:[{title:'Mongoose Logic',body:'`mongoose.connect()` மூலம் எக்ஸ்பிரஸ் சர்வரை டேட்டாபேஸுடன் எளிதாக இணைக்கலாம்.'}]},

  {id:9,rank:'B',name:'Token Authentication Control (JWT)',desc:'பயனர் லாகின் மற்றும் பாதுகாப்பிற்காக JWT மற்றும் bcrypt பயன்படுத்தி பாதுகாப்பு லேயர் செய்.',xp:80,field:'Security',
   tasks:['bcryptjs கொண்டு பாஸ்வேர்டை ஹேஷ் செய்ய வேண்டும்','யூசர் லாகினுக்கு வெற்றிகரமாக JWT டோக்கன் ஜெனரேட் செய்ய வேண்டும்','டோக்கனை சரிபார்க்க ஒரு Auth Middleware எழுத வேண்டும்'],
   videos:[{emoji:'🛡️',title:'JWT Auth Tutorial Tamil',channel:'Security Tamil',url:'https://www.youtube.com/results?search_query=JWT+authentication+node+tamil'}],
   notes:[{title:'Stateless Protection',body:'JWT டோக்கன் ஹெட்டரில் `Authorization: Bearer <token>` என அனுப்பப்பட்டு வெரிஃபை செய்யப்படும்.'}]},

  {id:10,rank:'A',name:'Advanced AI Integration Layer',desc:'Gemini அல்லது Claude API-ஐ ப்ராஜெக்ட்டில் இணைத்து ஸ்மார்ட் ஃபீச்சர் ஒன்றை உருவாக்கு.',xp:100,field:'AI Logic',
   tasks:['அதிகாரப்பூர்வ SDK இன்ஸ்டால் செய்து API Key செட் செய்ய வேண்டும்','ப்ராம்ப்ட் இன்ஜினியரிங் மூலம் சிஸ்டம் ரோல் டிஃபைன் செய்ய வேண்டும்','பயனர் இன்புட்டுக்கு தகுந்த AI பதிலை UI-இல் காட்ட வேண்டும்'],
   videos:[{emoji:'🤖',title:'Gemini API Integration Tamil',channel:'AI Tamil',url:'https://www.youtube.com/results?search_query=gemini+API+integration+tamil'}],
   notes:[{title:'AI Layering',body:'`.env` ஃபைலில் மட்டுமே உங்களது ஏபிஐ சாவியை வைக்க வேண்டும், கிட்ஹப்பில் புஷ் செய்யக்கூடாது.'}]},

  {id:11,rank:'A',name:'Production Architecture Launch',desc:'முழு அப்ளிகேஷனையும் Vercel (ஃப்ரண்ட்எண்ட்) மற்றும் Railway (பேக்எண்ட்) தளங்களில் லைவ் செய்.',xp:120,field:'DevOps',
   tasks:['கிட்ஹப் ரெப்போசிட்டரி உருவாக்கி கோட் புஷ் செய்ய வேண்டும்','ப்ரொடக்ஷன் என்விரான்மென்ட் வேரியபிள்கள் செட் செய்ய வேண்டும்','வெற்றிகரமாக லைவ் லிங்க் இயங்குவதை உறுதி செய்ய வேண்டும்'],
   videos:[{emoji:'🚀',title:'Full Stack Deploy Tamil',channel:'Deploy Tamil',url:'https://www.youtube.com/results?search_query=deploy+fullstack+app+tamil'}],
   notes:[{title:'Deployment Guide',body:'பேக்எண்டில் என்விரான்மென்ட் வேரியபிள்களை ரயில்வே டேஷ்போர்டில் மேனுவலாக ஆட் செய்ய வேண்டும்.'}]},

  {id:12,rank:'S',name:'Aegis-Eye AI Final Launch',desc:'உன்னுடைய மாஸ்டர் பீஸ் AI-இன்டெக்ரேட்டட் சாஸ் (SaaS) அப்ளிகேஷனை உலகிற்கு லான்ச் செய்!',xp:200,field:'Mastery Grade',
   tasks:['முழுமையான லேண்டிங் பேஜ் மற்றும் அனிமேஷன்கள் சேர்க்கப்பட வேண்டும்','அனைத்து மாடுல்களும் எர்ரர் இல்லாமல் இயங்க வேண்டும்','லிங்க்டின் அல்லது ட்விட்டரில் பில்ட் இன் பப்ளிக் லான்ச் போஸ்ட் போட வேண்டும்'],
   videos:[{emoji:'🏆',title:'SaaS Product Build Tamil',channel:'Tamil SaaS',url:'https://www.youtube.com/results?search_query=build+saas+product+tamil'}],
   notes:[{title:'Rank S Tier achieved',body:'வாழ்த்துகள் ஹண்டர்! நீங்கள் முழுமையான Full Stack + AI டெவலப்பராக சிஸ்டம் மூலம் அங்கீகரிக்கப்படுகிறீர்கள்!'}]}
];

const SKILLS=[
  {name:'Python Basics',icon:'🐍',unlock:1},{name:'HTML/CSS Layout',icon:'⚡',unlock:2},
  {name:'JavaScript Engine',icon:'📜',unlock:3},{name:'Data Modeling',icon:'🌆',unlock:4},
  {name:'React UI Core',icon:'⚛',unlock:5},{name:'Express Backend',icon:'🔧',unlock:6},
  {name:'SSH Security',icon:'🔐',unlock:7},{name:'MongoDB Storage',icon:'🍃',unlock:8},
  {name:'JWT Guard',icon:'🛡',unlock:9},{name:'TypeScript Static',icon:'📘',unlock:9},
  {name:'Next.js Server',icon:'▲',unlock:10},{name:'Docker Container',icon:'🐳',unlock:11},
  {name:'LLM Integration',icon:'🤖',unlock:10},{name:'Prompt Mastery',icon:'✨',unlock:10},
  {name:'CI/CD Pipelines',icon:'🚀',unlock:11},{name:'SaaS Architecture',icon:'🏗',unlock:12}
];

const RANKS={1:'E',2:'E',3:'D',4:'D',5:'C',6:'C',7:'B',8:'B',9:'A',10:'A',11:'A',12:'S'};
const QUICK_Q=['Python எப்படி தொடங்குவது?','React Hooks என்றால் என்ன?','SSH சர்வர் பாதுகாப்பு எப்படி?','SaaS லான்ச் செய்ய என்ன தேவை?'];

let state={level:1,xp:0,totalXP:0,done:0,questIndex:0,checkedTasks:{}};
try{const s=localStorage.getItem('sl_v4');if(s)state=JSON.parse(s);}catch(e){}

let voiceLang='ta-IN';
let isSpeaking=false;
let synth=window.speechSynthesis;

function save(){try{localStorage.setItem('sl_v4',JSON.stringify(state));}catch(e){}}
function xpNeeded(l){return l*120}

function updateUI(){
  const max=xpNeeded(state.level);
  const pct=Math.min(100,Math.round(state.xp/max*100));
  const rank=RANKS[state.level]||'S';
  
  document.getElementById('s-lvl').textContent=String(state.level).padStart(2,'0');
  document.getElementById('s-xp').textContent=state.xp+' / '+max;
  document.getElementById('s-done').textContent=state.done;
  document.getElementById('xp-fill').style.width=pct+'%';
  document.getElementById('xp-pct').textContent=pct+'%';
  document.getElementById('rank-pill').textContent='◈ RANK '+rank+' ◈';
  
  document.getElementById('p-lvl').textContent=String(state.level).padStart(2,'0');
  document.getElementById('p-totalxp').textContent=state.totalXP||0;
}

function switchTab(name,el){
  document.querySelectorAll('.tab-panel').forEach(p=>p.classList.remove('active'));
  document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active'));
  document.getElementById('tab-'+name).classList.add('active');
  el.classList.add('active');
}

function showNotif(msg){
  const n=document.getElementById('notif');n.textContent=msg;n.classList.add('show');
  setTimeout(()=>n.classList.remove('show'),2500);
}

// ── SCREENSHOT WORKFLOW & VERIFICATION ──
function handleScreenshotSelect(event) {
  const file = event.target.files[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = function(e) {
      const preview = document.getElementById('preview-img');
      preview.src = e.target.result;
      preview.style.display = 'block';
      document.getElementById('verify-btn').style.display = 'block';
    }
    reader.readAsDataURL(file);
  }
}

function verifyTaskUpload(questId){
  const q = QUESTS.find(x => x.id === questId);
  // Verify if all checklists are checked
  let totalTasks = q.tasks.length;
  let checkedCount = 0;
  q.tasks.forEach((t, idx) => {
    if(state.checkedTasks[q.id + '-' + idx]) checkedCount++;
  });

  if(checkedCount < totalTasks) {
    showNotif('⚠️ அனைத்து டாஸ்க் செக்லிஸ்ட்களையும் டிக் செய்ய வேண்டும்!');
    speak('ஹண்டர், செக்லிஸ்டில் உள்ள அனைத்து டாஸ்க்குகளையும் முடித்த பின் ஸ்கிரீன்ஷாட் சமர்ப்பிக்கவும்.');
    return;
  }

  // Trigger effect and process success
  document.getElementById('verify-btn').innerText = '⚡ SCANNING INTEGRITY...';
  setTimeout(() => {
    document.getElementById('verify-btn').innerText = '✅ VERIFIED';
    processQuestClear(q);
  }, 1500);
}

function processQuestClear(q) {
  state.done++;
  state.questIndex++;
  state.xp += q.xp;
  state.totalXP += q.xp;

  // Level Up check
  while(state.xp >= xpNeeded(state.level) && state.level < 12){
    state.xp -= xpNeeded(state.level);
    state.level++;
    showLvlUp();
  }

  save();
  updateUI();
  triggerParticles();
  
  // Show Clear Modal
  document.getElementById('qc-name').textContent = q.name;
  document.getElementById('qc-xp').textContent = '+' + q.xp + ' POWER XP';
  document.getElementById('qcomplete-modal').style.display = 'flex';
  
  speak(`சிஸ்டம் அப்டேட். குவெஸ்ட் வெற்றிகரமாக வெரிஃபை செய்யப்பட்டது. உங்களுக்கு ${q.xp} எக்ஸ்பீரியன்ஸ் கிடைத்துள்ளது.`);
}

function closeQComplete(){
  document.getElementById('qcomplete-modal').style.display = 'none';
  renderQuests();
  renderSkills();
  renderRankJourney();
}

function toggleCheckTask(qId, taskIdx) {
  const key = qId + '-' + taskIdx;
  const cb = document.getElementById('cb-' + key);
  state.checkedTasks[key] = cb.checked;
  
  const label = document.getElementById('lbl-' + key);
  if(cb.checked) label.classList.add('checked-done');
  else label.classList.remove('checked-done');
  
  save();
}

// ── RENDER ENGINE ──
function renderQuests(){
  const container=document.getElementById('quest-list');
  container.innerHTML='';

  if(state.questIndex >= QUESTS.length){
    container.innerHTML = `<div style="text-align:center; padding:30px; color:var(--green)">
      <h3>🏆 ALL TIER OBJECTIVES CLEARED!</h3>
      <p style="font-size:13px; color:var(--text); margin-top:5px">நீங்கள் இந்த சிஸ்டத்தின் அல்டிமேட் 'S-Rank Hunter' ஆகிவிட்டீர்கள்.</p>
    </div>`;
    return;
  }

  const q = QUESTS[state.questIndex];
  const d = document.createElement('div');
  d.className = 'quest-card';

  let checklistHtml = `<div class="task-checklist-box"><div class="checklist-title">Required Tasks</div>`;
  q.tasks.forEach((t, idx) => {
    const key = q.id + '-' + idx;
    const isChecked = state.checkedTasks[key] ? 'checked' : '';
    const cls = state.checkedTasks[key] ? 'checked-done' : '';
    checklistHtml += `
      <div class="task-item">
        <input type="checkbox" id="cb-${key}" ${isChecked} onchange="toggleCheckTask(${q.id}, ${idx})">
        <label id="lbl-${key}" class="${cls}" for="cb-${key}">${t}</label>
      </div>`;
  });
  checklistHtml += `</div>`;

  let ytHtml = `<div class="yt-quest-section"><div class="yt-quest-label">▶ தமிழ் references</div>`;
  q.videos.forEach(v => {
    ytHtml += `
      <div class="yt-quest-card" onclick="window.open('${v.url}','_blank')">
        <div class="yt-quest-emoji">${v.emoji}</div>
        <div>
          <div class="yt-quest-title">${v.title}</div>
          <div class="yt-quest-sub">📺 ${v.channel}</div>
        </div>
      </div>`;
  });
  ytHtml += `</div>`;

  let notesHtml = `<div class="notes-section">
    <button class="notes-toggle" onclick="this.nextElementSibling.classList.toggle('open')"><span>⬡ STUDY RESOURCE NOTES</span> <span>▼</span></button>
    <div class="notes-body">`;
  q.notes.forEach(n => {
    notesHtml += `<div class="note-card"><div class="note-title">${n.title}</div><p>${n.body}</p></div>`;
  });
  notesHtml += `</div></div>`;

  d.innerHTML = `
    <div style="font-size:9px; color:var(--dim); font-family:'Orbitron'; letter-spacing:1px; margin-bottom:5px">OBJECTive ${state.questIndex+1} / ${QUESTS.length}</div>
    <div class="quest-header">
      <span class="rank-tag r${q.rank}">${q.rank}</span>
      <span class="quest-name">${q.name}</span>
      <span class="quest-xp">+${q.xp}XP</span>
    </div>
    <div class="quest-desc">${q.desc}</div>
    
    ${checklistHtml}

    <div class="screenshot-zone">
      <label class="ss-label" for="ss-file-${q.id}">📸 CLICK TO UPLOAD TASK PROOF (SCREENSHOT)</label>
      <input type="file" accept="image/*" class="ss-input" id="ss-file-${q.id}" onchange="handleScreenshotSelect(event)">
      <img id="preview-img" class="preview-img" src="" alt="preview">
      <button class="verify-btn" id="verify-btn" onclick="verifyTaskUpload(${q.id})">SUBMIT SCREENSHOT FOR MATRIX EVALUATION</button>
    </div>

    ${ytHtml}
    ${notesHtml}
  `;
  container.appendChild(d);
}

function renderSkills(){
  const g=document.getElementById('skill-grid'); g.innerHTML='';
  SKILLS.forEach(sk=>{
    const unlocked=state.level>=sk.unlock;
    const d=document.createElement('div');
    d.className='skill-card '+(unlocked?'unlocked':'locked');
    d.innerHTML=`<div class="skill-icon">${sk.icon}</div><div class="skill-name">${sk.name}</div><div class="skill-status">${unlocked?'UNLOCKED':'LVL '+sk.unlock}</div>`;
    if(unlocked) d.onclick=()=>{
      document.getElementById('ai-in').value=`${sk.name} பற்றி எளிய தமிழில் விளக்கு.`;
      switchTab('guide',document.querySelectorAll('.tab')[2]);
      askAI();
    };
    g.appendChild(d);
  });
}

function renderQuickChips(){
  const c=document.getElementById('quick-chips'); c.innerHTML='';
  QUICK_Q.forEach(q=>{
    const chip=document.createElement('div'); chip.className='chip'; chip.textContent=q;
    chip.onclick=()=>{document.getElementById('ai-in').value=q; askAI();};
    c.appendChild(chip);
  });
}

function renderRankJourney(){
  const c=document.getElementById('rank-steps'); c.innerHTML='';
  const ranks=['E','D','C','B','A','S'];
  const curRank=RANKS[state.level]||'S';
  const ri=ranks.indexOf(curRank);
  ranks.forEach((r,i)=>{
    const done=i<ri; const cur=i===ri;
    const d=document.createElement('div'); d.className='rank-step';
    d.innerHTML=`<div class="rank-step-badge ${cur?'current':done?'done':'future'}">${r}</div>`;
    c.appendChild(d);
  });
}

// ── AUDIO ENGINE ──
function speak(t){
  if(!synth)return; synth.cancel();
  const u=new SpeechSynthesisUtterance(t);
  u.lang=voiceLang; u.rate=0.95;
  const voices=synth.getVoices();
  const ta=voices.find(v=>v.lang.startsWith('ta'))||voices.find(v=>v.lang.startsWith('en-IN'));
  if(ta)u.voice=ta;
  isSpeaking=true;
  document.getElementById('voice-btn').classList.add('speaking');
  u.onend=()=>{
    isSpeaking=false;
    document.getElementById('voice-btn').classList.remove('speaking');
  };
  synth.speak(u);
}

function toggleLang(){
  voiceLang=voiceLang==='ta-IN'?'en-US':'ta-IN';
  document.getElementById('lang-toggle').textContent=voiceLang==='ta-IN'?'தமிழ்':'EN';
  speak(voiceLang==='ta-IN'?'தமிழ் சிஸ்டம் ரெடி.':'English voice interface synchronized.');
}

function toggleVoice(){
  if(state.questIndex >= QUESTS.length){
    speak("அனைத்து குறிக்கோள்களும் முடிந்தது. நீங்கள் மாஸ்டர் எச்-ரங்க் ஹண்டர்.");
    return;
  }
  const q = QUESTS[state.questIndex];
  speak(`உங்களது தற்போதைய டாஸ்க் ${q.name}. குறிக்கோள்களை முடித்து ஸ்கிரீன்ஷாட் அப்லோட் செய்யவும்.`);
}

// ── EFFECTS ──
function triggerParticles(){
  const box = document.getElementById('particle-container'); box.innerHTML='';
  for(let i=0; i<30; i++){
    const p = document.createElement('div'); p.className='p-dot';
    const angle = Math.random()*360; const dist = 50 + Math.random()*100;
    p.style.setProperty('--tx', Math.cos(angle)*dist+'px');
    p.style.setProperty('--ty', Math.sin(angle)*dist+'px');
    p.style.left = '50%'; p.style.top = '50%';
    p.style.animationDelay = Math.random()*0.2+'s';
    box.appendChild(p);
  }
}

function showLvlUp(){
  document.getElementById('lvlup-num').textContent=String(state.level).padStart(2,'0');
  document.getElementById('lvlup-rank').textContent='RANK ' + (RANKS[state.level]||'S') + ' REACHED';
  document.getElementById('lvlup').classList.add('show');
  speak(`லெவல் அப்! உங்கள் ஆற்றல் அதிகரித்துள்ளது ஹண்டர்.`);
}
function closeLvlUp(){ document.getElementById('lvlup').classList.remove('show'); }

// ── MOCK AI GUIDE ANSWER ──
function askAI(){
  const inp=document.getElementById('ai-in'); const msg=inp.value.trim(); if(!msg)return;
  const msgs=document.getElementById('ai-msgs');
  
  const ud=document.createElement('div'); ud.className='ai-msg usr'; ud.textContent='> '+msg; msgs.appendChild(ud);
  const ld=document.createElement('div'); ld.className='ai-msg load'; ld.textContent='[ SYSTEM ] Analyzing question...'; msgs.appendChild(ld);
  msgs.scrollTop=msgs.scrollHeight; inp.value='';

  setTimeout(() => {
    ld.className='ai-msg sys';
    let reply = `[ SYSTEM ] ஹண்டர், நீங்கள் கேட்ட கேள்விக்கான விடை: இதைப் பற்றி முழுமையாகப் படிக்க சிஸ்டத்தில் உள்ள குறிப்புகளைப் பாருங்கள் அல்லது அப்லோடு செக்ஷனைப் பயன்படுத்தவும். தொடர்ந்து கோட் செய்ய உங்களை சிஸ்டம் அறிவுறுத்துகிறது!`;
    ld.textContent = reply;
    speak(reply.replace('[ SYSTEM ]', ''));
    msgs.scrollTop=msgs.scrollHeight;
  }, 1000);
}

function resetConfirm(){
  if(confirm('முழு மெமரியையும் அழிக்க வேண்டுமா? இந்த ஆக்ஷனை மாற்ற முடியாது!')){
    state={level:1,xp:0,totalXP:0,done:0,questIndex:0,checkedTasks:{}};
    save(); updateUI(); renderQuests(); renderSkills(); renderRankJourney();
    showNotif('⬡ SYSTEM REBOOTED');
  }
}

// INIT
updateUI(); renderQuests(); renderSkills(); renderQuickChips(); renderRankJourney();
</script>
</body>
</html>
