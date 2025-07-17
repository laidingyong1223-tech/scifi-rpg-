# scifi-rpg-
我的科幻 RPG 遊戲
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <title>星際遺跡：Omega 任務</title>
  <style>
    body {
      font-family: 'Courier New', monospace;
      background-color: #000;
      color: #0f0;
      padding: 20px;
    }
    .hidden { display: none; }
    .choice-btn {
      margin-top: 10px;
      padding: 10px;
      background: #111;
      border: 1px solid #0f0;
      color: #0f0;
      cursor: pointer;
    }
    .choice-btn:hover {
      background: #0f0;
      color: #000;
    }

    /* 動畫效果區塊 */
    #effect-area {
      position: fixed;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      z-index: 999;
      pointer-events: none;
    }

    @keyframes flash-red {
      0%, 100% { background-color: transparent; }
      50% { background-color: rgba(255, 0, 0, 0.4); }
    }
    @keyframes heal-green {
      0%, 100% { background-color: transparent; }
      50% { background-color: rgba(0, 255, 0, 0.4); }
    }
    @keyframes glitch-purple {
      0%, 100% { background-color: transparent; }
      50% { background-color: rgba(200, 0, 255, 0.4); }
    }

    .flash-red {
      animation: flash-red 0.4s ease;
    }
    .heal-green {
      animation: heal-green 0.4s ease;
    }
    .glitch-purple {
      animation: glitch-purple 0.4s ease;
    }
  </style>
</head>
<body>

  <h1>🌌 星際遺跡：Omega 任務</h1>
  <div id="game"></div>
  <div id="effect-area"></div>

  <script>
    const game = document.getElementById('game');
    const effectArea = document.getElementById('effect-area');

    let player = {
      class: '',
      hp: 100,
      mp: 30,
      attack: 10,
      cooldown: 0,
    };

    let enemy = {
      hp: 40,
      canAttack: true
    };

    function triggerEffect(effectName) {
      effectArea.className = '';
      void effectArea.offsetWidth;
      effectArea.classList.add(effectName);
    }

    function startGame() {
      game.innerHTML = `
        <p>你是一名少年太空冒險者，抵達了一座失落的外星遺跡...</p>
        <p>選擇你的職業：</p>
        <button class="choice-btn" onclick="chooseClass('技師')">技師（能量修復）</button>
        <button class="choice-btn" onclick="chooseClass('戰士')">戰士（強力斬擊）</button>
        <button class="choice-btn" onclick="chooseClass('駭客')">駭客（系統干擾）</button>
      `;
    }

    function chooseClass(selectedClass) {
      player.class = selectedClass;
      if (selectedClass === '戰士') player.attack += 5;
      game.innerHTML = `
        <p>你選擇了：<strong>${player.class}</strong></p>
        <p>任務開始，你走入了第一個房間...</p>
        <button class="choice-btn" onclick="enterBattle()">繼續</button>
      `;
    }

    function enterBattle() {
      player.hp = 100;
      player.mp = 30;
      player.cooldown = 0;
      enemy.hp = 40;
      enemy.canAttack = true;
      renderBattle('');
    }

    function renderBattle(message) {
      const skillAvailable = player.cooldown === 0 && player.mp >= 10;
      const skillText = skillAvailable ? '' : `（冷卻或 MP 不足）`;
      game.innerHTML = `
        <p>👾 守衛機器人 HP：${enemy.hp}</p>
        <p>🧑 你的 HP：${player.hp} / MP：${player.mp}</p>
        <button class="choice-btn" onclick="attackEnemy()">普通攻擊</button>
        <button class="choice-btn" onclick="useSkill()" ${skillAvailable ? '' : 'disabled'}>使用技能 ${skillText}</button>
        ${message ? `<p>${message}</p>` : ''}
      `;
    }

    function attackEnemy() {
      enemy.hp -= player.attack;
      let log = `你造成了 ${player.attack} 傷害。`;

      if (enemy.hp <= 0) return winBattle();

      if (enemy.canAttack) {
        player.hp -= 8;
        log += ` 敵人反擊造成 8 傷害。`;
      } else {
        log += ` 敵人系統干擾中，無法攻擊。`;
        enemy.canAttack = true;
      }

      if (player.hp <= 0) return gameOver();
      if (player.cooldown > 0) player.cooldown--;

      renderBattle(log);
    }

    function useSkill() {
      let log = '';
      player.cooldown = 2;
      player.mp -= 10;

      if (player.class === '技師') {
        player.hp += 30;
        if (player.hp > 100) player.hp = 100;
        log = '💚 你使用了「能量修復」，回復了 30 HP。';
        triggerEffect('heal-green');

      } else if (player.class === '戰士') {
        let damage = player.attack * 2;
        enemy.hp -= damage;
        log = `💥 你使用了「強力斬擊」，造成 ${damage} 傷害！`;
        triggerEffect('flash-red');

      } else if (player.class === '駭客') {
        enemy.canAttack = false;
        log = '🟪 你使用了「系統干擾」，敵人下回合無法攻擊！';
        triggerEffect('glitch-purple');
      }

      if (enemy.hp <= 0) return winBattle();
      if (player.cooldown > 0) player.cooldown--;

      renderBattle(log);
    }

    function winBattle() {
      game.innerHTML = `
        <p>✅ 你擊敗了守衛機器人！</p>
        <p>你撿到了一個奇怪的能量核心。</p>
        <button class="choice-btn" onclick="startGame()">再玩一次</button>
      `;
    }

    function gameOver() {
      game.innerHTML = `
        <p>💀 你被守衛機器人擊敗了...</p>
        <p>任務失敗。</p>
        <button class="choice-btn" onclick="startGame()">重新開始</button>
      `;
    }

    startGame();
  </script>
</body>
</html>
