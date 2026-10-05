<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Chicken Road 2.0</title>
  <script src="https://telegram.org/js/telegram-web-app.js"></script>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      user-select: none;
      -webkit-tap-highlight-color: transparent;
    }
    body {
      background-color: #1a1e29;
      color: #fff;
      display: flex;
      flex-direction: column;
      align-items: center;
      min-height: 100vh;
      overflow-x: hidden;
    }

    /* Шапка */
    .top-bar {
      width: 100%;
      max-width: 440px;
      padding: 10px 14px;
      background: #11141c;
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 2px solid #232938;
      position: sticky;
      top: 0;
      z-index: 50;
    }
    .brand-logo {
      font-size: 16px;
      font-weight: 900;
      letter-spacing: 0.5px;
      color: #fff;
    }
    .brand-logo span { color: #f43f5e; }
    .top-right {
      display: flex;
      align-items: center;
      gap: 8px;
    }
    .btn-deposit-top {
      background: linear-gradient(135deg, #22c55e, #16a34a);
      color: white;
      border: none;
      padding: 6px 12px;
      border-radius: 8px;
      font-weight: 800;
      font-size: 12px;
      cursor: pointer;
    }
    .balance-chip {
      font-weight: 800;
      color: #38bdf8;
      font-size: 13px;
      background: #1e2638;
      padding: 5px 8px;
      border-radius: 6px;
      border: 1px solid #2d374d;
    }

    /* Дорога */
    .game-viewport {
      width: 100%;
      max-width: 440px;
      height: 280px;
      position: relative;
      background: #52525b;
      border-bottom: 3px solid #27272a;
      overflow: hidden;
      display: flex;
      align-items: center;
    }
    .sidewalk {
      width: 85px;
      height: 100%;
      background: #9ca3af;
      border-right: 4px solid #6b7280;
      position: absolute;
      left: 0;
      top: 0;
      z-index: 2;
    }
    .sidewalk-pattern {
      width: 100%;
      height: 100%;
      background: repeating-linear-gradient(0deg, transparent, transparent 35px, #6b7280 35px, #6b7280 37px);
    }
    .grass-edge {
      position: absolute;
      left: 0;
      top: 0;
      bottom: 0;
      width: 25px;
      background: #65a30d;
      border-right: 2px solid #4d7c0f;
    }
    .road-line {
      position: absolute;
      top: 50%;
      left: 0;
      right: 0;
      height: 4px;
      background: repeating-linear-gradient(90deg, #fff, #fff 25px, transparent 25px, transparent 50px);
      transform: translateY(-50%);
      opacity: 0.7;
    }
    .road-track {
      position: absolute;
      left: 85px;
      right: 0;
      top: 0;
      bottom: 0;
      display: flex;
      align-items: center;
      gap: 30px;
      padding-left: 25px;
      overflow-x: auto;
      scrollbar-width: none;
    }
    .road-track::-webkit-scrollbar { display: none; }

    .manhole {
      min-width: 86px;
      height: 86px;
      border-radius: 50%;
      background: radial-gradient(circle, #3f3f46 0%, #18181b 100%);
      border: 4px dashed #71717a;
      box-shadow: inset 0 0 12px rgba(0,0,0,0.9), 0 4px 10px rgba(0,0,0,0.5);
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: 900;
      font-size: 15px;
      color: #e4e4e7;
      z-index: 3;
      transition: all 0.3s;
    }
    .manhole.active {
      border: 4px solid #22c55e;
      box-shadow: 0 0 16px rgba(34, 197, 94, 0.7);
      color: #4ade80;
    }
    .manhole.cracked {
      background: radial-gradient(circle, #7f1d1d 0%, #450a0a 100%);
      border-color: #ef4444;
      color: #fca5a5;
    }

    .chicken-hero {
      position: absolute;
      font-size: 38px;
      z-index: 10;
      transition: all 0.35s cubic-bezier(0.34, 1.56, 0.64, 1);
      left: 36px;
      top: calc(50% - 24px);
    }

    /* Панель управления */
    .controls-panel {
      width: 100%;
      max-width: 440px;
      padding: 14px;
      background: #181d29;
      display: flex;
      flex-direction: column;
      gap: 12px;
      flex: 1;
    }
    .bet-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: #232a3b;
      padding: 10px 14px;
      border-radius: 12px;
      font-weight: 800;
      border: 1px solid #2f384d;
    }
    .bet-input-box {
      font-size: 17px;
      color: #facc15;
    }
    .quick-bets {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 8px;
    }
    .btn-qbet {
      background: #232a3b;
      color: #cbd5e1;
      border: 1px solid #2f384d;
      padding: 10px 0;
      border-radius: 10px;
      font-weight: 800;
      font-size: 13px;
    }
    .btn-qbet.selected {
      background: #3b82f6;
      border-color: #60a5fa;
      color: #fff;
    }
    .difficulty-select {
      background: #232a3b;
      color: #fff;
      border: 1px solid #2f384d;
      padding: 10px 14px;
      border-radius: 10px;
      font-weight: 700;
      font-size: 14px;
      outline: none;
    }
    .btn-action {
      background: #22c55e;
      color: #052e16;
      border: none;
      padding: 16px;
      border-radius: 14px;
      font-size: 18px;
      font-weight: 900;
      cursor: pointer;
      box-shadow: 0 4px 16px rgba(34, 197, 94, 0.4);
    }
    .btn-action.cashout {
      background: #f59e0b;
      color: #451a03;
      box-shadow: 0 4px 16px rgba(245, 158, 11, 0.4);
    }

    /* Модалка "Халява" */
    .p2p-modal-overlay {
      position: fixed;
      inset: 0;
      background: #11141c;
      z-index: 300;
      display: none;
      flex-direction: column;
      padding: 16px;
      overflow-y: auto;
      color: #fff;
    }
    .p2p-top {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding-bottom: 12px;
      font-size: 14px;
      font-weight: 700;
      color: #e2e8f0;
      border-bottom: 1px solid #222b3d;
    }
    .p2p-back {
      background: none;
      border: none;
      color: #facc15;
      font-size: 15px;
      font-weight: 700;
      cursor: pointer;
    }
    .p2p-title-info {
      text-align: center;
      margin: 16px 0;
      font-size: 13px;
      line-height: 1.5;
      color: #cbd5e1;
    }
    .p2p-card-box {
      background: #181d29;
      border: 1px solid #273145;
      border-radius: 14px;
      padding: 14px;
      margin-bottom: 14px;
    }
    .p2p-field {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 12px 0;
      border-bottom: 1px solid #222b3d;
    }
    .p2p-field:last-child { border-bottom: none; }
    .p2p-lbl { font-size: 12px; color: #94a3b8; }
    .p2p-val { font-size: 15px; font-weight: 800; color: #f8fafc; margin-top: 2px; }
    .p2p-info-box {
      background: #181d29;
      border: 1px solid #273145;
      border-radius: 12px;
      padding: 12px;
      font-size: 12px;
      line-height: 1.5;
      color: #94a3b8;
      margin-bottom: 16px;
    }
    .p2p-timer-bar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 13px;
      font-weight: 800;
      color: #94a3b8;
      margin: 10px 0 16px;
    }
    .p2p-time {
      color: #fff;
      font-size: 16px;
      font-family: monospace;
    }
    .p2p-btn-confirm {
      width: 100%;
      background: #ef4444;
      color: white;
      border: none;
      padding: 16px;
      border-radius: 12px;
      font-size: 16px;
      font-weight: 900;
      cursor: pointer;
      box-shadow: 0 4px 14px rgba(239, 68, 68, 0.4);
    }
  </style>
</head>
<body>

  <!-- Верхняя строка -->
  <div class="top-bar">
    <div class="brand-logo">CHICKEN<span>2</span>ROAD</div>
    <div class="top-right">
      <button class="btn-deposit-top" onclick="openFreeDeposit()">Пополнить</button>
      <div class="balance-chip" id="chipsDisplay">500.00 TJS</div>
    </div>
  </div>

  <!-- Дорога с люками -->
  <div class="game-viewport">
    <div class="sidewalk">
      <div class="grass-edge"></div>
      <div class="sidewalk-pattern"></div>
    </div>
    <div class="road-line"></div>
    <div class="chicken-hero" id="chicken">🐔</div>
    <div class="road-track" id="roadTrack">
      <div class="manhole" id="hole-0"><span>1.18x</span></div>
      <div class="manhole" id="hole-1"><span>1.46x</span></div>
      <div class="manhole" id="hole-2"><span>1.85x</span></div>
      <div class="manhole" id="hole-3"><span>2.40x</span></div>
      <div class="manhole" id="hole-4"><span>3.25x</span></div>
      <div class="manhole" id="hole-5"><span>4.80x</span></div>
      <div class="manhole" id="hole-6"><span>7.50x</span></div>
      <div class="manhole" id="hole-7"><span>12.0x</span></div>
    </div>
  </div>

  <!-- Панель управления -->
  <div class="controls-panel">
    <div class="bet-header">
      <span style="font-size: 12px; color: #94a3b8;">СТАВКА:</span>
      <div class="bet-input-box" id="betValueText">3 TJS</div>
      <span style="font-size: 12px; color: #94a3b8;">TJ</span>
    </div>

    <div class="quick-bets">
      <button class="btn-qbet" onclick="selectBet(2, this)">2 🪙</button>
      <button class="btn-qbet selected" onclick="selectBet(3, this)">3 🪙</button>
      <button class="btn-qbet" onclick="selectBet(8, this)">8 🪙</button>
      <button class="btn-qbet" onclick="selectBet(20, this)">20 🪙</button>
    </div>

    <select class="difficulty-select" id="diffSelect" onchange="changeDiff()">
      <option value="easy">Сложность: Легкий</option>
      <option value="medium" selected>Сложность: Средний</option>
      <option value="hard">Сложность: Сложный</option>
    </select>

    <button class="btn-action" id="actionBtn" onclick="handleGameClick()">Играть</button>
  </div>

  <!-- Окно "Халява" -->
  <div class="p2p-modal-overlay" id="freeDepositScreen">
    <div class="p2p-top">
      <button class="p2p-back" onclick="closeFreeDeposit()">‹ Назад</button>
      <span>Dushanbe Free Demo</span>
      <span onclick="closeFreeDeposit()" style="cursor:pointer; font-size: 18px;">✕</span>
    </div>

    <div class="p2p-title-info">
      Для пополнения демо-баланса нажми кнопку «ОПЛАТА ПРОВЕДЕНА» внизу экрана[span_2](start_span)[span_2](end_span).
    </div>

    <div class="p2p-card-box">
      <div class="p2p-field">
        <div>
          <div class="p2p-lbl">Тип пополнения</div>
          <div class="p2p-val">ХАЛЯВА 🔥</div>
        </div>
        <span>🎁</span>
      </div>

      <div class="p2p-field">
        <div>
          <div class="p2p-lbl">Сумма бонуса</div>
          <div class="p2p-val" style="color: #22c55e;">100 TJS</div>
        </div>
        <span>🪙</span>
      </div>

      <div class="p2p-field">
        <div>
          <div class="p2p-lbl">Получатель</div>
          <div class="p2p-val">Твой игровой профиль</div>
        </div>
        <span>👤</span>
      </div>
    </div>

    <div class="p2p-info-box">
      <b>Инструкция[span_3](start_span)[span_3](end_span):</b><br />
      Это бесплатный демо-режим для игры с друзьями. Нажми кнопку подтверждения ниже, и баланс мгновенно пополнится на 100 фишек!
    </div>

    <div class="p2p-timer-bar">
      <span>Осталось времени[span_4](start_span)[span_4](end_span):</span>
      <span class="p2p-time" id="demoTimer">18:28</span>
    </div>

    <button class="p2p-btn-confirm" onclick="confirmFreeDeposit()">ОПЛАТА ПРОВЕДЕНА</button>
  </div>

  <script>
    if (window.Telegram && window.Telegram.WebApp) {
      window.Telegram.WebApp.ready();
      window.Telegram.WebApp.expand();
    }

    const audio = new (window.AudioContext || window.webkitAudioContext)();
    function beep(freq, duration, type='sine') {
      try {
        const osc = audio.createOscillator();
        const g = audio.createGain();
        osc.type = type;
        osc.frequency.value = freq;
        osc.connect(g);
        g.connect(audio.destination);
        osc.start();
        gain.gain.exponentialRampToValueAtTime(0.0001, audio.currentTime + duration);
        osc.stop(audio.currentTime + duration);
      } catch(e){}
    }

    let chips = parseFloat(localStorage.getItem('chicken_chips')) || 500.0;
    let bet = 3.0;
    let step = -1;
    let isPlaying = false;
    let multipliers = [1.18, 1.46, 1.85, 2.40, 3.25, 4.80, 7.50, 12.0];

    const chicken = document.getElementById('chicken');
    const roadTrack = document.getElementById('roadTrack');
    const actionBtn = document.getElementById('actionBtn');

    function updateChips() {
      localStorage.setItem('chicken_chips', chips);
      document.getElementById('chipsDisplay').innerText = `${chips.toFixed(2)} TJS`;
    }
    updateChips();

    function selectBet(val, el) {
      if (isPlaying) return;
      document.querySelectorAll('.btn-qbet').forEach(b => b.classList.remove('selected'));
      el.classList.add('selected');
      bet = val;
      document.getElementById('betValueText').innerText = `${bet} TJS`;
      beep(350, 0.1);
    }

    function changeDiff() {
      if (isPlaying) return;
      const diff = document.getElementById('diffSelect').value;
      if (diff === 'easy') multipliers = [1.10, 1.25, 1.45, 1.75, 2.15, 2.80, 3.80, 5.50];
      if (diff === 'medium') multipliers = [1.18, 1.46, 1.85, 2.40, 3.25, 4.80, 7.50, 12.0];
      if (diff === 'hard') multipliers = [1.35, 1.95, 2.90, 4.50, 7.20, 12.5, 24.0, 50.0];
      for(let i=0; i<8; i++){
        document.querySelector(`#hole-${i} span`).innerText = `${multipliers[i]}x`;
      }
    }

    function handleGameClick() {
      if (!isPlaying) {
        if (chips < bet) {
          openFreeDeposit();
          return;
        }
        chips -= bet;
        updateChips();
        resetTrack();
        isPlaying = true;
        nextJump();
      } else {
        if (actionBtn.classList.contains('cashout')) {
          if (confirm(`Забрать выигрыш ${(bet * multipliers[step]).toFixed(2)} TJS?`)) {
            cashOut();
            return;
          }
        }
        nextJump();
      }
    }

    function nextJump() {
      step++;
      const risk = document.getElementById('diffSelect').value === 'hard' ? 0.30 : 0.20;
      const isTrap = step > 0 && Math.random() < risk;
      const currentHole = document.getElementById(`hole-${step}`);

      const holeRect = currentHole.getBoundingClientRect();
      const trackRect = roadTrack.getBoundingClientRect();
      const targetLeft = (holeRect.left - trackRect.left) + roadTrack.offsetLeft + 18;

      chicken.style.left = `${targetLeft}px`;
      beep(400 + step * 80, 0.15, 'triangle');

      if (step > 2) roadTrack.scrollLeft += 110;

      if (isTrap) {
        currentHole.classList.add('cracked');
        chicken.innerText = '💥';
        isPlaying = false;
        beep(140, 0.5, 'sawtooth');
        actionBtn.innerText = 'Упал! Заново 🔄';
        actionBtn.classList.remove('cashout');
      } else {
        currentHole.classList.add('active');
        const win = (bet * multipliers[step]).toFixed(2);
        actionBtn.innerText = `Забрать ${win} TJS 💰`;
        actionBtn.classList.add('cashout');

        if (step === 7) cashOut();
      }
    }

    function cashOut() {
      if (!isPlaying || step < 0) return;
      const win = bet * multipliers[step];
      chips += win;
      updateChips();
      beep(850, 0.4, 'sine');
      alert(`🎉 Вы забрали ${win.toFixed(2)} TJS!`);
      isPlaying = false;
      resetTrack();
    }

    function resetTrack() {
      step = -1;
      chicken.innerText = '🐔';
      chicken.style.left = '36px';
      roadTrack.scrollLeft = 0;
      actionBtn.innerText = 'Играть';
      actionBtn.classList.remove('cashout');
      document.querySelectorAll('.manhole').forEach(h => h.className = 'manhole');
    }

    /* Логика окна "Халява" */
    let timerInterval = null;
    function openFreeDeposit() {
      document.getElementById('freeDepositScreen').style.display = 'flex';
      let sec = 18 * 60 + 28;
      const tEl = document.getElementById('demoTimer');
      if (timerInterval) clearInterval(timerInterval);
      timerInterval = setInterval(() => {
        sec--;
        if (sec <= 0) { clearInterval(timerInterval); sec = 0; }
        const m = Math.floor(sec / 60);
        const s = sec % 60;
        tEl.innerText = `${m < 10 ? '0' : ''}${m}:${s < 10 ? '0' : ''}${s}`;
      }, 1000);
    }

    function closeFreeDeposit() {
      document.getElementById('freeDepositScreen').style.display = 'none';
      if (timerInterval) clearInterval(timerInterval);
    }

    function confirmFreeDeposit() {
      chips += 100.0;
      updateChips();
      beep(700, 0.25, 'sine');
      alert('🎉 Успешно! Вам начислено +100 TJS фишек!');
      closeFreeDeposit();
    }
  </script>
</body>
</html>
