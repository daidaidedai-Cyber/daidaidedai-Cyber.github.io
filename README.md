<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>圆球冒险游戏 - 移动版</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-touch-callout: none;
            -webkit-user-select: none;
            -webkit-tap-highlight-color: transparent;
        }
        
        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Arial, sans-serif;
            overflow: hidden;
            position: fixed;
            width: 100%;
            height: 100%;
        }
        
        .game-container {
            position: relative;
            width: 100vw;
            height: 100vh;
            display: flex;
            flex-direction: column;
        }
        
        .game-header {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 2px 20px rgba(0,0,0,0.1);
            z-index: 100;
        }
        
        .hearts {
            font-size: 24px;
            color: #ff4757;
        }
        
        .score {
            font-size: 20px;
            font-weight: bold;
            color: #333;
        }
        
        .game-area {
            flex: 1;
            position: relative;
            overflow: hidden;
        }
        
        #gameCanvas {
            position: absolute;
            top: 0;
            left: 0;
            background-color: white;
            touch-action: none;
        }
        
        .controls {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 15px;
            z-index: 50;
        }
        
        .control-btn {
            width: 70px;
            height: 70px;
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.9);
            border: 3px solid #4CAF50;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
            font-weight: bold;
            color: #4CAF50;
            cursor: pointer;
            user-select: none;
            -webkit-user-select: none;
            transition: all 0.1s ease;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
        }
        
        .control-btn:active {
            transform: scale(0.9);
            background: rgba(76, 175, 80, 0.9);
            color: white;
        }
        
        .joystick-container {
            position: absolute;
            bottom: 30px;
            left: 30px;
            width: 150px;
            height: 150px;
            z-index: 50;
        }
        
        .joystick-base {
            position: absolute;
            width: 150px;
            height: 150px;
            background: rgba(255, 255, 255, 0.3);
            border-radius: 50%;
            border: 3px solid rgba(255, 255, 255, 0.5);
        }
        
        .joystick-stick {
            position: absolute;
            width: 70px;
            height: 70px;
            background: rgba(255, 255, 255, 0.9);
            border-radius: 50%;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            border: 3px solid #4CAF50;
            transition: none;
            box-shadow: 0 4px 15px rgba(0,0,0,0.3);
        }
        
        .game-over {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0, 0, 0, 0.9);
            display: none;
            align-items: center;
            justify-content: center;
            z-index: 200;
        }
        
        .game-over-content {
            background: white;
            padding: 40px;
            border-radius: 20px;
            text-align: center;
            max-width: 90%;
            box-shadow: 0 20px 40px rgba(0,0,0,0.3);
        }
        
        .game-over h2 {
            color: #ff4757;
            font-size: 32px;
            margin-bottom: 20px;
        }
        
        .final-score {
            font-size: 24px;
            color: #333;
            margin-bottom: 30px;
        }
        
        .restart-btn {
            background: linear-gradient(45deg, #4CAF50, #45a049);
            color: white;
            border: none;
            padding: 15px 40px;
            font-size: 20px;
            border-radius: 25px;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(76, 175, 80, 0.3);
        }
        
        .restart-btn:active {
            transform: scale(0.95);
        }
        
        .share-btn {
            background: linear-gradient(45deg, #1da1f2, #0d8bd9);
            color: white;
            border: none;
            padding: 12px 30px;
            font-size: 16px;
            border-radius: 20px;
            cursor: pointer;
            margin-top: 15px;
            transition: all 0.3s ease;
        }
        
        .orientation-warning {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0, 0, 0, 0.95);
            display: none;
            align-items: center;
            justify-content: center;
            z-index: 1000;
            color: white;
            text-align: center;
            padding: 20px;
        }
        
        @media (max-width: 600px) and (orientation: portrait) {
            .controls {
                bottom: 10px;
            }
            
            .control-btn {
                width: 60px;
                height: 60px;
                font-size: 20px;
            }
            
            .joystick-container {
                width: 120px;
                height: 120px;
                left: 10px;
                bottom: 10px;
            }
            
            .joystick-base {
                width: 120px;
                height: 120px;
            }
            
            .joystick-stick {
                width: 60px;
                height: 60px;
            }
        }
        
        @media (orientation: landscape) and (max-height: 500px) {
            .controls {
                display: none;
            }
            
            .joystick-container {
                bottom: 10px;
                left: 10px;
            }
        }
    </style>
</head>
<body>
    <div class="game-container">
        <div class="game-header">
            <div class="hearts" id="hearts">❤️❤️❤️❤️❤️❤️❤️❤️❤️❤️</div>
            <div class="score" id="score">积分: 0</div>
        </div>
        
        <div class="game-area">
            <canvas id="gameCanvas"></canvas>
            
            <!-- 虚拟摇杆 -->
            <div class="joystick-container" id="joystickContainer">
                <div class="joystick-base"></div>
                <div class="joystick-stick" id="joystickStick"></div>
            </div>
            
            <!-- 控制按钮 -->
            <div class="controls">
                <button class="control-btn" id="upBtn">↑</button>
                <button class="control-btn" id="leftBtn">←</button>
                <button class="control-btn" id="rightBtn">→</button>
                <button class="control-btn" id="downBtn">↓</button>
            </div>
        </div>
        
        <!-- 游戏结束界面 -->
        <div class="game-over" id="gameOver">
            <div class="game-over-content">
                <h2>游戏结束！</h2>
                <div class="final-score">最终积分: <span id="finalScore">0</span></div>
                <button class="restart-btn" id="restartBtn">重新开始</button>
                <br>
                <button class="share-btn" id="shareBtn">分享游戏</button>
            </div>
        </div>
        
        <!-- 横屏提示 -->
        <div class="orientation-warning" id="orientationWarning">
            <div>
                <h3>📱 请竖屏游玩</h3>
                <p>为了获得最佳游戏体验，请将设备旋转至竖屏模式</p>
            </div>
        </div>
    </div>

    <script>
        // 游戏核心代码
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const heartsDisplay = document.getElementById('hearts');
        const scoreDisplay = document.getElementById('score');
        const gameOverDiv = document.getElementById('gameOver');
        const finalScoreSpan = document.getElementById('finalScore');
        const restartBtn = document.getElementById('restartBtn');
        const shareBtn = document.getElementById('shareBtn');
        const orientationWarning = document.getElementById('orientationWarning');

        // 设置画布大小
        function resizeCanvas() {
            const gameArea = document.querySelector('.game-area');
            canvas.width = gameArea.clientWidth;
            canvas.height = gameArea.clientHeight;
        }
        resizeCanvas();
        window.addEventListener('resize', resizeCanvas);

        // 游戏状态
        let score = 0;
        let gameRunning = true;
        let player = {
            x: canvas.width / 2,
            y: canvas.height / 2,
            radius: 15,
            speed: 5,
            health: 10,
            color: '#0066cc'
        };

        // 控制状态
        const controls = {
            up: false, down: false, left: false, right: false
        };

        // 游戏对象数组
        let monsters = [];
        let bullets = [];
        let particles = [];

        // 摇杆控制
        const joystickContainer = document.getElementById('joystickContainer');
        const joystickStick = document.getElementById('joystickStick');
        let joystickActive = false;
        let joystickCenter = { x: 0, y: 0 };

        // 怪物类
        class Monster {
            constructor(x, y) {
                this.x = x;
                this.y = y;
                this.size = 25;
                this.speed = 1.5;
                this.color = '#ff8c00';
                this.shootCooldown = 0;
                this.isDying = false;
                this.dyingAnimation = 0;
            }

            update() {
                if (this.isDying) {
                    this.dyingAnimation++;
                    if (this.dyingAnimation > 20) return false;
                    return true;
                }

                const dx = player.x - this.x;
                const dy = player.y - this.y;
                const distance = Math.sqrt(dx * dx + dy * dy);

                if (distance > 0) {
                    this.x += (dx / distance) * this.speed;
                    this.y += (dy / distance) * this.speed;
                }

                if (this.shootCooldown > 0) this.shootCooldown--;
                if (this.shootCooldown === 0 && Math.random() < 0.02) {
                    this.shoot();
                    this.shootCooldown = 120;
                }
                return true;
            }

            shoot() {
                const dx = player.x - this.x;
                const dy = player.y - this.y;
                const distance = Math.sqrt(dx * dx + dy * dy);
                if (distance > 0) {
                    bullets.push(new Bullet(this.x, this.y, (dx / distance) * 2, (dy / distance) * 2));
                }
            }

            draw() {
                if (this.isDying) {
                    ctx.save();
                    ctx.globalAlpha = 1 - (this.dyingAnimation / 20);
                    const scale = 1 + (this.dyingAnimation / 20);
                    ctx.translate(this.x, this.y);
                    ctx.scale(scale, scale);
                    ctx.translate(-this.x, -this.y);
                }

                ctx.fillStyle = this.color;
                ctx.fillRect(this.x - this.size/2, this.y - this.size/2, this.size, this.size);

                if (this.isDying) ctx.restore();
            }

            startDying() {
                this.isDying = true;
                for (let i = 0; i < 8; i++) {
                    particles.push(new Particle(this.x, this.y));
                }
            }
        }

        // 子弹类
        class Bullet {
            constructor(x, y, vx, vy) {
                this.x = x; this.y = y; this.vx = vx; this.vy = vy;
                this.radius = 4; this.color = '#ff4500';
            }

            update() { this.x += this.vx; this.y += this.vy; }

            draw() {
                ctx.fillStyle = this.color;
                ctx.beginPath(); ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2); ctx.fill();
            }

            isOffScreen() {
                return this.x < 0 || this.x > canvas.width || this.y < 0 || this.y > canvas.height;
            }
        }

        // 粒子类
        class Particle {
            constructor(x, y) {
                this.x = x; this.y = y;
                this.vx = (Math.random() - 0.5) * 8;
                this.vy = (Math.random() - 0.5) * 8;
                this.size = Math.random() * 4 + 2;
                this.life = 30; this.maxLife = 30;
                this.color = '#ff8c00';
            }

            update() {
                this.x += this.vx; this.y += this.vy;
                this.vx *= 0.98; this.vy *= 0.98;
                this.life--;
                return this.life > 0;
            }

            draw() {
                ctx.save();
                ctx.globalAlpha = this.life / this.maxLife;
                ctx.fillStyle = this.color;
                ctx.beginPath(); ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2); ctx.fill();
                ctx.restore();
            }
        }

        // 摇杆控制函数
        function initJoystick() {
            const rect = joystickContainer.getBoundingClientRect();
            joystickCenter.x = rect.left + rect.width / 2;
            joystickCenter.y = rect.top + rect.height / 2;

            // 触摸事件
            joystickContainer.addEventListener('touchstart', handleJoystickStart, { passive: false });
            joystickContainer.addEventListener('touchmove', handleJoystickMove, { passive: false });
            joystickContainer.addEventListener('touchend', handleJoystickEnd, { passive: false });

            // 鼠标事件（用于测试）
            joystickContainer.addEventListener('mousedown', handleJoystickStart);
            joystickContainer.addEventListener('mousemove', handleJoystickMove);
            joystickContainer.addEventListener('mouseup', handleJoystickEnd);
            joystickContainer.addEventListener('mouseleave', handleJoystickEnd);
        }

        function handleJoystickStart(e) {
            e.preventDefault();
            joystickActive = true;
        }

        function handleJoystickMove(e) {
            if (!joystickActive) return;
            e.preventDefault();

            const touch = e.touches ? e.touches[0] : e;
            const rect = joystickContainer.getBoundingClientRect();
            const centerX = rect.left + rect.width / 2;
            const centerY = rect.top + rect.height / 2;
            
            let deltaX = touch.clientX - centerX;
            let deltaY = touch.clientY - centerY;
            
            const distance = Math.sqrt(deltaX * deltaX + deltaY * deltaY);
            const maxDistance = rect.width / 2 - 35;
            
            if (distance > maxDistance) {
                deltaX = (deltaX / distance) * maxDistance;
                deltaY = (deltaY / distance) * maxDistance;
            }
            
            joystickStick.style.transform = `translate(calc(-50% + ${deltaX}px), calc(-50% + ${deltaY}px))`;
            
            // 更新控制状态
            controls.left = deltaX < -20;
            controls.right = deltaX > 20;
            controls.up = deltaY < -20;
            controls.down = deltaY > 20;
        }

        function handleJoystickEnd(e) {
            e.preventDefault();
            joystickActive = false;
            joystickStick.style.transform = 'translate(-50%, -50%)';
            controls.left = controls.right = controls.up = controls.down = false;
        }

        // 按钮控制
        function initButtons() {
            const buttons = [
                { id: 'upBtn', control: 'up' },
                { id: 'downBtn', control: 'down' },
                { id: 'leftBtn', control: 'left' },
                { id: 'rightBtn', control: 'right' }
            ];

            buttons.forEach(({ id, control }) => {
                const btn = document.getElementById(id);
                if (btn) {
                    btn.addEventListener('touchstart', (e) => {
                        e.preventDefault();
                        controls[control] = true;
                    }, { passive: false });

                    btn.addEventListener('touchend', (e) => {
                        e.preventDefault();
                        controls[control] = false;
                    }, { passive: false });

                    // 鼠标事件（用于测试）
                    btn.addEventListener('mousedown', () => controls[control] = true);
                    btn.addEventListener('mouseup', () => controls[control] = false);
                }
            });
        }

        // 横屏检测
        function checkOrientation() {
            if (window.innerHeight < window.innerWidth && window.innerHeight < 500) {
                orientationWarning.style.display = 'flex';
            } else {
                orientationWarning.style.display = 'none';
            }
        }
        window.addEventListener('resize', checkOrientation);
        checkOrientation();

        // 更新玩家位置
        function updatePlayer() {
            if (controls.up && player.y - player.radius > 0) player.y -= player.speed;
            if (controls.down && player.y + player.radius < canvas.height) player.y += player.speed;
            if (controls.left && player.x - player.radius > 0) player.x -= player.speed;
            if (controls.right && player.x + player.radius < canvas.width) player.x += player.speed;
        }

        // 生成怪物
        function spawnMonster() {
            if (Math.random() < 0.02) {
                const angle = Math.random() * Math.PI * 2;
                const distance = 200 + Math.random() * 100;
                const x = player.x + Math.cos(angle) * distance;
                const y = player.y + Math.sin(angle) * distance;
                if (x > 0 && x < canvas.width && y > 0 && y < canvas.height) {
                    monsters.push(new Monster(x, y));
                }
            }
        }

        // 碰撞检测
        function checkCollisions() {
            for (let i = monsters.length - 1; i >= 0; i--) {
                const monster = monsters[i];
                if (monster.isDying) continue;
                const dx = player.x - monster.x;
                const dy = player.y - monster.y;
                const distance = Math.sqrt(dx * dx + dy * dy);
                if (distance < player.radius + monster.size / 2) {
                    monster.startDying();
                    score++;
                    updateScore();
                }
            }

            for (let i = bullets.length - 1; i >= 0; i--) {
                const bullet = bullets[i];
                const dx = player.x - bullet.x;
                const dy = player.y - bullet.y;
                const distance = Math.sqrt(dx * dx + dy * dy);
                if (distance < player.radius + bullet.radius) {
                    bullets.splice(i, 1);
                    player.health--;
                    updateHearts();
                    if (player.health <= 0) gameOver();
                }
            }
        }

        // 游戏结束
        function gameOver() {
            gameRunning = false;
            finalScoreSpan.textContent = score;
            gameOverDiv.style.display = 'flex';
        }

        // 重新开始
        function restartGame() {
            score = 0; gameRunning = true;
            player = { x: canvas.width / 2, y: canvas.height / 2, radius: 15, speed: 5, health: 10, color: '#0066cc' };
            monsters = []; bullets = []; particles = [];
            updateScore(); updateHearts();
            gameOverDiv.style.display = 'none';
            gameLoop();
        }

        // 更新显示
        function updateHearts() {
            let hearts = '';
            for (let i = 0; i < player.health; i++) hearts += '❤️';
            heartsDisplay.textContent = hearts;
        }

        function updateScore() {
            scoreDisplay.textContent = '积分: ' + score;
        }

        // 分享功能
        function shareGame() {
            const gameUrl = window.location.href;
            if (navigator.share) {
                navigator.share({
                    title: '圆球冒险游戏',
                    text: `我在圆球冒险游戏中获得了${score}分！快来挑战吧！`,
                    url: gameUrl
                });
            } else {
                navigator.clipboard.writeText(gameUrl).then(() => {
                    alert('游戏链接已复制到剪贴板！');
                });
            }
        }

        // 游戏循环
        function gameLoop() {
            if (!gameRunning) return;
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            updatePlayer();
            spawnMonster();

            for (let i = monsters.length - 1; i >= 0; i--) {
                if (!monsters[i].update()) monsters.splice(i, 1);
            }

            for (let i = bullets.length - 1; i >= 0; i--) {
                bullets[i].update();
                if (bullets[i].isOffScreen()) bullets.splice(i, 1);
            }

            for (let i = particles.length - 1; i >= 0; i--) {
                if (!particles[i].update()) particles.splice(i, 1);
            }

            checkCollisions();

            // 绘制游戏对象
            ctx.fillStyle = player.color;
            ctx.beginPath(); ctx.arc(player.x, player.y, player.radius, 0, Math.PI * 2); ctx.fill();
            monsters.forEach(monster => monster.draw());
            bullets.forEach(bullet => bullet.draw());
            particles.forEach(particle => particle.draw());

            requestAnimationFrame(gameLoop);
        }

        // 初始化
        initJoystick();
        initButtons();
        restartBtn.addEventListener('click', restartGame);
        shareBtn.addEventListener('click', shareGame);

        // 防止默认触摸行为
        document.addEventListener('touchmove', (e) => {
            e.preventDefault();
        }, { passive: false });

        // 开始游戏
        gameLoop();
    </script>
</body>
</html>
