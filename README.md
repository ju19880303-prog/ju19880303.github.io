# ju19880303.github.io
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover" />
  <meta name="apple-mobile-web-app-capable" content="yes" />
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
  <meta name="apple-mobile-web-app-title" content="暗影深淵" />
  <meta name="mobile-web-app-capable" content="yes" />
  <meta name="theme-color" content="#0b0b12" />
  <link rel="icon" type="image/svg+xml" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 64 64'%3E%3Crect width='64' height='64' rx='14' fill='%2312101c'/%3E%3Ccircle cx='32' cy='32' r='22' fill='none' stroke='%23d4af37' stroke-width='3'/%3E%3Cpath d='M32 12 L42 36 L32 52 L22 36 Z' fill='%239b59b6'/%3E%3Ccircle cx='32' cy='33' r='5' fill='%23ffd700'/%3E%3C/svg%3E" />
  <title>暗影深淵：狩魔者</title>
  <style>
    :root {
      --bg-dark: #09090f;
      --panel-bg: rgba(18, 17, 28, 0.94);
      --panel-border: #3a3352;
      --gold: #e6c25b;
      --gold-dim: #9e8236;
      --crimson: #e74c3c;
      --cyan: #38bdf8;
      --purple: #a855f7;
      --green: #22c55e;
      --text-main: #f1f5f9;
      --text-muted: #94a3b8;
    }
    * {
      box-sizing: border-box;
      user-select: none;
      -webkit-user-select: none;
      -webkit-touch-callout: none;
      margin: 0;
      padding: 0;
    }
    html, body {
      width: 100%;
      height: 100dvh;
      overflow: hidden;
      background: var(--bg-dark);
      color: var(--text-main);
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Noto Sans TC", sans-serif;
      touch-action: none;
      overscroll-behavior: none;
    }
    #gameCanvas {
      display: block;
      width: 100%;
      height: 100dvh;
      touch-action: none;
    }
    .hidden { display: none !important; }

    /* Error Guard */
    #errorBanner {
      position: fixed;
      top: 8px;
      left: 50%;
      transform: translateX(-50%);
      z-index: 9999;
      background: rgba(185, 28, 28, 0.95);
      color: #fff;
      padding: 8px 14px;
      border-radius: 8px;
      font-size: 12px;
      max-width: 92vw;
      border: 1px solid #fca5a5;
    }

    /* HUD */
    #hud {
      position: fixed;
      inset: 0;
      pointer-events: none;
      z-index: 20;
    }
    .hud-top {
      position: absolute;
      top: max(8px, env(safe-area-inset-top));
      left: 10px;
      right: 10px;
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
      gap: 8px;
    }
    .hud-card {
      background: rgba(13, 13, 22, 0.85);
      border: 1px solid rgba(230, 194, 91, 0.35);
      border-radius: 10px;
      padding: 6px 10px;
      backdrop-filter: blur(6px);
      pointer-events: auto;
    }
    .bar-wrap {
      width: 145px;
      height: 11px;
      background: #1e1b2e;
      border-radius: 6px;
      overflow: hidden;
      margin-top: 4px;
      border: 1px solid #332e4d;
      position: relative;
    }
    .bar-fill {
      height: 100%;
      transition: width 0.12s linear;
    }
    .bar-text {
      position: absolute;
      inset: 0;
      font-size: 9px;
      line-height: 10px;
      text-align: center;
      color: #fff;
      text-shadow: 0 1px 2px #000;
      font-weight: 700;
    }
    .toolbar {
      display: flex;
      flex-wrap: wrap;
      justify-content: flex-end;
      gap: 5px;
      max-width: 62vw;
    }
    .btn-hud {
      background: linear-gradient(180deg, #27233a, #171524);
      border: 1px solid #4c436e;
      color: #f8fafc;
      padding: 6px 9px;
      border-radius: 8px;
      font-size: 12px;
      font-weight: 600;
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      gap: 4px;
      position: relative;
    }
    .btn-hud:active { transform: scale(0.95); }
    .badge-dot {
      position: absolute;
      top: -4px;
      right: -4px;
      background: #ef4444;
      color: #fff;
      font-size: 9px;
      min-width: 15px;
      height: 15px;
      border-radius: 9px;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 0 3px;
      font-weight: 700;
    }

    /* Stage Timeline & Boss Bar */
    #stageTimeline {
      position: absolute;
      top: calc(max(8px, env(safe-area-inset-top)) + 66px);
      left: 50%;
      transform: translateX(-50%);
      width: min(360px, 86vw);
      background: rgba(12, 12, 20, 0.84);
      border: 1px solid #3f385c;
      border-radius: 10px;
      padding: 5px 10px;
      text-align: center;
    }
    .tl-track {
      height: 7px;
      background: #1e1b2e;
      border-radius: 4px;
      position: relative;
      margin-top: 4px;
    }
    .tl-fill {
      height: 100%;
      background: linear-gradient(90deg, #38bdf8, #a855f7, #ef4444);
      border-radius: 4px;
      width: 0%;
    }
    .tl-pin {
      position: absolute;
      top: -3px;
      width: 3px;
      height: 13px;
      background: #fbbf24;
      border-radius: 2px;
    }
    #bossHud {
      position: absolute;
      top: calc(max(8px, env(safe-area-inset-top)) + 110px);
      left: 50%;
      transform: translateX(-50%);
      width: min(380px, 90vw);
      background: rgba(28, 10, 16, 0.9);
      border: 1px solid #ef4444;
      border-radius: 10px;
      padding: 6px 12px;
      text-align: center;
    }

    /* Bottom Skills & Dodge */
    .hud-bottom {
      position: absolute;
      bottom: max(12px, env(safe-area-inset-bottom));
      left: 12px;
      right: 12px;
      display: flex;
      justify-content: space-between;
      align-items: flex-end;
    }
    .skill-bar {
      display: flex;
      gap: 7px;
      pointer-events: auto;
    }
    .skill-slot {
      width: 50px;
      height: 50px;
      border-radius: 10px;
      background: rgba(16, 15, 26, 0.9);
      border: 1.5px solid #4c436e;
      position: relative;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      overflow: hidden;
      cursor: pointer;
    }
    .skill-slot.active { border-color: var(--gold); }
    .skill-cd-mask {
      position: absolute;
      bottom: 0;
      left: 0;
      right: 0;
      background: rgba(0, 0, 0, 0.68);
      pointer-events: none;
    }
    .skill-lv {
      position: absolute;
      top: 2px;
      right: 4px;
      font-size: 9px;
      color: var(--gold);
      font-weight: 700;
    }
    .skill-auto {
      position: absolute;
      bottom: 1px;
      font-size: 8px;
      color: #4ade80;
      font-weight: 700;
    }
    #dodgeBtn {
      width: 74px;
      height: 74px;
      border-radius: 50%;
      background: radial-gradient(circle at 30% 30%, #3b3363, #171426);
      border: 2px solid var(--gold);
      color: #fff;
      font-size: 13px;
      font-weight: 700;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      pointer-events: auto;
      box-shadow: 0 4px 16px rgba(0,0,0,0.6);
      position: relative;
      overflow: hidden;
      cursor: pointer;
    }
    #dodgeBtn:active { transform: scale(0.92); }
    #npcInteractBtn {
      position: absolute;
      bottom: 102px;
      left: 50%;
      transform: translateX(-50%);
      background: linear-gradient(135deg, #d97706, #b45309);
      border: 2px solid #fde68a;
      color: #fff;
      padding: 10px 20px;
      border-radius: 24px;
      font-size: 14px;
      font-weight: 700;
      pointer-events: auto;
      box-shadow: 0 4px 20px rgba(217, 119, 6, 0.5);
      cursor: pointer;
      animation: pulse 1.4s infinite;
    }
    @keyframes pulse {
      0%, 100% { transform: translateX(-50%) scale(1); }
      50% { transform: translateX(-50%) scale(1.05); }
    }

    /* Modals & Title */
    .overlay {
      position: fixed;
      inset: 0;
      background: rgba(6, 6, 12, 0.84);
      backdrop-filter: blur(6px);
      z-index: 100;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 12px;
    }
    .modal {
      background: var(--panel-bg);
      border: 1.5px solid var(--panel-border);
      border-radius: 14px;
      width: min(640px, 96vw);
      max-height: 88dvh;
      display: flex;
      flex-direction: column;
      overflow: hidden;
      box-shadow: 0 12px 40px rgba(0,0,0,0.8);
    }
    .modal-header {
      padding: 12px 16px;
      background: rgba(26, 24, 42, 0.95);
      border-bottom: 1px solid var(--panel-border);
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 8px;
    }
    .modal-title {
      font-size: 16px;
      font-weight: 700;
      color: var(--gold);
    }
    .modal-body {
      padding: 14px;
      overflow-y: auto;
      touch-action: pan-y;
      flex: 1;
    }
    .btn {
      background: linear-gradient(180deg, #332d4d, #211d33);
      border: 1px solid #594f82;
      color: #fff;
      padding: 8px 14px;
      border-radius: 8px;
      font-size: 13px;
      font-weight: 600;
      cursor: pointer;
    }
    .btn:disabled { opacity: 0.45; cursor: not-allowed; }
    .btn-gold {
      background: linear-gradient(180deg, #d97706, #92400e);
      border-color: #fbbf24;
      color: #fff;
    }
    .btn-green {
      background: linear-gradient(180deg, #16a34a, #14532d);
      border-color: #4ade80;
    }
    .btn-red {
      background: linear-gradient(180deg, #dc2626, #7f1d1d);
      border-color: #f87171;
    }

    /* Class & Gender Grid */
    .gender-row {
      display: flex;
      gap: 10px;
      justify-content: center;
      margin-bottom: 12px;
    }
    .gender-btn {
      flex: 1;
      max-width: 170px;
      padding: 8px;
      border-radius: 10px;
      border: 1.5px solid #3f385c;
      background: #171524;
      color: #cbd5e1;
      font-weight: 700;
      cursor: pointer;
    }
    .gender-btn.selected {
      border-color: var(--gold);
      background: rgba(230, 194, 91, 0.16);
      color: var(--gold);
    }
    .class-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(170px, 1fr));
      gap: 10px;
    }
    .class-card {
      background: #161423;
      border: 1.5px solid #352f4d;
      border-radius: 10px;
      padding: 10px;
      cursor: pointer;
      transition: transform 0.12s, border-color 0.12s;
    }
    .class-card.selected {
      border-color: var(--gold);
      background: rgba(230, 194, 91, 0.12);
    }

    /* Equipment 10-slot & 100-bag */
    .equip-grid {
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      gap: 6px;
      margin-bottom: 12px;
    }
    .equip-slot {
      background: #151321;
      border: 1.5px solid #383252;
      border-radius: 8px;
      padding: 6px 4px;
      text-align: center;
      min-height: 62px;
      cursor: pointer;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      font-size: 11px;
    }
    .bag-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(56px, 1fr));
      gap: 6px;
    }
    .bag-cell {
      aspect-ratio: 1;
      background: #14121f;
      border: 1.5px solid #2e2945;
      border-radius: 8px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      font-size: 11px;
      position: relative;
      cursor: pointer;
    }
    .rarity-0 { border-color: #64748b !important; color: #e2e8f0; }
    .rarity-1 { border-color: #38bdf8 !important; color: #7dd3fc; }
    .rarity-2 { border-color: #facc15 !important; color: #fef08a; }
    .rarity-3 { border-color: #f97316 !important; color: #fdba74; }

    /* Skill Tree & Stats */
    .stat-row {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 8px 10px;
      background: #161423;
      border: 1px solid #322c4a;
      border-radius: 8px;
      margin-bottom: 6px;
    }
    .tree-tabs {
      display: flex;
      gap: 6px;
      margin-bottom: 10px;
    }
    .tree-tab {
      flex: 1;
      padding: 7px 6px;
      background: #161423;
      border: 1px solid #3a3352;
      border-radius: 8px;
      color: #cbd5e1;
      font-size: 12px;
      font-weight: 700;
      cursor: pointer;
    }
    .tree-tab.active {
      border-color: var(--gold);
      color: var(--gold);
      background: rgba(230, 194, 91, 0.14);
    }
    .tree-node {
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: #161423;
      border: 1px solid #36304f;
      border-radius: 8px;
      padding: 8px 10px;
      margin-bottom: 7px;
      gap: 8px;
    }
    .tree-node.locked { opacity: 0.55; }

    /* Blessing Cards */
    .card-list {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(175px, 1fr));
      gap: 10px;
    }
    .blessing-card {
      background: linear-gradient(180deg, #211d36, #141222);
      border: 1.5px solid #594f82;
      border-radius: 12px;
      padding: 12px;
      cursor: pointer;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      gap: 6px;
    }
    .blessing-card:hover, .blessing-card:active {
      border-color: var(--gold);
      transform: translateY(-2px);
    }
  </style>
</head>
<body>
  <div id="errorBanner" class="hidden"></div>
  <canvas id="gameCanvas"></canvas>

  <!-- HUD -->
  <div id="hud" class="hidden">
    <div class="hud-top">
      <div class="hud-card">
        <div style="font-size:12px;font-weight:700;color:var(--gold)" id="hudTitle">庇護聖所</div>
        <div style="font-size:11px;color:#cbd5e1" id="hudSub">英雄 Lv.1 | 任務 Lv.1 | 💰0</div>
        <div class="bar-wrap">
          <div class="bar-fill" id="hpBar" style="background:linear-gradient(90deg,#dc2626,#ef4444);width:100%"></div>
          <div class="bar-text" id="hpText">100 / 100</div>
        </div>
        <div class="bar-wrap" style="height:8px;margin-top:3px">
          <div class="bar-fill" id="xpBar" style="background:linear-gradient(90deg,#0284c7,#38bdf8);width:0%"></div>
          <div class="bar-text" id="xpText" style="font-size:8px;line-height:7px">EXP 0%</div>
        </div>
      </div>

      <div class="toolbar" id="townToolbar">
        <button class="btn-hud" onclick="openModal('equipModal')">🎒 裝備欄</button>
        <button class="btn-hud" onclick="openModal('statModal')">📊 屬性<span class="badge-dot hidden" id="statBadge">0</span></button>
        <button class="btn-hud" onclick="openModal('treeModal')">🌳 技能樹<span class="badge-dot hidden" id="treeBadge">0</span></button>
        <button class="btn-hud" onclick="openModal('saveModal')">💾 存檔</button>
      </div>

      <div class="toolbar hidden" id="stageToolbar">
        <button class="btn-hud" id="zoomBtn" onclick="cycleZoom()">🔍 0.70x</button>
        <button class="btn-hud" onclick="retreatToTown()">🏃 撤退回城</button>
      </div>
    </div>

    <div id="stageTimeline" class="hidden">
      <div style="display:flex;justify-content:space-between;font-size:11px;font-weight:700">
        <span id="stageNameHud">第1關</span>
        <span id="stageClock">00:00 / 07:30</span>
        <span id="killCountHud">擊殺: 0</span>
      </div>
      <div class="tl-track">
        <div class="tl-fill" id="tlFill"></div>
        <div class="tl-pin" style="left:20%"></div>
        <div class="tl-pin" style="left:40%"></div>
        <div class="tl-pin" style="left:60%"></div>
        <div class="tl-pin" style="left:80%"></div>
        <div class="tl-pin" style="left:99%;background:#ef4444"></div>
      </div>
    </div>

    <div id="bossHud" class="hidden">
      <div style="font-size:12px;font-weight:700;color:#fca5a5" id="bossNameHud">深淵首領</div>
      <div class="bar-wrap" style="width:100%;height:12px">
        <div class="bar-fill" id="bossHpBar" style="background:linear-gradient(90deg,#991b1b,#ef4444);width:100%"></div>
        <div class="bar-text" id="bossHpText">1000 / 1000</div>
      </div>
    </div>

    <button id="npcInteractBtn" class="hidden" onclick="interactNearestNpc()">💬 與 NPC 對話</button>

    <div class="hud-bottom">
      <div class="skill-bar" id="skillBar"></div>
      <button id="dodgeBtn" onpointerdown="triggerDodge(event)">
        <span id="dodgeLabel">暗影步</span>
        <span id="dodgeCdText" style="font-size:10px;color:#fde68a">就緒</span>
      </button>
    </div>
  </div>

  <!-- Title / Character Creation -->
  <div id="titleOverlay" class="overlay">
    <div class="modal">
      <div class="modal-header">
        <div class="modal-title">⚔️ 暗影深淵：狩魔者</div>
        <span style="font-size:11px;color:var(--text-muted)">純淨暗黑割草動作 RPG</span>
      </div>
      <div class="modal-body">
        <div id="continueBox" class="hidden" style="background:rgba(34,197,94,0.14);border:1px solid #22c55e;border-radius:10px;padding:10px;margin-bottom:12px;display:flex;justify-content:space-between;align-items:center;gap:8px">
          <div>
            <div style="font-size:13px;font-weight:700;color:#86efac">偵測到冒險存檔</div>
            <div style="font-size:11px;color:#cbd5e1" id="saveSummaryText">任務等級 Lv.1</div>
          </div>
          <button class="btn btn-green" onclick="continueGame()">▶ 繼續冒險</button>
        </div>

        <div style="font-size:12px;color:var(--gold);font-weight:700;margin-bottom:6px;text-align:center">1. 選擇英雄性別</div>
        <div class="gender-row">
          <button class="gender-btn selected" id="genderM" onclick="selectGender('M')">♂ 男性英雄</button>
          <button class="gender-btn" id="genderF" onclick="selectGender('F')">♀ 女性英雄</button>
        </div>

        <div style="font-size:12px;color:var(--gold);font-weight:700;margin-bottom:6px;text-align:center">2. 選擇職業</div>
        <div class="class-grid" id="classGrid"></div>

        <div style="margin-top:14px;display:flex;justify-content:center">
          <button class="btn btn-gold" style="width:100%;max-width:280px;padding:11px;font-size:15px" onclick="startNewGame()">踏入庇護聖所</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Dynamic Modal Container -->
  <div id="dynamicOverlay" class="overlay hidden" onclick="if(event.target===this && canCloseModal) closeModal()">
    <div class="modal">
      <div class="modal-header">
        <div class="modal-title" id="dynTitle">視窗</div>
        <div style="display:flex;gap:6px;align-items:center" id="dynHeaderActions"></div>
      </div>
      <div class="modal-body" id="dynBody"></div>
    </div>
  </div>

<script>
window.onerror = function(msg, src, line) {
  const b = document.getElementById('errorBanner');
  if (b) {
    b.textContent = '執行提示: ' + msg + ' (L' + line + ')';
    b.classList.remove('hidden');
  }
};

/* ==================== DATA DEFINITIONS ==================== */
const SAVE_KEY = 'shadow_abyss_save_v5';

const EQUIP_SLOTS = [
  { id: 'weapon', name: '主手武器', icon: '⚔️' },
  { id: 'offhand', name: '副手/盾', icon: '🛡️' },
  { id: 'hat', name: '帽子', icon: '🎩' },
  { id: 'armor', name: '衣服', icon: '👕' },
  { id: 'pants', name: '褲子', icon: '👖' },
  { id: 'boots', name: '鞋子', icon: '👢' },
  { id: 'head1', name: '頭飾 1', icon: '👑' },
  { id: 'head2', name: '頭飾 2', icon: '🎀' },
  { id: 'acc1', name: '飾品 1', icon: '💍' },
  { id: 'acc2', name: '飾品 2', icon: '📿' }
];

const RARITIES = [
  { name: '普通', color: '#94a3b8', mult: 1.0 },
  { name: '魔法', color: '#38bdf8', mult: 1.25 },
  { name: '稀有', color: '#facc15', mult: 1.55 },
  { name: '傳奇', color: '#f97316', mult: 1.95 }
];

const CLASSES = {
  mage: {
    id: 'mage', name: '法師', icon: '🔮', color: '#38bdf8', attackType: 'ranged', dodgeName: '暗影步',
    desc: '遠程奧術彈普攻，擅長大範圍元素連鎖與控場',
    base: { hp: 110, atk: 14, spd: 185, crit: 0.08, cdr: 0.05 },
    skills: [
      { id: 'chain_lightning', name: '連鎖閃電', icon: '⚡', cd: 2.2, dmg: 1.15, desc: '高壓電弧在多名敵人間連續彈跳' },
      { id: 'frost_nova', name: '霜凍新星', icon: '❄️', cd: 3.4, dmg: 1.1, desc: '周身爆發寒冰衝擊波並大幅減速敵人' },
      { id: 'meteor', name: '隕石術', icon: '☄️', cd: 4.0, dmg: 1.85, desc: '鎖定密集敵群降下燃燒隕石與火海' },
      { id: 'arcane_orb', name: '秘法軌彈', icon: '🔮', cd: 1.8, dmg: 0.95, desc: '發射多枚自動追蹤秘法飛彈' },
      { id: 'storm_sphere', name: '雷暴光球', icon: '🌐', cd: 3.8, dmg: 1.25, desc: '釋放緩慢推進並持續電擊周圍的雷球' },
      { id: 'flame_vortex', name: '烈焰風暴', icon: '🌪️', cd: 4.2, dmg: 1.4, desc: '召喚旋轉火龍捲持續絞殺目標區域' },
      { id: 'ice_lance', name: '冰霜長槍', icon: '🗡️', cd: 2.0, dmg: 1.35, desc: '高速穿透冰槍，對減速目標造成雙倍暴擊' }
    ],
    trees: [
      { name: '烈焰隕星流', nodes: [
        { id: 'm_f1', name: '余燼增幅', max: 3, desc: '火焰與隕石技能傷害 +12%/級', stat: 'skillDmg', val: 0.12 },
        { id: 'm_f2', name: '廣域爆燃', max: 3, desc: '全技能範圍半徑 +10%/級', stat: 'area', val: 0.10 },
        { id: 'm_f3', name: '雙星墜落', max: 2, desc: '技能額外目標/投射物 +1/級', stat: 'proj', val: 1 },
        { id: 'm_f4', name: '灼熱暴擊', max: 3, desc: '暴擊傷害 +22%/級', stat: 'critDmg', val: 0.22 },
        { id: 'm_f5', name: '末日火雨', max: 2, desc: '全技能傷害 +20%/級', stat: 'skillDmg', val: 0.20 },
        { id: 'm_f6', name: '奧義：焚天魔導', max: 1, desc: '技能傷害 +35% 且目標數 +1', stat: 'cap_mf', val: 1 }
      ]},
      { name: '雷霆連鎖流', nodes: [
        { id: 'm_l1', name: '高壓傳導', max: 3, desc: '技能冷卻縮減 +6%/級', stat: 'cdr', val: 0.06 },
        { id: 'm_l2', name: '電弧跳躍', max: 3, desc: '全技能目標/彈跳數 +1/級', stat: 'proj', val: 1 },
        { id: 'm_l3', name: '超載核心', max: 3, desc: '暴擊率 +5%/級', stat: 'crit', val: 0.05 },
        { id: 'm_l4', name: '迅捷施法', max: 3, desc: '移動速度 +7%/級，冷卻 +5%/級', stat: 'cdr', val: 0.05 },
        { id: 'm_l5', name: '雷神之怒', max: 2, desc: '全技能傷害 +18%/級', stat: 'skillDmg', val: 0.18 },
        { id: 'm_l6', name: '奧義：萬雷天牢', max: 1, desc: '冷卻縮減 +18% 且目標數 +2', stat: 'cap_ml', val: 1 }
      ]},
      { name: '永凍控場流', nodes: [
        { id: 'm_i1', name: '極寒護體', max: 3, desc: '最大生命 +14%/級', stat: 'hpPct', val: 0.14 },
        { id: 'm_i2', name: '冰霜擴散', max: 3, desc: '技能範圍 +12%/級', stat: 'area', val: 0.12 },
        { id: 'm_i3', name: '碎冰重擊', max: 3, desc: '暴擊傷害 +20%/級', stat: 'critDmg', val: 0.20 },
        { id: 'm_i4', name: '寒冰屏障', max: 3, desc: '傷害減免 +5%/級', stat: 'dr', val: 0.05 },
        { id: 'm_i5', name: '絕對零度', max: 2, desc: '全技能冷卻縮減 +8%/級', stat: 'cdr', val: 0.08 },
        { id: 'm_i6', name: '奧義：冰河紀元', max: 1, desc: '範圍 +30%、生命 +30%、技能傷 +25%', stat: 'cap_mi', val: 1 }
      ]}
    ]
  },
  berserker: {
    id: 'berserker', name: '狂戰士', icon: '🪓', color: '#ef4444', attackType: 'melee', dodgeName: '暗影步',
    desc: '近戰重型弧形斬擊，高生命嗜血與貼身旋風爆發',
    base: { hp: 155, atk: 16, spd: 192, crit: 0.07, cdr: 0.02 },
    skills: [
      { id: 'whirlwind', name: '旋風斬', icon: '🌀', cd: 2.4, dmg: 1.25, desc: '繞身揮出高速迴旋斬擊絞殺近身敵群' },
      { id: 'blood_cleave', name: '血怒重劈', icon: '🩸', cd: 2.6, dmg: 1.5, desc: '朝敵群重劈出裂地血斬並回復少量生命' },
      { id: 'warcry', name: '戰吼震波', icon: '📣', cd: 4.0, dmg: 1.05, desc: '發出怒吼震退敵人並提升自身攻速' },
      { id: 'ground_slam', name: '裂地猛擊', icon: '💥', cd: 3.4, dmg: 1.65, desc: '重擊地面產生震波與碎石飛濺' },
      { id: 'flying_axes', name: '狂風飛斧', icon: '🪓', cd: 2.3, dmg: 1.15, desc: '朝四周拋出迴旋飛斧收割怪群' },
      { id: 'lava_stomp', name: '熔岩踐踏', icon: '🌋', cd: 3.6, dmg: 1.45, desc: '迸發裂地岩漿波紋灼燒周圍魔物' },
      { id: 'blood_crescent', name: '血魂斬擊', icon: '🌙', cd: 2.8, dmg: 1.4, desc: '揮出巨型血氣月牙波貫穿前方敵陣' }
    ],
    trees: [
      { name: '狂怒旋風流', nodes: [
        { id: 'b_w1', name: '利刃風暴', max: 3, desc: '近戰與旋風技能傷害 +12%/級', stat: 'skillDmg', val: 0.12 },
        { id: 'b_w2', name: '巨斧橫掃', max: 3, desc: '技能範圍半徑 +12%/級', stat: 'area', val: 0.12 },
        { id: 'b_w3', name: '疾風步伐', max: 3, desc: '移動速度 +8%/級', stat: 'spdPct', val: 0.08 },
        { id: 'b_w4', name: '連環飛斧', max: 2, desc: '技能額外目標/投射物 +1/級', stat: 'proj', val: 1 },
        { id: 'b_w5', name: '無盡狂怒', max: 2, desc: '全技能冷卻縮減 +8%/級', stat: 'cdr', val: 0.08 },
        { id: 'b_w6', name: '奧義：血風暴君', max: 1, desc: '技能範圍 +35% 且技能傷害 +35%', stat: 'cap_bw', val: 1 }
      ]},
      { name: '嗜血狂戰流', nodes: [
        { id: 'b_b1', name: '渴血本能', max: 3, desc: '擊殺回復生命 +2/級', stat: 'leech', val: 2 },
        { id: 'b_b2', name: '蠻荒體魄', max: 3, desc: '最大生命 +15%/級', stat: 'hpPct', val: 0.15 },
        { id: 'b_b3', name: '殘暴重擊', max: 3, desc: '暴擊率 +5%/級', stat: 'crit', val: 0.05 },
        { id: 'b_b4', name: '處決狂熱', max: 3, desc: '暴擊傷害 +24%/級', stat: 'critDmg', val: 0.24 },
        { id: 'b_b5', name: '鋼鐵意志', max: 2, desc: '傷害減免 +6%/級', stat: 'dr', val: 0.06 },
        { id: 'b_b6', name: '奧義：不滅戰神', max: 1, desc: '生命 +40%、吸血 +5、傷害 +25%', stat: 'cap_bb', val: 1 }
      ]},
      { name: '裂地重擊流', nodes: [
        { id: 'b_g1', name: '千鈞之力', max: 3, desc: '基礎與技能攻擊力 +12%/級', stat: 'atkPct', val: 0.12 },
        { id: 'b_g2', name: '餘震擴散', max: 3, desc: '技能範圍 +10%/級', stat: 'area', val: 0.10 },
        { id: 'b_g3', name: '碎甲一擊', max: 3, desc: '全技能傷害 +13%/級', stat: 'skillDmg', val: 0.13 },
        { id: 'b_g4', name: '戰神號令', max: 3, desc: '冷卻縮減 +6%/級', stat: 'cdr', val: 0.06 },
        { id: 'b_g5', name: '山崩地裂', max: 2, desc: '目標與投射物 +1/級', stat: 'proj', val: 1 },
        { id: 'b_g6', name: '奧義：滅世震擊', max: 1, desc: '攻擊力 +35% 且暴擊傷害 +50%', stat: 'cap_bg', val: 1 }
      ]}
    ]
  },
  hunter: {
    id: 'hunter', name: '狩魔獵人', icon: '🏹', color: '#22c55e', attackType: 'ranged', dodgeName: '暗影步',
    desc: '遠程高機動獵魔箭矢，擅長直線貫穿與高頻暴擊彈幕',
    base: { hp: 120, atk: 15, spd: 205, crit: 0.12, cdr: 0.04 },
    skills: [
      { id: 'pierce_volley', name: '穿心箭雨', icon: '🏹', cd: 2.1, dmg: 1.15, desc: '齊射多支高速穿透箭矢貫穿整排敵人' },
      { id: 'chakram', name: '連環飛刃', icon: '🪃', cd: 2.5, dmg: 1.25, desc: '拋出迴旋雙刃切割中距離密集怪群' },
      { id: 'shadow_trap', name: '暗影陷阱', icon: '🕸️', cd: 3.2, dmg: 1.55, desc: '在周圍布下引爆陷阱重創並減速敵人' },
      { id: 'rapid_fire', name: '疾風連射', icon: '💨', cd: 1.7, dmg: 0.92, desc: '高頻射出多發追擊獵魔箭矢' },
      { id: 'explosive_arrow', name: '爆裂箭矢', icon: '💣', cd: 2.7, dmg: 1.45, desc: '命中目標引發二次連環火藥爆炸' },
      { id: 'blade_rain', name: '暗影刃雨', icon: '🗡️', cd: 3.5, dmg: 1.5, desc: '在敵群上方降下密集暗影飛刀雨' },
      { id: 'sentry_turret', name: '雷霆箭塔', icon: '🗼', cd: 4.0, dmg: 1.2, desc: '釋放自動連鎖電擊的獵魔箭塔脈衝' }
    ],
    trees: [
      { name: '穿心箭雨流', nodes: [
        { id: 'h_p1', name: '銳利箭鏃', max: 3, desc: '攻擊力 +11%/級', stat: 'atkPct', val: 0.11 },
        { id: 'h_p2', name: '多重齊射', max: 3, desc: '全技能投射物/目標 +1/級', stat: 'proj', val: 1 },
        { id: 'h_p3', name: '鷹眼鎖定', max: 3, desc: '暴擊率 +6%/級', stat: 'crit', val: 0.06 },
        { id: 'h_p4', name: '致命穿刺', max: 3, desc: '暴擊傷害 +25%/級', stat: 'critDmg', val: 0.25 },
        { id: 'h_p5', name: '箭雨風暴', max: 2, desc: '技能冷卻縮減 +7%/級', stat: 'cdr', val: 0.07 },
        { id: 'h_p6', name: '奧義：萬箭穿心', max: 1, desc: '投射物 +2 且暴擊傷害 +55%', stat: 'cap_hp', val: 1 }
      ]},
      { name: '疾風連射流', nodes: [
        { id: 'h_r1', name: '靈巧身法', max: 3, desc: '移動速度 +8%/級', stat: 'spdPct', val: 0.08 },
        { id: 'h_r2', name: '快速裝填', max: 3, desc: '全技能冷卻縮減 +6%/級', stat: 'cdr', val: 0.06 },
        { id: 'h_r3', name: '風行閃避', max: 3, desc: '閃避率 +4%/級', stat: 'dodge', val: 0.04 },
        { id: 'h_r4', name: '連環彈幕', max: 2, desc: '技能投射物 +1/級', stat: 'proj', val: 1 },
        { id: 'h_r5', name: '獵殺時刻', max: 3, desc: '全技能傷害 +14%/級', stat: 'skillDmg', val: 0.14 },
        { id: 'h_r6', name: '奧義：幻影神射手', max: 1, desc: '冷卻縮減 +18%、移速 +20%、投射物 +1', stat: 'cap_hr', val: 1 }
      ]},
      { name: '暗影陷阱流', nodes: [
        { id: 'h_t1', name: '高爆火藥', max: 3, desc: '技能範圍 +12%/級', stat: 'area', val: 0.12 },
        { id: 'h_t2', name: '致命機關', max: 3, desc: '全技能傷害 +13%/級', stat: 'skillDmg', val: 0.13 },
        { id: 'h_t3', name: '暗影斗篷', max: 3, desc: '最大生命 +13%/級', stat: 'hpPct', val: 0.13 },
        { id: 'h_t4', name: '連環引爆', max: 2, desc: '技能目標/投射物 +1/級', stat: 'proj', val: 1 },
        { id: 'h_t5', name: '秘寶嗅覺', max: 3, desc: '掉寶幸運 +15%/級', stat: 'luckPct', val: 0.15 },
        { id: 'h_t6', name: '奧義：深淵獵殺網', max: 1, desc: '範圍 +35% 且全技能傷害 +35%', stat: 'cap_ht', val: 1 }
      ]}
    ]
  },
  necro: {
    id: 'necro', name: '死靈法師', icon: '💀', color: '#a855f7', attackType: 'ranged', dodgeName: '暗影步',
    desc: '遠程暗影骨矢普攻，驅使骸骨軍團、屍爆與瘟疫詛咒',
    base: { hp: 125, atk: 14, spd: 185, crit: 0.07, cdr: 0.05 },
    skills: [
      { id: 'skeleton_army', name: '骸骨軍團', icon: '💀', cd: 3.5, dmg: 1.1, desc: '召喚自動衝鋒撕咬魔物的骸骨戰士' },
      { id: 'corpse_explosion', name: '屍爆術', icon: '💣', cd: 2.6, dmg: 1.55, desc: '引爆敵群周圍的暗影屍骸造成範圍轟炸' },
      { id: 'decrepify', name: '衰老詛咒', icon: '紫', cd: 3.4, dmg: 1.15, desc: '展開詛咒力場腐蝕並大幅緩速敵軍' },
      { id: 'bone_spear', name: '白骨之矛', icon: '🦴', cd: 2.0, dmg: 1.25, desc: '發射高穿透白骨長矛刺穿成排敵人' },
      { id: 'wraith_rush', name: '怨靈衝擊', icon: '👻', cd: 2.4, dmg: 1.2, desc: '釋放成群追蹤怨靈撕咬周圍魔物' },
      { id: 'poison_nova', name: '劇毒新星', icon: '☠️', cd: 3.2, dmg: 1.35, desc: '向四周擴散高濃度瘟疫毒環持續腐蝕' },
      { id: 'bone_prison', name: '骨牢尖刺', icon: '⛓️', cd: 3.6, dmg: 1.5, desc: '從地底刺出成排骨刺重創敵群' }
    ],
    trees: [
      { name: '亡靈大軍流', nodes: [
        { id: 'n_s1', name: '白骨強化', max: 3, desc: '全技能與召喚傷害 +12%/級', stat: 'skillDmg', val: 0.12 },
        { id: 'n_s2', name: '軍團擴編', max: 3, desc: '召喚與投射物數量 +1/級', stat: 'proj', val: 1 },
        { id: 'n_s3', name: '靈魂汲取', max: 3, desc: '擊殺回復生命 +2/級', stat: 'leech', val: 2 },
        { id: 'n_s4', name: '迅捷復甦', max: 3, desc: '冷卻縮減 +6%/級', stat: 'cdr', val: 0.06 },
        { id: 'n_s5', name: '骸骨護盾', max: 2, desc: '最大生命 +18%/級', stat: 'hpPct', val: 0.18 },
        { id: 'n_s6', name: '奧義：亡靈天災', max: 1, desc: '召喚/投射物 +2 且技能傷害 +30%', stat: 'cap_ns', val: 1 }
      ]},
      { name: '瘟疫屍爆流', nodes: [
        { id: 'n_c1', name: '腐蝕擴散', max: 3, desc: '技能範圍半徑 +13%/級', stat: 'area', val: 0.13 },
        { id: 'n_c2', name: '連環爆破', max: 3, desc: '全技能傷害 +13%/級', stat: 'skillDmg', val: 0.13 },
        { id: 'n_c3', name: '劇毒暴擊', max: 3, desc: '暴擊率 +5%/級', stat: 'crit', val: 0.05 },
        { id: 'n_c4', name: '死靈爆傷', max: 3, desc: '暴擊傷害 +22%/級', stat: 'critDmg', val: 0.22 },
        { id: 'n_c5', name: '瘟疫蔓延', max: 2, desc: '目標數 +1/級', stat: 'proj', val: 1 },
        { id: 'n_c6', name: '奧義：冥界大爆破', max: 1, desc: '範圍 +35%、技能傷害 +35%', stat: 'cap_nc', val: 1 }
      ]},
      { name: '衰老詛咒流', nodes: [
        { id: 'n_d1', name: '暗影精通', max: 3, desc: '冷卻縮減 +6%/級', stat: 'cdr', val: 0.06 },
        { id: 'n_d2', name: '咒怨纏身', max: 3, desc: '技能範圍 +11%/級', stat: 'area', val: 0.11 },
        { id: 'n_d3', name: '不死契約', max: 3, desc: '傷害減免 +5%/級', stat: 'dr', val: 0.05 },
        { id: 'n_d4', name: '靈魂收割', max: 3, desc: '攻擊力 +12%/級', stat: 'atkPct', val: 0.12 },
        { id: 'n_d5', name: '深淵低語', max: 2, desc: '全技能傷害 +18%/級', stat: 'skillDmg', val: 0.18 },
        { id: 'n_d6', name: '奧義：永夜冥王', max: 1, desc: '冷卻 +18%、吸血 +4、全傷 +25%', stat: 'cap_nd', val: 1 }
      ]}
    ]
  },
  paladin: {
    id: 'paladin', name: '聖騎士', icon: '🛡️', color: '#facc15', attackType: 'melee', dodgeName: '暗影步',
    desc: '近戰神聖重錘揮擊，兼具高護甲減傷與環繞聖光淨化',
    base: { hp: 165, atk: 14, spd: 185, crit: 0.07, cdr: 0.04 },
    skills: [
      { id: 'blessed_hammer', name: '祝福之錘', icon: '🔨', cd: 2.4, dmg: 1.2, desc: '召喚環繞自身螺旋飛舞的神聖戰錘' },
      { id: 'consecration', name: '奉獻光環', icon: '✨', cd: 3.4, dmg: 1.15, desc: '展開神聖火圈持續灼燒敵人並回復生命' },
      { id: 'fist_of_heavens', name: '天堂之拳', icon: '✝️', cd: 2.8, dmg: 1.5, desc: '降下聖光雷擊並四散射出神聖矢彈' },
      { id: 'shield_bash', name: '聖盾衝擊', icon: '🛡️', cd: 2.5, dmg: 1.35, desc: '震盪盾牌釋放神聖衝擊波擊退前方敵群' },
      { id: 'judgment_beam', name: '審判聖光', icon: '☀️', cd: 3.6, dmg: 1.55, desc: '從天降下淨化光柱灼燒範圍內魔物' },
      { id: 'holy_swords', name: '聖靈飛劍', icon: '⚔️', cd: 2.2, dmg: 1.15, desc: '召喚自動飛射穿刺敵人的聖光劍陣' },
      { id: 'smite_lightning', name: '雷霆懲戒', icon: '⚡', cd: 2.6, dmg: 1.3, desc: '神聖閃電連鎖劈擊多名敵人' }
    ],
    trees: [
      { name: '祝福神錘流', nodes: [
        { id: 'p_h1', name: '聖錘鍛造', max: 3, desc: '全技能傷害 +12%/級', stat: 'skillDmg', val: 0.12 },
        { id: 'p_h2', name: '多重聖錘', max: 3, desc: '戰錘與投射物數量 +1/級', stat: 'proj', val: 1 },
        { id: 'p_h3', name: '光輝擴散', max: 3, desc: '技能範圍 +11%/級', stat: 'area', val: 0.11 },
        { id: 'p_h4', name: '神聖律動', max: 3, desc: '冷卻縮減 +6%/級', stat: 'cdr', val: 0.06 },
        { id: 'p_h5', name: '裁決暴擊', max: 2, desc: '暴擊傷害 +25%/級', stat: 'critDmg', val: 0.25 },
        { id: 'p_h6', name: '奧義：天堂戰錘陣', max: 1, desc: '投射物 +2 且技能傷害 +30%', stat: 'cap_ph', val: 1 }
      ]},
      { name: '聖光奉獻流', nodes: [
        { id: 'p_c1', name: '聖潔之軀', max: 3, desc: '最大生命 +15%/級', stat: 'hpPct', val: 0.15 },
        { id: 'p_c2', name: '聖光庇佑', max: 3, desc: '傷害減免 +5%/級', stat: 'dr', val: 0.05 },
        { id: 'p_c3', name: '淨化烈焰', max: 3, desc: '技能範圍 +13%/級', stat: 'area', val: 0.13 },
        { id: 'p_c4', name: '恩典回復', max: 3, desc: '擊殺回復生命 +2/級', stat: 'leech', val: 2 },
        { id: 'p_c5', name: '耀眼光芒', max: 2, desc: '全技能傷害 +17%/級', stat: 'skillDmg', val: 0.17 },
        { id: 'p_c6', name: '奧義：永恆聖殿', max: 1, desc: '生命 +40%、範圍 +30%、減傷 +10%', stat: 'cap_pc', val: 1 }
      ]},
      { name: '神盾反擊流', nodes: [
        { id: 'p_s1', name: '重盾猛擊', max: 3, desc: '基礎與技能攻擊力 +12%/級', stat: 'atkPct', val: 0.12 },
        { id: 'p_s2', name: '神聖暴擊', max: 3, desc: '暴擊率 +5%/級', stat: 'crit', val: 0.05 },
        { id: 'p_s3', name: '懲戒電弧', max: 2, desc: '目標數 +1/級', stat: 'proj', val: 1 },
        { id: 'p_s4', name: '無畏衝鋒', max: 3, desc: '移動速度 +7%/級', stat: 'spdPct', val: 0.07 },
        { id: 'p_s5', name: '天罰之光', max: 2, desc: '冷卻縮減 +8%/級', stat: 'cdr', val: 0.08 },
        { id: 'p_s6', name: '奧義：大天使化身', max: 1, desc: '攻擊力 +35% 且全技能傷害 +30%', stat: 'cap_ps', val: 1 }
      ]}
    ]
  },
  kungfu: {
    id: 'kungfu', name: '中國古拳法大師', icon: '🥋', color: '#f8fafc', attackType: 'melee', dodgeName: '閃身步',
    desc: '光頭白衣灰褲武道宗師，近戰剛柔並濟拳腳連擊與獨特「閃身步」',
    base: { hp: 140, atk: 16, spd: 202, crit: 0.10, cdr: 0.05 },
    skills: [
      { id: 'langzi_kick', name: '浪子踢球', icon: '⚽', cd: 2.2, dmg: 1.45, desc: '旋踢發出高速旋轉金色內力氣球貫穿整排敵人並擊退' },
      { id: 'bear_shake', name: '狗熊哆嗦毛', icon: '🐻', cd: 3.2, dmg: 1.35, desc: '抖動筋骨爆發厚實震盪氣浪，震飛周圍魔物並護體' },
      { id: 'head_bob', name: '搖頭晃腦', icon: '🙃', cd: 2.8, dmg: 1.15, desc: '施展詭譎晃身步法，朝四周彈射多道高速寸勁氣彈' },
      { id: 'willow_step', name: '風擺柳碎步', icon: '🌿', cd: 2.5, dmg: 1.3, desc: '分出三道白衣殘影對周圍敵群發動高頻連環掌擊腿掃' },
      { id: 'monkey_flower', name: '猴子繞花線', icon: '🐒', cd: 3.0, dmg: 1.2, desc: '雙手穿花舞動，召喚∞字軌跡繞身盤旋的青金拳影' },
      { id: 'hot_foot_five', name: '燙腳五股', icon: '🔥', cd: 3.6, dmg: 1.55, desc: '連續五次高頻猛踏地面，擴散五圈赤金火勁震波' }
    ],
    trees: [
      { name: '剛猛腿勁流', nodes: [
        { id: 'k_l1', name: '鐵腿功', max: 3, desc: '拳腳與全技能傷害 +12%/級', stat: 'skillDmg', val: 0.12 },
        { id: 'k_l2', name: '連環蹴鞠', max: 3, desc: '氣勁球與震波目標數 +1/級', stat: 'proj', val: 1 },
        { id: 'k_l3', name: '烈火踏地', max: 3, desc: '技能範圍半徑 +12%/級', stat: 'area', val: 0.12 },
        { id: 'k_l4', name: '寸勁爆發', max: 3, desc: '暴擊傷害 +24%/級', stat: 'critDmg', val: 0.24 },
        { id: 'k_l5', name: '千斤墜', max: 2, desc: '基礎與技能攻擊力 +16%/級', stat: 'atkPct', val: 0.16 },
        { id: 'k_l6', name: '奧義：無影連環腿', max: 1, desc: '技能傷害 +35% 且目標數 +2', stat: 'cap_kl', val: 1 }
      ]},
      { name: '靈猴身法流', nodes: [
        { id: 'k_m1', name: '梯雲縱', max: 3, desc: '移動速度 +8%/級', stat: 'spdPct', val: 0.08 },
        { id: 'k_m2', name: '閃身無影', max: 3, desc: '全技能冷卻縮減 +6%/級', stat: 'cdr', val: 0.06 },
        { id: 'k_m3', name: '醉拳閃避', max: 3, desc: '閃避率 +5%/級', stat: 'dodge', val: 0.05 },
        { id: 'k_m4', name: '分身幻影', max: 2, desc: '技能投射物/殘影數 +1/級', stat: 'proj', val: 1 },
        { id: 'k_m5', name: '穿花疾手', max: 3, desc: '暴擊率 +5%/級', stat: 'crit', val: 0.05 },
        { id: 'k_m6', name: '奧義：萬花迷蹤步', max: 1, desc: '冷卻 +18%、閃避 +12%、移速 +20%', stat: 'cap_km', val: 1 }
      ]},
      { name: '熊虎硬氣功', nodes: [
        { id: 'k_b1', name: '金鐘罩', max: 3, desc: '最大生命 +15%/級', stat: 'hpPct', val: 0.15 },
        { id: 'k_b2', name: '鐵布衫', max: 3, desc: '傷害減免 +5%/級', stat: 'dr', val: 0.05 },
        { id: 'k_b3', name: '震山抖毛', max: 3, desc: '技能範圍 +12%/級', stat: 'area', val: 0.12 },
        { id: 'k_b4', name: '吐納回氣', max: 3, desc: '擊殺回復生命 +2/級', stat: 'leech', val: 2 },
        { id: 'k_b5', name: '內勁外放', max: 2, desc: '全技能傷害 +18%/級', stat: 'skillDmg', val: 0.18 },
        { id: 'k_b6', name: '奧義：混元金剛體', max: 1, desc: '生命 +40%、減傷 +10%、全傷 +25%', stat: 'cap_kb', val: 1 }
      ]}
    ]
  }
};

const TIER_THRESHOLDS = [0, 2, 5, 9, 14, 20];

const STAGES = [
  { id: 1, name: '第1關：幽暗古林', reqQuest: 1, dropLv: 1, bg: '#101814', grid: '#18261e', obsType: 'tree', hpMult: 1.0, dmgMult: 1.0, bossName: '腐木樹妖王' },
  { id: 2, name: '第2關：荒蕪石塚', reqQuest: 2, dropLv: 5, bg: '#17161d', grid: '#24222e', obsType: 'pillar', hpMult: 1.45, dmgMult: 1.25, bossName: '墓園守衛者' },
  { id: 3, name: '第3關：腐化毒沼', reqQuest: 3, dropLv: 10, bg: '#111a12', grid: '#1b2b1d', obsType: 'crate', hpMult: 2.0, dmgMult: 1.5, bossName: '瘟疫沼澤巨獸' },
  { id: 4, name: '第4關：沉沒聖堂', reqQuest: 4, dropLv: 15, bg: '#121722', grid: '#1c2436', obsType: 'wall', hpMult: 2.75, dmgMult: 1.8, bossName: '墮落大主教' },
  { id: 5, name: '第5關：永凍冰淵', reqQuest: 5, dropLv: 20, bg: '#0f1c26', grid: '#182d3d', obsType: 'crystal', hpMult: 3.7, dmgMult: 2.15, bossName: '霜骸冰霜巨龍' },
  { id: 6, name: '第6關：猩紅地牢', reqQuest: 6, dropLv: 25, bg: '#1f1014', grid: '#30181f', obsType: 'wall', hpMult: 4.9, dmgMult: 2.55, bossName: '血獄典獄長' },
  { id: 7, name: '第7關：熔岩煉獄', reqQuest: 7, dropLv: 30, bg: '#21120c', grid: '#361d13', obsType: 'obsidian', hpMult: 6.4, dmgMult: 3.0, bossName: '熔火炎魔領主' },
  { id: 8, name: '第8關：虛空裂隙',

