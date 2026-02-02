<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>RPGクリッカー：完全版</title>


<style>
* { touch-action: manipulation; user-select: none; }

body {
  text-align: center;
  font-family: sans-serif;
  background-color: #121212;
  color: #e0e0e0;
  padding: 20px;
}

.header {
  display: flex;
  justify-content: space-around;
  background: #1e1e1e;
  padding: 15px;
  border-radius: 15px;
  margin-bottom: 20px;
}

.stat-box { display: flex; flex-direction: column; }
.xp-value { font-size: 24px; color: #3498db; font-weight: bold; }
.money-value { font-size: 24px; color: #f1c40f; font-weight: bold; }

#xp-bar-container {
  width: 200px; height: 10px;
  background: #333; border-radius: 5px;
  margin: 5px auto;
}
#xp-bar { height: 100%; background: #3498db; transition: 0.3s; }

.main-btn {
  position: relative;
  width: 170px; height: 170px;
  border-radius: 50%;
  border: none;
  background: #2980b9;
  color: white;
  font-size: 20px;
  margin: 15px;
  cursor: pointer;
  overflow: hidden;
}

.ripple {
  position: absolute;
  border-radius: 50%;
  background: rgba(255,255,255,0.4);
  transform: scale(0);
  animation: ripple 0.6s linear;
  pointer-events: none;
}
@keyframes ripple {
  to { transform: scale(4); opacity: 0; }
}

.gain-text {
  position: absolute;
  color: #f1c40f;
  font-weight: bold;
  pointer-events: none;
  animation: floatUp 1s ease-out forwards;
}
@keyframes floatUp {
  from { opacity: 1; transform: translate(-50%,0); }
  to { opacity: 0; transform: translate(-50%,-40px); }
}

#buff-container {
  max-width: 300px;
  margin: 10px auto;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.buff {
  background: #1e1e1e;
  border: 1px solid #555;
  border-radius: 8px;
  padding: 8px;
  display: flex;
  justify-content: space-between;
}

.shop {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 15px;
  max-width: 800px;
  margin: 20px auto;
}

.item { background:#1e1e1e; padding:15px; border-radius:12px; display:none; }
.item.unlocked { display:block; }
.item button { margin-top:8px; cursor:pointer; padding:5px 10px; background:#2980b9; color:white; border:none; border-radius:5px; }

.item .desc {
  margin-top:8px;
  font-size:13px;
  color:#bdbdbd;
  line-height:1.3;
}

#boss-area {
  display:none;
  background:#1e1e1e;
  padding:15px;
  border-radius:15px;
  margin:15px auto;
  max-width:300px;
}

.toast {
  position: fixed;
  left: 50%;
  bottom: 20px;
  transform: translateX(-50%);
  background: #222;
  color: #fff;
  padding: 8px 12px;
  border-radius: 8px;
  border: 1px solid #444;
  z-index: 9999;
  opacity: 0;
  transition: opacity 0.3s ease;
}

button.shop-btn {
  margin-top:10px;
  padding:10px 20px;
  background:#2980b9;
  color:white;
  border:none;
  border-radius:5px;
  cursor:pointer;
  font-size:25px;
}

.screen {
  display: none;
}

.screen.active {
  display: block;
}

#menu-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: #1e1e1e;
  display: flex;
  justify-content: space-around;
  padding: 10px 0;
  border-top: 1px solid #333;
}

#menu-bar button {
  background: none;
  border: none;
  color: #e0e0e0;
  font-size: 22px;
}

#gacha-result {
  margin-top: 8px;
  font-size: 14px;
  font-weight: bold;
  color: #f1c40f;
}

/* ===== ガチャ演出 ===== */
#gacha-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.85);
  display: none;
  justify-content: center;
  align-items: center;
  z-index: 10000;
}

#gacha-box {
  width: 260px;
  height: 260px;
  background: #222;
  border-radius: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 22px;
  font-weight: bold;
  color: #fff;
  animation: gacha-spin 1s linear infinite;
}

@keyframes gacha-spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.gacha-rare {
  background: radial-gradient(circle, #3498db, #1e1e1e);
  box-shadow: 0 0 20px #3498db;
}

.gacha-super {
  background: radial-gradient(circle, #f1c40f, #1e1e1e);
  box-shadow: 0 0 30px #f1c40f;
  animation: flash 0.5s infinite alternate;
}

.gacha-ultra {
  background: linear-gradient(45deg,
    red, orange, yellow, green, cyan, blue, violet);
  color: black;
  box-shadow: 0 0 40px white;
  animation: ultra 0.2s infinite;
}

@keyframes flash {
  from { opacity: 0.7; }
  to { opacity: 1; }
}

@keyframes ultra {
  from { transform: scale(1) rotate(-2deg); }
  to { transform: scale(1.05) rotate(2deg); }
}


</style>
</head>

<body>

<div class="header">
  <div class="stat-box">
    <span>LEVEL</span>
    <span class="xp-value">Lv.<span id="level">1</span></span>
    <div id="xp-bar-container"><div id="xp-bar"></div></div>
  </div>
  <div class="stat-box">
    <span>MONEY</span>
    <span class="money-value"><span id="pts">0</span> G</span>
  </div>
  
</div>

<div id="multiplier-top" style="margin:0 auto 12px; font-size:18px; font-weight:bold; color:#f1c40f;">倍率 ×<span id="rebirth-multiplier-top">1.0</span></div>
<button class="main-btn" onclick="performClick(event)">CLICK!</button>
<div id="buff-container"></div>

<div style="display:flex; gap:10px; justify-content:center; flex-wrap:wrap; margin:10px;">
  <button class="shop-btn" id="goblin-boss-btn" onclick="startBoss('goblin')">⚔️ ゴブリン王に挑む</button>
  <button class="shop-btn" id="dragon-boss-btn" onclick="startBoss('dragon')" style="display:none;">🐉 炎竜に挑む</button>
</div>

<div id="boss-area">
  <h3 id="boss-name">ゴブリン王</h3>
  <div id="boss-rank"></div>
  <div id="boss-hp-text"></div>
  <div style="background:#333;height:10px;border-radius:5px;">
    <div id="boss-hp-bar" style="height:100%;background:#e74c3c;"></div>
  </div>
  <div id="boss-timer"></div>
  <button class="main-btn" style="background:#c0392b" onclick="attackBoss(event)">ATTACK!</button>
</div>

<div class="shop">
  <div class="item unlocked" id="item1">
    木刀
    <button onclick="buyItem(1,50)">50G</button>
    <div class="desc">攻撃力 +1。序盤の基本武器。</div>
  </div>

  <div class="item" id="item2">
    見習い召喚師
    <button onclick="buyItem(2,200)">200G</button>
    <div class="desc">毎秒 +1G 自動生成。Lv3で解禁。</div>
  </div>

  <div class="item" id="item3">
    銀の剣
    <button onclick="buyItem(3,800)">800G</button>
    <div class="desc">攻撃力 +3。中級武器。Lv5で解禁。</div>
  </div>

  <div class="item" id="item4">
    ドラゴンの巣
    <button onclick="buyItem(4,5000)">5000G</button>
    <div class="desc">自動生成 +5G。Lv10で解禁。</div>
  </div>

  <div class="item" id="item5">
    Wブースト
    <button onclick="buyItem(5,7500)">7500G</button>
    <div class="desc">10秒間ダブルブースト。</div>
  </div>

  <div class="item" id="item6">
    両刃斧
    <button onclick="buyItem(6,2500)">2500G</button>
    <div class="desc">攻撃力 +10。強力な武器。Lv12で解禁。</div>
  </div>

  <div class="item" id="item7">
    リボーン
    <button onclick="buyItem(7,3000000)">3000000G</button>
    <div class="desc">倍率 +50%。レベルとGをリセット。Lv20で解禁。</div>
  </div>
</div>



<div class="item unlocked">
  🎰 武器ガチャ
  <button onclick="drawGacha()">5000G</button>
  <div class="desc">
    ランダムで攻撃力が上昇するガチャ。<br>
    レア排出あり！
  </div>
  <div id="gacha-result"></div>
</div>
<div id="main" class="screen active">
  <!-- メイン画面 -->
</div>

<div id="shop" class="screen">
  <!-- ショップ画面 -->
</div>

<div id="menu-bar">
  <button onclick="showScreen('main')">🏠</button>
  <button onclick="showScreen('menu')">☰</button>
</div>

<div id="screen-main" class="screen">…メイン画面…</div>
<div id="screen-shop" class="screen">…ショップ…</div>
<div id="screen-boss" class="screen">…ボス画面…</div>
<div id="screen-menu" class="screen">
  <h3>メニュー</h3>
  <button onclick="toggleSound()">🔊 効果音</button>
  <div id="status-box"></div>
</div>


<script>
let gameData = {
  pts: 0,
  xp: 0,
  level: 1,
  power: 1,
  autoAmount: 0,
  boostUntil: 0,
  rebirth: 0
};

let bossData = {
  active: false,
  currentBoss: null,
  bosses: {
    goblin: { name: 'ゴブリン王', baseHp: 4500, time: 10 },
    dragon: { name: '炎竜', baseHp: 30000, time: 10 }
  },
  hp: 1000,
  maxHp: 1000,
  time: 10,
  timer: null
};

/* ===== 安定版 効果音システム ===== */
let audioCtx;
let seEnabled = true;

function initAudioOnce(){
  if(!audioCtx){
    audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  }
  if(audioCtx.state === "suspended"){
    audioCtx.resume();
  }
}

function playSE(freq, duration = 0.1, volume = 0.2){
  if(!seEnabled) return;

  initAudioOnce();

  const osc = audioCtx.createOscillator();
  const gain = audioCtx.createGain();

  osc.type = "sine";
  osc.frequency.value = freq;

  gain.gain.value = volume;

  osc.connect(gain);
  gain.connect(audioCtx.destination);

  osc.start();
  osc.stop(audioCtx.currentTime + duration);
}


const playClickSound = () => playSE(700, 0.05, 0.2);
const playBossStartSound = () => playSE(200, 0.3, 0.3);
const playAttackSound = () => playSE(900, 0.08, 0.25);
const playBossWinSound = () => playSE(1200, 0.3, 0.3);
const playBossLoseSound = () => playSE(150, 0.4, 0.3);
const playBuySound = () => playSE(500, 0.15, 0.25);

const playErrorSound = () => playSE(180, 0.12, 0.28);

document.addEventListener("touchstart", initAudioOnce, { once:true });
document.addEventListener("click", initAudioOnce, { once:true });


function showMessage(msg, duration = 1500){
  try{
    playErrorSound();
  }catch(e){ }
  const el = document.createElement('div');
  el.className = 'toast';
  el.innerText = msg;
  document.body.appendChild(el);
  // allow CSS transition
  requestAnimationFrame(() => el.style.opacity = '1');
  setTimeout(() => {
    el.style.opacity = '0';
    setTimeout(() => el.remove(), 300);
  }, duration);
}

function toggleSound(){
  seEnabled = !seEnabled;
  showMessage(seEnabled ? "🔊 効果音ON" : "🔇 効果音OFF");
}


function saveGame(){
  localStorage.setItem("clickerSave", JSON.stringify(gameData));
}

function loadGame(){
  const s = localStorage.getItem("clickerSave");
  if(s){
    try{
      const parsed = JSON.parse(s);
      // parsed のプロパティが定義されていればその値を数値化して代入する（0 を正しく扱うために || を使わない）
      if(parsed.hasOwnProperty('pts')) gameData.pts = Number(parsed.pts);
      if(parsed.hasOwnProperty('xp')) gameData.xp = Number(parsed.xp);
      if(parsed.hasOwnProperty('level')) gameData.level = Number(parsed.level);
      if(parsed.hasOwnProperty('power')) gameData.power = Number(parsed.power);
      if(parsed.hasOwnProperty('autoAmount')) gameData.autoAmount = Number(parsed.autoAmount);
      if(parsed.hasOwnProperty('boostUntil')) gameData.boostUntil = Number(parsed.boostUntil);
      if(parsed.hasOwnProperty('rebirth')) gameData.rebirth = Number(parsed.rebirth);
      // 不足する場合は既定値を保持する
    }catch(e){
      console.log("セーブデータ読み込みエラー:", e);
    }
  }
}

const format = n => {
  if(isNaN(n) || !isFinite(n)) return "0";
  return Math.floor(n).toLocaleString();
};
const getNextXP = lv => Math.floor(100 * Math.pow(1.5, lv - 1));

function updateDisplay(){
  let need = getNextXP(gameData.level);
  while(gameData.xp >= need){
    gameData.xp -= need;
    gameData.level++;
    need = getNextXP(gameData.level);
  }
  
  document.getElementById("pts").innerText = format(gameData.pts);
  document.getElementById("level").innerText = gameData.level;
  document.getElementById("xp-bar").style.width = (gameData.xp / need * 100) + "%";
  const multText = (1 + gameData.rebirth * 0.5).toFixed(1);
  const headerMult = document.getElementById("rebirth-multiplier");
  if(headerMult) headerMult.innerText = multText;
  const topMult = document.getElementById("rebirth-multiplier-top");
  if(topMult) topMult.innerText = multText;
  
  const items = [
    document.getElementById("item1"),
    document.getElementById("item2"), 
    document.getElementById("item3"),
    document.getElementById("item4"),
    document.getElementById("item5"),
    document.getElementById("item6"),
    document.getElementById("item7")
  ];
  
  if(gameData.level >= 3) items[1].classList.add("unlocked");
  if(gameData.level >= 5) items[2].classList.add("unlocked");
  if(gameData.level >= 10) items[3].classList.add("unlocked");
  if(gameData.level >= 12) items[5].classList.add("unlocked");
  if(gameData.level >= 15) items[4].classList.add("unlocked");
  if(gameData.level >= 20) items[6].classList.add("unlocked");
  
  if(gameData.level >= 20) {
    const dragonBtn = document.getElementById("dragon-boss-btn");
    if(dragonBtn) dragonBtn.style.display = "inline-block";
  }
  
  const goblinBtn = document.getElementById("goblin-boss-btn");
  if(goblinBtn) goblinBtn.disabled = gameData.level < 10;
  saveGame();
}

function updateBuffDisplay(){
  const buffContainer = document.getElementById("buff-container");
  buffContainer.innerHTML = "";
  if(gameData.boostUntil > Date.now()){
    buffContainer.innerHTML = `<div class="buff"><span>Wブースト</span><span>${Math.ceil((gameData.boostUntil - Date.now()) / 1000)}s</span></div>`;
  }
}


function performClick(e){
  const boost = Date.now() < gameData.boostUntil ? 2 : 1;
  const gain = Math.floor(gameData.power * boost * (1 + gameData.rebirth * 0.5));

  gameData.pts = (Number(gameData.pts) || 0) + gain;
  gameData.xp = (Number(gameData.xp) || 0) + gain * 2;

  const r = document.createElement("span");
  r.className = "ripple";
  const rect = e.currentTarget.getBoundingClientRect();
  r.style.width = r.style.height = rect.width + "px";
  r.style.left = (e.clientX - rect.left - rect.width / 2) + "px";
  r.style.top = (e.clientY - rect.top - rect.height / 2) + "px";
  e.currentTarget.appendChild(r);
  setTimeout(() => r.remove(), 600);

  const g = document.createElement("div");
  g.className = "gain-text";
  g.innerText = "+" + format(gain) + " G";
  g.style.left = e.clientX + "px";
  g.style.top = e.clientY + "px";
  document.body.appendChild(g);
  setTimeout(() => g.remove(), 1000);

  playClickSound();
  updateDisplay();
}

function startBoss(bossType = 'goblin'){
  bossData.currentBoss = bossType;
  const boss = bossData.bosses[bossType];
  bossData.maxHp = Math.floor(boss.baseHp * (1 + gameData.rebirth * 0.5));
  bossData.hp = bossData.maxHp;
  bossData.active = true;
  bossData.time = Math.max(2, boss.time - gameData.rebirth);
  
  document.getElementById("boss-name").innerText = boss.name;
  document.getElementById("boss-area").style.display = "block";
  document.getElementById("goblin-boss-btn").disabled = true;
  document.getElementById("dragon-boss-btn").disabled = true;

  playBossStartSound();
  updateBossUI();
  
  clearInterval(bossData.timer);
  bossData.timer = setInterval(() => {
    bossData.time--;
    updateBossUI();
    if(bossData.time <= 0) endBoss(false);
  }, 1000);
}

function attackBoss(e){
  if(!bossData.active) return;
  const dmg = Math.floor(gameData.power * (1 + gameData.rebirth * 0.5));
  bossData.hp -= dmg;
  playAttackSound();
  if(bossData.hp <= 0) endBoss(true);
  updateBossUI();
}

function updateBossUI(){
  const currentHp = Math.max(0, Math.floor(bossData.hp));
  
  document.getElementById("boss-hp-text").innerText = `HP ${format(currentHp)}/${format(bossData.maxHp)}`;
  document.getElementById("boss-hp-bar").style.width = Math.max(0, Math.min(100, (currentHp / bossData.maxHp * 100))) + "%";
  document.getElementById("boss-timer").innerText = `残り ${Math.max(0, bossData.time)}s`;
  document.getElementById("boss-rank").innerText = `強化段階 ★${gameData.rebirth}`;
}

function endBoss(win){
  clearInterval(bossData.timer);
  bossData.active = false;
  document.getElementById("boss-area").style.display = "none";
  document.getElementById("goblin-boss-btn").disabled = false;
  document.getElementById("dragon-boss-btn").disabled = false;
  
  if(win){
    const bossRewardMult = (1 + gameData.rebirth * 0.5);
    gameData.pts = (gameData.pts || 0) + Math.floor(3000 * bossRewardMult);
    gameData.xp = (gameData.xp || 0) + Math.floor(500 * bossRewardMult);
    playBossWinSound();
  }else{
    gameData.pts = Math.max(0, (gameData.pts || 0) -  3000);
    playBossLoseSound();
  }
  updateDisplay();
}

function showScreen(name){
  document.querySelectorAll('.screen')
    .forEach(s => s.classList.remove('active'));

  document.getElementById('screen-' + name)
    .classList.add('active');
}

showScreen('main');

function drawGacha(){
  const cost = 5000;
  if(gameData.pts < cost){
    showMessage("Gが足りません");
    return;
  }

  gameData.pts -= cost;
  updateDisplay();

  const overlay = document.getElementById("gacha-overlay");
  const box = document.getElementById("gacha-box");
  const text = document.getElementById("gacha-text");

  overlay.style.display = "flex";
  box.className = "";
  text.innerText = "抽選中…";

  playBuySound();

  setTimeout(() => {
    const r = Math.random() * 100;
    let powerGain = 0;
    let resultText = "";

    if(r < 60){
      powerGain = 10;
      resultText = "ノーマル\n攻撃力 +10";
    }else if(r < 85){
      powerGain = 50;
      resultText = "レア！\n攻撃力 +50";
      box.classList.add("gacha-rare");
    }else if(r < 95){
      powerGain = 100;
      resultText = "スーパーレア！！\n攻撃力 +100";
      box.classList.add("gacha-super");
    }else{
      powerGain = 500;
      resultText = "✨ウルトラレア✨\n攻撃力 +500";
      box.classList.add("gacha-ultra");
    }

    gameData.power += powerGain;
    text.innerText = resultText;

    playBossWinSound();
    updateDisplay();

    setTimeout(() => {
      overlay.style.display = "none";
      document.getElementById("gacha-result").innerText =
        resultText.replace("\n", " ");
    }, 1800);

  }, 1500);
}


function buyItem(id, cost){
  if((Number(gameData.pts) || 0) < cost){
    showMessage("お金が足りません");
    return;
  }


  gameData.pts = (Number(gameData.pts) || 0) - cost;
  switch(id){
    case 1: gameData.power = (Number(gameData.power) || 0) + 1; break;
    case 2: gameData.autoAmount = (Number(gameData.autoAmount) || 0) + 1; break;
    case 3: gameData.power = (Number(gameData.power) || 0) + 3; break;
    case 4: gameData.autoAmount = (Number(gameData.autoAmount) || 0) + 5; break;
    case 5: gameData.boostUntil = Date.now() + 10000; break;
    case 6: gameData.power = (Number(gameData.power) || 0) + 10; break;
    case 7: 
      // 倍率を +20% して、アイテムをリセット
      gameData.rebirth = (Number(gameData.rebirth) || 0) + 1;
      gameData.level = 1;
      gameData.xp = 0;
      gameData.pts = 0;
      gameData.power = 1;
      gameData.autoAmount = 0;
      gameData.boostUntil = 0;
      break;
  }
  playBuySound();
  updateDisplay();
}

// 初期化処理
loadGame();
updateDisplay();

// 自動更新ループ
setInterval(() => {
  // 数値化してから加算することで文字列連結などの不具合を防ぐ
  if((Number(gameData.autoAmount) || 0) > 0 && !bossData.active){
    gameData.pts = (Number(gameData.pts) || 0) + (Number(gameData.autoAmount) || 0);
    updateDisplay();
  }
  updateBuffDisplay();
}, 1000);

window.addEventListener("beforeunload", saveGame);

</script>

<!-- ガチャ演出 -->
<div id="gacha-overlay">
  <div id="gacha-box">
    <div id="gacha-text">抽選中…</div>
  </div>
</div>

</body>
</html>