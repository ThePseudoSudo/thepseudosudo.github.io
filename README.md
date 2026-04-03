<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no"/>
<title>Kana Master</title>
<style>
  @import url('https://na01.safelinks.protection.outlook.com/?url=https%3A%2F%2Ffonts.googleapis.com%2Fcss2%3Ffamily%3DAlex%2BBrush%26family%3DNoto%2BSerif%2BJP%3Awght%40400%3B700%26family%3DDM%2BMono%3Awght%40400%3B500%26family%3DVT323%26display%3Dswap&data=05%7C02%7C%7C970a4812e91344362f2e08de91218793%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C639107770358869518%7CUnknown%7CTWFpbGZsb3d8eyJFbXB0eU1hcGkiOnRydWUsIlYiOiIwLjAuMDAwMCIsIlAiOiJXaW4zMiIsIkFOIjoiTWFpbCIsIldUIjoyfQ%3D%3D%7C0%7C%7C%7C&sdata=SAnRN0FFmf%2F%2BJr9x1QUWjb8Zd4zVwZFQi7gHv1%2BWnt8%3D&reserved=0');

  :root {
    --ink: #1a1a2e; --paper: #f5f0e8; --accent: #c0392b; --dim: #e5ddd0; --card-bg: #faf7f2; --border: rgba(26, 26, 46, 0.2); --title-font: 'Alex Brush', cursive; --ornament: transparent;
  }

  [data-theme="regal"] { --ink: #ffd700; --paper: #7c0a02; --accent: #ffd700; --dim: #5a0000; --card-bg: #8b0000; --border: rgba(255, 215, 0, 0.3); --ornament: #ffd700; }
  [data-theme="sakura"] { --ink: #3e2723; --paper: #fce4ec; --accent: #d81b60; --dim: #f1d1d9; --card-bg: #f8bbd0; --border: rgba(62, 39, 35, 0.2); }
  [data-theme="hacker"] { --ink: #00ff41; --paper: #000000; --accent: #00ff41; --dim: #002200; --card-bg: #0a0a0a; --border: rgba(0, 255, 65, 0.3); --title-font: 'VT323', monospace; }

  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: 'DM Mono', monospace; background: var(--paper); color: var(--ink); min-height: 100vh; transition: 0.3s; overflow-x: hidden; }
 
  .ornament { display: none; position: absolute; width: 60px; height: 60px; border: 1px solid var(--ornament); pointer-events: none; opacity: 0.4; z-index: 1; }
  [data-theme="regal"] .ornament { display: block; }
  .tl { top: 15px; left: 15px; border-right: none; border-bottom: none; }
  .tr { top: 15px; right: 15px; border-left: none; border-bottom: none; }

  .petal { position: fixed; top: -10%; background: #ffb7c5; border-radius: 10px 2px 10px 1px; pointer-events: none; z-index: 1; display: none; animation: fall linear infinite; }
  [data-theme="sakura"] .petal { display: block; }
  @keyframes fall { 0% { transform: translateY(0) rotate(0deg); opacity: 1; } 100% { transform: translateY(110vh) rotate(360deg); opacity: 0; } }

  .screen { display: none; min-height: 100vh; flex-direction: column; position: relative; z-index: 2; }
  .screen.active { display: flex; }

  #home { padding: 100px 20px 40px; }
  .theme-toggle { position: absolute; top: 20px; right: 20px; background: var(--card-bg); border: 1px solid var(--border); color: var(--ink); padding: 6px 12px; border-radius: 8px; cursor: pointer; font-size: 0.7rem; font-weight: bold; z-index: 20; }
 
  .home-header { text-align: center; margin-bottom: 30px; }
  .home-header h1 { font-family: var(--title-font); font-size: 4rem; line-height: 1; }

  .sets-panel { flex: 1; overflow-y: auto; padding: 0 10px 20px; }
  .group-label { font-size: 0.65rem; letter-spacing: 2px; color: var(--ink); opacity: 0.5; text-transform: uppercase; margin: 25px 0 10px; font-weight: bold; text-align: center; }
 
  .row-item { display: flex; align-items: center; justify-content: space-between; padding: 14px; background: var(--card-bg); border: 1px solid var(--border); border-radius: 12px; margin-bottom: 8px; cursor: pointer; transition: 0.2s; }
  .row-item.selected { border-color: var(--ink); background: rgba(255, 255, 255, 0.05); }
  .set-kana { font-family: 'Noto Serif JP', serif; font-size: 1.4rem; width: 35px; }
 
  .check-circle { width: 22px; height: 22px; border-radius: 50%; border: 1px solid var(--border); display: flex; align-items: center; justify-content: center; font-size: 0.7rem; color: transparent; font-weight: bold; }
  .selected .check-circle { background: var(--ink); color: var(--paper); border-color: var(--ink); }
 
  .toggle-pill { width: 42px; height: 22px; border-radius: 12px; background: var(--dim); border: 1px solid var(--border); position: relative; transition: 0.3s; }
  .toggle-pill.on { background: var(--ink); border-color: var(--ink); }
  .toggle-pill::after { content: ''; position: absolute; top: 3px; left: 3px; width: 14px; height: 14px; border-radius: 50%; background: var(--paper); transition: 0.3s; }
  .toggle-pill.on::after { transform: translateX(20px); }

  .card-area { flex: 1; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 20px; }
  .flashcard { width: 100%; max-width: 300px; aspect-ratio: 3/2; perspective: 1000px; cursor: pointer; }
  .card-inner { width: 100%; height: 100%; position: relative; transform-style: preserve-3d; transition: transform 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
  .flipped .card-inner { transform: rotateY(180deg); }
  .no-anim .card-inner { transition: none !important; }

  .card-face { position: absolute; inset: 0; display: flex; align-items: center; justify-content: center; background: var(--card-bg); border: 1px solid var(--border); border-radius: 20px; backface-visibility: hidden; }
  .card-back { transform: rotateY(180deg); }
  .card-kana-lg { font-family: 'Noto Serif JP', serif; font-size: 5rem; color: var(--ink); }
  .card-romaji-lg { font-size: 2.8rem; font-weight: 500; color: var(--ink); }

  .btn-wrap { visibility: hidden; opacity: 0; display: flex; width: 100%; max-width: 300px; gap: 10px; margin-top: 25px; transition: 0.3s; }
  .btn-wrap.visible { visibility: visible; opacity: 1; }
  .btn { flex: 1; padding: 18px; border: 1px solid var(--border); border-radius: 12px; font-family: 'DM Mono'; font-weight: bold; cursor: pointer; background: var(--card-bg); color: var(--ink); }
  .btn-primary { background: var(--ink); color: var(--paper); border: 1px solid var(--ink); }
  .start-btn { width: 100%; padding: 20px; background: var(--ink); color: var(--paper); border: 1px solid var(--ink); border-radius: 12px; font-family: 'DM Mono'; font-weight: bold; font-size: 1rem; margin-top: 10px; cursor: pointer; }
  .start-btn:disabled { opacity: 0.2; cursor: not-allowed; }
</style>
</head>
<body>

<div id="sakura-container"></div>

<div id="home" class="screen active">
  <div class="ornament tl"></div><div class="ornament tr"></div>
  <button class="theme-toggle" onclick="cycleTheme()">🎨 THEME</button>
  <div class="home-header"><h1>Kana Master</h1></div>

  <div class="sets-panel">
    <div class="group-label">Modifiers</div>
    <div class="row-item" onclick="toggleOption('dakuten')"><span>Dakuten</span><div class="toggle-pill" id="t-dakuten"></div></div>
    <div class="row-item" onclick="toggleOption('handakuten')"><span>Handakuten</span><div class="toggle-pill" id="t-handakuten"></div></div>
    <div class="row-item" onclick="toggleOption('combos')"><span>Combinations</span><div class="toggle-pill" id="t-combos"></div></div>

    <div class="group-label">Hiragana</div>
    <div id="hira-list"></div>
    <div class="group-label">Katakana</div>
    <div id="kata-list"></div>
  </div>

  <button class="start-btn" onclick="startStudy()" id="start-btn">BEGIN (0)</button>
</div>

<div id="study" class="screen">
  <div style="display:flex; justify-content:space-between; padding:20px; font-weight:bold; font-size:0.75rem;">
    <div onclick="goHome()" style="cursor:pointer">← EXIT</div>
    <div id="mute-btn" onclick="toggleMute()" style="cursor:pointer">VOICE ON</div>
    <div id="prog-text">0 Left</div>
  </div>
  <div class="card-area">
    <div class="flashcard" id="fcard" onclick="flip()">
      <div class="card-inner">
        <div class="card-face"><div class="card-kana-lg" id="q-kana"></div></div>
        <div class="card-face card-back"><div class="card-romaji-lg" id="a-roma"></div></div>
      </div>
    </div>
    <div class="btn-wrap" id="b-wrap">
      <button class="btn" onclick="act('miss')">MISSED</button>
      <button class="btn btn-primary" onclick="act('next')">GOT IT</button>
    </div>
  </div>
</div>

<div id="results" class="screen" style="align-items:center; justify-content:center; text-align:center;">
  <h2 style="font-family: var(--title-font); font-size: 4rem; margin-bottom: 20px;">Complete</h2>
  <button class="start-btn" style="width:200px" onclick="goHome()">AGAIN</button>
</div>

<script>
const DATA = {
  h_v: { n:"Vowels", k:"あ", type:"h", cards:[{k:"あ",r:"a"},{k:"い",r:"i"},{k:"う",r:"u"},{k:"え",r:"e"},{k:"お",r:"o"}] },
  h_k: { n:"K-row", k:"か", type:"h", cards:[{k:"か",r:"ka"},{k:"き",r:"ki"},{k:"く",r:"ku"},{k:"け",r:"ke"},{k:"こ",r:"ko"}], d:[{k:"が",r:"ga"},{k:"ぎ",r:"gi"},{k:"ぐ",r:"gu"},{k:"げ",r:"ge"},{k:"ご",r:"go"}], c:[{k:"きゃ",r:"kya"},{k:"きゅ",r:"kyu"},{k:"きょ",r:"kyo"}], dc:[{k:"ぎゃ",r:"gya"},{k:"ぎゅ",r:"gyu"},{k:"ぎょ",r:"gyo"}] },
  h_s: { n:"S-row", k:"さ", type:"h", cards:[{k:"さ",r:"sa"},{k:"し",r:"shi"},{k:"す",r:"su"},{k:"せ",r:"se"},{k:"そ",r:"so"}], d:[{k:"ざ",r:"za"},{k:"じ",r:"ji"},{k:"ず",r:"zu"},{k:"ぜ",r:"ze"},{k:"ぞ",r:"zo"}], c:[{k:"しゃ",r:"sha"},{k:"しゅ",r:"shu"},{k:"しょ",r:"sho"}], dc:[{k:"じゃ",r:"ja"},{k:"じゅ",r:"ju"},{k:"じょ",r:"jo"}] },
  h_t: { n:"T-row", k:"た", type:"h", cards:[{k:"た",r:"ta"},{k:"ち",r:"chi"},{k:"つ",r:"tsu"},{k:"て",r:"te"},{k:"と",r:"to"}], d:[{k:"だ",r:"da"},{k:"ぢ",r:"ji"},{k:"づ",r:"zu"},{k:"で",r:"de"},{k:"ど",r:"do"}], c:[{k:"ちゃ",r:"cha"},{k:"ちゅ",r:"chu"},{k:"ちょ",r:"cho"}] },
  h_n: { n:"N-row", k:"な", type:"h", cards:[{k:"な",r:"na"},{k:"に",r:"ni"},{k:"ぬ",r:"nu"},{k:"ね",r:"ne"},{k:"の",r:"no"}], c:[{k:"にゃ",r:"nya"},{k:"にゅ",r:"nyu"},{k:"にょ",r:"nyo"}] },
  h_h: { n:"H-row", k:"は", type:"h", cards:[{k:"は",r:"ha"},{k:"ひ",r:"hi"},{k:"ふ",r:"fu"},{k:"へ",r:"he"},{k:"ほ",r:"ho"}], d:[{k:"ば",r:"ba"},{k:"び",r:"bi"},{k:"ぶ",r:"bu"},{k:"べ",r:"be"},{k:"ぼ",r:"bo"}], p:[{k:"ぱ",r:"pa"},{k:"ぴ",r:"pi"},{k:"ぷ",r:"pu"},{k:"ぺ",r:"pe"},{k:"ぽ",r:"po"}], c:[{k:"ひゃ",r:"hya"},{k:"ひゅ",r:"hyu"},{k:"ひょ",r:"hyo"}], dc:[{k:"びゃ",r:"bya"},{k:"びゅ",r:"byu"},{k:"びょ",r:"byo"}], pc:[{k:"ぴゃ",r:"pya"},{k:"ぴゅ",r:"pyu"},{k:"ぴょ",r:"pya"}] },
  h_m: { n:"M-row", k:"ま", type:"h", cards:[{k:"ま",r:"ma"},{k:"み",r:"mi"},{k:"む",r:"mu"},{k:"め",r:"me"},{k:"も",r:"mo"}], c:[{k:"みゃ",r:"mya"},{k:"みゅ",r:"myu"},{k:"みょ",r:"myo"}] },
  h_y: { n:"Y-row", k:"や", type:"h", cards:[{k:"や",r:"ya"},{k:"ゆ",r:"yu"},{k:"よ",r:"yo"}] },
  h_r: { n:"R-row", k:"ら", type:"h", cards:[{k:"ら",r:"ra"},{k:"り",r:"ri"},{k:"る",r:"ru"},{k:"れ",r:"re"},{k:"ろ",r:"ro"}], c:[{k:"りゃ",r:"rya"},{k:"りゅ",r:"ryu"},{k:"りょ",r:"ryo"}] },
  h_w: { n:"W-row", k:"わ", type:"h", cards:[{k:"わ",r:"wa"},{k:"を",r:"wo"},{k:"ん",r:"n"}] },
  k_v: { n:"Vowels", k:"ア", type:"k", cards:[{k:"ア",r:"a"},{k:"イ",r:"i"},{k:"ウ",r:"u"},{k:"エ",r:"e"},{k:"オ",r:"o"}] },
  k_k: { n:"K-row", k:"カ", type:"k", cards:[{k:"カ",r:"ka"},{k:"キ",r:"ki"},{k:"ク",r:"ku"},{k:"ケ",r:"ke"},{k:"コ",r:"ko"}], d:[{k:"ガ",r:"ga"},{k:"ギ",r:"gi"},{k:"グ",r:"gu"},{k:"ゲ",r:"ge"},{k:"ゴ",r:"go"}], c:[{k:"キャ",r:"kya"},{k:"キュ",r:"kyu"},{k:"キョ",r:"kyo"}], dc:[{k:"ギャ",r:"gya"},{k:"ギュ",r:"gyu"},{k:"ギョ",r:"gyo"}] },
  k_s: { n:"S-row", k:"サ", type:"k", cards:[{k:"サ",r:"sa"},{k:"シ",r:"shi"},{k:"ス",r:"su"},{k:"セ",r:"se"},{k:"ソ",r:"so"}], d:[{k:"ザ",r:"za"},{k:"ジ",r:"ji"},{k:"ズ",r:"zu"},{k:"ぜ",r:"ze"},{k:"ぞ",r:"zo"}], c:[{k:"シャ",r:"sha"},{k:"シュ",r:"shu"},{k:"ショ",r:"sho"}], dc:[{k:"ジャ",r:"ja"},{k:"ジュ",r:"ju"},{k:"ジョ",r:"jo"}] },
  k_t: { n:"T-row", k:"タ", type:"k", cards:[{k:"タ",r:"ta"},{k:"チ",r:"chi"},{k:"ツ",r:"tsu"},{k:"テ",r:"te"},{k:"ト",r:"to"}], d:[{k:"ダ",r:"da"},{k:"ヂ",r:"ji"},{k:"ヅ",r:"zu"},{k:"デ",r:"de"},{k:"ド",r:"do"}], c:[{k:"チャ",r:"cha"},{k:"チュ",r:"chu"},{k:"チョ",r:"cho"}] },
  k_n: { n:"N-row", k:"ナ", type:"k", cards:[{k:"ナ",r:"na"},{k:"ニ",r:"ni"},{k:"ヌ",r:"nu"},{k:"ネ",r:"ne"},{k:"ノ",r:"no"}], c:[{k:"ニャ",r:"nya"},{k:"ニュ",r:"nyu"},{k:"ニョ",r:"nyo"}] },
  k_h: { n:"H-row", k:"ハ", type:"k", cards:[{k:"ハ",r:"ha"},{k:"ヒ",r:"hi"},{k:"フ",r:"fu"},{k:"へ",r:"he"},{k:"ホ",r:"ho"}], d:[{k:"バ",r:"ba"},{k:"ビ",r:"bi"},{k:"ブ",r:"bu"},{k:"ベ",r:"be"},{k:"ボ",r:"bo"}], p:[{k:"パ",r:"pa"},{k:"ピ",r:"pi"},{k:"プ",r:"pu"},{k:"ペ",r:"pe"},{k:"ポ",r:"po"}], c:[{k:"ヒャ",r:"hya"},{k:"ヒュ",r:"hyu"},{k:"ヒョ",r:"hyo"}], dc:[{k:"ビャ",r:"bya"},{k:"ビュ",r:"byu"},{k:"ビョ",r:"byo"}], pc:[{k:"ピャ",r:"pya"},{k:"ピュ",r:"pyu"},{k:"ピょ",r:"pya"}] },
  k_m: { n:"M-row", k:"マ", type:"k", cards:[{k:"マ",r:"ma"},{k:"ミ",r:"mi"},{k:"ム",r:"mu"},{k:"メ",r:"me"},{k:"モ",r:"mo"}], c:[{k:"ミャ",r:"mya"},{k:"ミュ",r:"myu"},{k:"ミョ",r:"myo"}] },
  k_y: { n:"Y-row", k:"ヤ", type:"k", cards:[{k:"ヤ",r:"ya"},{k:"ユ",r:"yu"},{k:"ヨ",r:"yo"}] },
  k_r: { n:"R-row", k:"ラ", type:"k", cards:[{k:"ラ",r:"ra"},{k:"リ",r:"ri"},{k:"ル",r:"ru"},{k:"レ",r:"re"},{k:"ロ",r:"ro"}], c:[{k:"リャ",r:"rya"},{k:"リュ",r:"ryu"},{k:"リョ",r:"ryo"}] },
  k_w: { n:"W-row", k:"ワ", type:"k", cards:[{k:"ワ",r:"wa"},{k:"ヲ",r:"wo"},{k:"ン",r:"n"}] }
};

let selected = new Set();
let opts = { dakuten: false, handakuten: false, combos: false };
let themeIdx = 0;
const themes = ["standard", "regal", "sakura", "hacker"];
let queue = [];
let isMuted = false;
let audioTimeout = null;

function speak(t) {
  if (isMuted) return;
  window.speechSynthesis.cancel();
  if(audioTimeout) clearTimeout(audioTimeout);
  audioTimeout = setTimeout(() => {
    const m = new SpeechSynthesisUtterance(t);
    m.lang = 'ja-JP';
    m.rate = 0.25;
    window.speechSynthesis.speak(m);
  }, 500);
}

function init() {
  const hl = document.getElementById('hira-list');
  const kl = document.getElementById('kata-list');
  hl.innerHTML = ''; kl.innerHTML = '';
  Object.keys(DATA).forEach(id => {
    const s = DATA[id];
    const d = document.createElement('div');
    d.className = 'row-item' + (selected.has(id) ? ' selected' : '');
    d.innerHTML = `<div style="display:flex;align-items:center"><div class="set-kana">${s.k}</div><span style="font-size:0.85rem">${s.n}</span></div><div class="check-circle">✓</div>`;
    d.onclick = () => { selected.has(id)?selected.delete(id):selected.add(id); init(); };
    (s.type==='h'?hl:kl).appendChild(d);
  });
  update();
}

function update() {
  const count = build().length;
  const b = document.getElementById('start-btn');
  b.textContent = (themes[themeIdx]==='hacker'?'INITIALIZE':'BEGIN') + ` (${count})`;
  b.disabled = count === 0;
}

function build() {
  let d = [];
  selected.forEach(id => {
    const s = DATA[id];
    d.push(...s.cards);
    if(opts.dakuten && s.d) d.push(...s.d);
    if(opts.handakuten && s.p) d.push(...s.p);
    if(opts.combos && s.c) d.push(...s.c);
    if(opts.combos && opts.dakuten && s.dc) d.push(...s.dc);
    if(opts.combos && opts.handakuten && s.pc) d.push(...s.pc);
  });
  return d;
}

function startStudy() {
  window.speechSynthesis.speak(new SpeechSynthesisUtterance(''));
  queue = build().sort(()=>Math.random()-0.5);
  show('study'); next();
}

function next() {
  if (!queue.length) { show('results'); return; }
  const f = document.getElementById('fcard');
  f.classList.add('no-anim');
  f.classList.remove('flipped');
  document.getElementById('b-wrap').classList.remove('visible');
  document.getElementById('q-kana').textContent = queue[0].k;
  document.getElementById('a-roma').textContent = queue[0].r;
  document.getElementById('prog-text').textContent = `${queue.length} Left`;
  setTimeout(() => f.classList.remove('no-anim'), 50);
}

function flip() {
  const c = document.getElementById('fcard');
  const isFlipped = c.classList.contains('flipped');
 
  if (isFlipped) {
    c.classList.remove('flipped');
  } else {
    c.classList.add('flipped');
    document.getElementById('b-wrap').classList.add('visible');
    speak(queue[0].k);
  }
}

function act(type) {
  if (type==='next') queue.shift();
  else queue.push(queue.shift());
  next();
}

function cycleTheme() {
  themeIdx = (themeIdx + 1) % themes.length;
  document.body.setAttribute('data-theme', themes[themeIdx]);
  if(themes[themeIdx]==='sakura') {
    const c = document.getElementById('sakura-container');
    c.innerHTML = '';
    for(let i=0;i<12;i++) {
      const p = document.createElement('div'); p.className='petal';
      p.style.left = Math.random()*100+'vw'; p.style.animationDuration=(Math.random()*3+3)+'s';
      p.style.width = p.style.height = (Math.random()*8+6)+'px';
      c.appendChild(p);
    }
  }
  update();
}

function toggleOption(o) { opts[o] = !opts[o]; document.getElementById('t-'+o).classList.toggle('on'); update(); }
function toggleMute() { isMuted = !isMuted; document.getElementById('mute-btn').textContent = isMuted?'VOICE OFF':'VOICE ON'; }
function show(id) { document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active')); document.getElementById(id).classList.add('active'); }
function goHome() { show('home'); init(); }

init();
</script>
</body>
</html>
