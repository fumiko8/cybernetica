# cybernetica
https://fumiko8.github.io/cybernetica/

<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>B-52 против Амиго: Погоня в городе</title>
    <style>
        * {
            user-select: none;
            -webkit-tap-highlight-color: transparent;
            box-sizing: border-box;
        }

        body {
            margin: 0;
            min-height: 100vh;
            background: linear-gradient(145deg, #0a1f1e 0%, #0c2a2a 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Segoe UI', 'Courier New', 'Courier', monospace;
            padding: 20px;
        }

        /* Главный контейнер */
        .game-container {
            background: #1e2a2a;
            border-radius: 48px;
            padding: 16px 20px 20px 20px;
            box-shadow: 0 20px 35px rgba(0,0,0,0.5), inset 0 1px 0 rgba(255,255,255,0.1);
            border: 1px solid #4e7b6e;
        }

        /* Сцены (комикс/игра) */
        .scene {
            transition: opacity 0.2s ease;
        }

        .hidden {
            display: none;
        }

        /* КОМИКС */
        .comic-viewer {
            background: #0f1919;
            border-radius: 32px;
            padding: 20px;
            box-shadow: inset 0 0 10px #00000033, 0 10px 20px rgba(0,0,0,0.3);
        }

        .comic-panel {
            display: flex;
            justify-content: center;
            align-items: center;
            background: #00000055;
            border-radius: 28px;
            padding: 10px;
        }

        .comic-img {
            max-width: 100%;
            max-height: 65vh;
            border-radius: 24px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.5);
            background: #2c3e2f;
            display: block;
            margin: 0 auto;
            border: 3px solid #ffd966;
        }

        .comic-controls {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-top: 25px;
            gap: 20px;
            flex-wrap: wrap;
        }

        .nav-btn {
            background: #2c3e2f;
            border: none;
            font-size: 2rem;
            font-weight: bold;
            color: #f5e7c8;
            width: 70px;
            padding: 12px 0;
            border-radius: 60px;
            cursor: pointer;
            transition: all 0.1s linear;
            box-shadow: 0 5px 0 #141e1a;
            font-family: monospace;
        }

        .nav-btn:active {
            transform: translateY(2px);
            box-shadow: 0 2px 0 #141e1a;
        }

        .page-indicator {
            background: #000000aa;
            backdrop-filter: blur(4px);
            padding: 10px 18px;
            border-radius: 40px;
            font-weight: bold;
            font-size: 1.2rem;
            color: #fadfaa;
        }

        .start-game-btn {
            background: #e2b23c;
            border: none;
            font-size: 1.7rem;
            font-weight: bold;
            padding: 12px 32px;
            border-radius: 80px;
            cursor: pointer;
            color: #1e2a1c;
            font-family: inherit;
            transition: 0.07s linear;
            box-shadow: 0 6px 0 #8b5a2b;
        }

        .start-game-btn:active {
            transform: translateY(3px);
            box-shadow: 0 2px 0 #8b5a2b;
        }

        /* ИГРОВАЯ ЗОНА — вид сверху-сбоку, стиль among us */
        .game-area {
            background: #1f3a33;
            border-radius: 32px;
            padding: 20px;
            position: relative;
        }

        canvas {
            display: block;
            margin: 0 auto;
            background: #213a34;
            border-radius: 24px;
            box-shadow: 0 0 0 4px #d4b87a, 0 12px 28px black;
            cursor: none;
        }

        .game-ui {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-top: 20px;
            background: #071312b3;
            backdrop-filter: blur(8px);
            padding: 12px 24px;
            border-radius: 60px;
            gap: 25px;
            flex-wrap: wrap;
        }

        .stats {
            display: flex;
            gap: 32px;
            font-weight: bold;
            font-size: 1.4rem;
            background: #00000066;
            padding: 8px 20px;
            border-radius: 40px;
        }

        .stat {
            color: #fee3a0;
        }

        .stat span {
            color: #d0ffb0;
            font-size: 1.7rem;
            margin-right: 5px;
        }

        .restart-btn {
            background: #6b3e1c;
            color: #ffecb3;
            border: none;
            padding: 8px 24px;
            border-radius: 32px;
            font-weight: bold;
            font-size: 1.2rem;
            cursor: pointer;
            transition: 0.05s linear;
            font-family: monospace;
        }

        .restart-btn:active {
            transform: scale(0.96);
        }

        .message-area {
            background: #010d0bc9;
            padding: 6px 16px;
            border-radius: 28px;
            font-size: 1rem;
            color: #f9e7b3;
        }

        @media (max-width: 750px) {
            .stats { gap: 12px; font-size: 1rem; }
            .stat span { font-size: 1.3rem; }
            .nav-btn { width: 55px; font-size: 1.5rem; }
        }
    </style>
</head>
<body>
<div class="game-container" id="appRoot">
    <!-- Сцена комикса -->
    <div id="comicScene" class="scene comic-viewer">
        <div class="comic-panel">
            <img id="comicImage" class="comic-img" alt="comic page" src="">
        </div>
        <div class="comic-controls">
            <button id="prevBtn" class="nav-btn">◀</button>
            <div class="page-indicator" id="pageIndicator">1 / 7</div>
            <button id="nextBtn" class="nav-btn">▶</button>
        </div>
        <div style="display: flex; justify-content: center; margin-top: 25px;">
            <button id="startGameFromComic" class="start-game-btn">🤖 НАЧАТЬ ИГРУ ⚡</button>
        </div>
    </div>

    <!-- Сцена игры -->
    <div id="gameScene" class="scene hidden game-area">
        <canvas id="gameCanvas" width="900" height="500"></canvas>
        <div class="game-ui">
            <div class="stats">
                <div class="stat">🔋 ЗАРЯД: <span id="batteryStat">100</span>%</div>
                <div class="stat">🏆 ПРЕСЛЕДОВАНИЕ: <span id="distanceStat">0</span> м</div>
            </div>
            <div class="message-area" id="gameMsg">⚡ Догони Амиго! Останавливайся у фонарей!</div>
            <button id="gameRestartBtn" class="restart-btn">🔄 РЕСТАРТ</button>
        </div>
    </div>
</div>

<script>
    (function(){
        // ------------------------------- КОМИКС ЛОГИКА ---------------------------------
        const TOTAL_COMICS = 7;
        let currentComicPage = 1;
        const comicImg = document.getElementById('comicImage');
        const prevBtn = document.getElementById('prevBtn');
        const nextBtn = document.getElementById('nextBtn');
        const pageIndicator = document.getElementById('pageIndicator');
        const comicScene = document.getElementById('comicScene');
        const gameScene = document.getElementById('gameScene');
        const startGameBtn = document.getElementById('startGameFromComic');

        // Функция загрузки PNG (комиксы должны лежать рядом: comic1.png ... comic7.png)
        function updateComicPage() {
            const fileName = `comic${currentComicPage}.png`;
            comicImg.src = fileName;
            pageIndicator.innerText = `${currentComicPage} / ${TOTAL_COMICS}`;
            // обработка ошибок: если картинка не загрузится, показать плейсхолдер (но по условию файлы есть)
            comicImg.onerror = () => {
                comicImg.src = 'data:image/svg+xml,%3Csvg%20xmlns%22%3E%3Crect%20width%3D%22400%22%20height%3D%22250%22%20fill%3D%22%233b5e4b%22/%3E%3Ctext%20x%3D%2250%25%22%20y%3D%2250%25%22%20fill%3D%22white%22%20text-anchor%3D%22middle%22%3Eкомикс%20' + fileName + '%20не%20найден%3C/text%3E%3C/svg%3E';
            };
        }

        function goPrev() {
            if (currentComicPage > 1) {
                currentComicPage--;
                updateComicPage();
            }
        }

        function goNext() {
            if (currentComicPage < TOTAL_COMICS) {
                currentComicPage++;
                updateComicPage();
            } else {
                // на последней странице, кнопка тоже работает но можно показать подсказку (не обязательно)
                if(currentComicPage === TOTAL_COMICS){
                    // можно визуально мигнуть, но ничего страшного
                }
            }
        }

        prevBtn.addEventListener('click', goPrev);
        nextBtn.addEventListener('click', goNext);
        
        // Загружаем первую страницу комикса
        updateComicPage();

        // ------------------------------- ПЕРЕХОД В ИГРУ ---------------------------------
        let gameInitialized = false;
        let gameLoopRunning = false;
        let animationId = null;
        let game = null; // объект игры будет создан при старте

        function stopGameLoop() {
            if (animationId) {
                cancelAnimationFrame(animationId);
                animationId = null;
            }
            gameLoopRunning = false;
        }

        function startGameModule() {
            if (game && typeof game.destroy === 'function') {
                game.destroy();
            }
            // Инициализируем заново игровой движок
            const canvas = document.getElementById('gameCanvas');
            if (!canvas) return;
            game = new ChaseGame(canvas);
            game.init();
            gameLoopRunning = true;
            function gameLoop() {
                if (!gameLoopRunning) return;
                if (game && game.update) {
                    game.update();
                }
                animationId = requestAnimationFrame(gameLoop);
            }
            if (animationId) cancelAnimationFrame(animationId);
            animationId = requestAnimationFrame(gameLoop);
        }

        // Кнопка начать игру (из комикса)
        startGameBtn.addEventListener('click', () => {
            // переключаем сцены
            comicScene.classList.add('hidden');
            gameScene.classList.remove('hidden');
            // если игра ещё не запущена или перезапускаем
            if (game) {
                stopGameLoop();
                game.destroy();
            }
            startGameModule();
        });

        // Рестарт внутри игры (кнопка)
        document.getElementById('gameRestartBtn').addEventListener('click', () => {
            if (game) {
                stopGameLoop();
                game.destroy();
                startGameModule();
            }
        });

        // ------------------------------------------------------------
        // ИГРОВОЙ ДВИЖОК: вид сверху-сбоку (2D top-down perspective как Among Us)
        // Главный герой B-52 догоняет Амиго. Нужно останавливаться у фонарей для подзарядки.
        // Механика: заряд со временем падает, если заряд <=0 -> проигрыш.
        // Прикосновение к фонарному столбу (круг зоны) и НЕПОДВИЖНОСТЬ в течение 0.6 сек повышает заряд.
        // Движение: WASD или стрелки. Амиго убегает вперед по городу (увеличивается дистанция).
        // Чем больше дистанция, тем сложнее? базовая скорость Амиго = 1.6, игрок ускоряется если заряд >20.
        // Фонари расставлены по карте (фиксированные точки). Игрок должен подходить к фонарю и стоять на месте.
        
        class ChaseGame {
            constructor(canvas) {
                this.canvas = canvas;
                this.ctx = canvas.getContext('2d');
                this.width = canvas.width;
                this.height = canvas.height;
                
                // игрок
                this.player = { x: this.width/2, y: this.height-70, radius: 18, speed: 3.4 };
                // цель (Амиго)
                this.enemy = { x: this.width/2, y: 70, radius: 18 };
                
                // дистанция преследования (метры, условно растет со временем)
                this.distance = 0;
                this.battery = 100;   // заряд %
                this.gameOver = false;
                this.win = false;
                
                // таймер разряда: заряд падает каждую секунду на 3 единицы (если игрок движется)
                // но если стоит у фонаря, то не падает и восстанавливается
                this.drainInterval = null;
                this.lastTimestamp = 0;
                this.lastFrameTime = 0;
                
                // управление
                this.keys = { ArrowUp: false, ArrowDown: false, ArrowLeft: false, ArrowRight: false, w: false, s: false, a: false, d: false };
                
                // Фонарные столбы (массив позиций и зона активации)
                this.lampPosts = [];
                this.generateLampPosts();
                
                // состояние зарядки: активная зарядка у фонаря
                this.isCharging = false;
                this.chargingTimer = 0;    // сколько кадров/сек стоит на месте у фонаря
                this.chargingRequiredFrames = 0.45; // ~0.45 секунды для начала зарядки (при 60fps ~27 кадров)
                this.chargingGainRate = 1.2; // % за кадр (при 60fps ~72% в секунду)
                this.currentLampNear = null;
                this.wasMoving = false;
                
                // сообщение
                this.message = "Двигайся WASD/стрелки. Стой у фонаря для зарядки!";
                this.messageTimer = 0;
                
                // частота обновления разряда через setInterval (для надежности)
                this.batteryDrainer = null;
                
                // привязка событий
                this.handleKeyDown = this.onKeyDown.bind(this);
                this.handleKeyUp = this.onKeyUp.bind(this);
                
                // скорость Амиго увеличивается с дистанцией
                this.enemyBaseSpeed = 0.8;
                // счётчик кадров для движения врага
                this.frameCounter = 0;
                this.enemyMoveAcc = 0;
            }
            
            generateLampPosts() {
                // 8 фонарей в разных местах карты, похоже на уличное освещение
                this.lampPosts = [
                    { x: 150, y: 100, radius: 28, active: true },
                    { x: 400, y: 180, radius: 28, active: true },
                    { x: 700, y: 130, radius: 28, active: true },
                    { x: 220, y: 320, radius: 28, active: true },
                    { x: 580, y: 380, radius: 28, active: true },
                    { x: 760, y: 280, radius: 28, active: true },
                    { x: 450, y: 430, radius: 28, active: true },
                    { x: 80, y: 440, radius: 28, active: true },
                    { x: 820, y: 440, radius: 28, active: true }
                ];
            }
            
            init() {
                this.bindControls();
                this.startBatteryDrain();
                this.gameOver = false;
                this.win = false;
                this.battery = 100;
                this.distance = 0;
                this.player.x = this.width/2;
                this.player.y = this.height-70;
                this.enemy.x = this.width/2;
                this.enemy.y = 70;
                this.isCharging = false;
                this.chargingTimer = 0;
                this.updateUI();
                this.showMessage("⚡ Догони Амиго! Подзарядка у фонарей (стой на месте) 🔋", 2.5);
            }
            
            bindControls() {
                window.addEventListener('keydown', this.handleKeyDown);
                window.addEventListener('keyup', this.handleKeyUp);
            }
            
            unbindControls() {
                window.removeEventListener('keydown', this.handleKeyDown);
                window.removeEventListener('keyup', this.handleKeyUp);
            }
            
            onKeyDown(e) {
                const key = e.key;
                if (this.gameOver || this.win) return;
                if (this.keys.hasOwnProperty(key)) this.keys[key] = true;
                if (key === 'w') this.keys.w = true;
                if (key === 's') this.keys.s = true;
                if (key === 'a') this.keys.a = true;
                if (key === 'd') this.keys.d = true;
                e.preventDefault();
            }
            
            onKeyUp(e) {
                const key = e.key;
                if (this.keys.hasOwnProperty(key)) this.keys[key] = false;
                if (key === 'w') this.keys.w = false;
                if (key === 's') this.keys.s = false;
                if (key === 'a') this.keys.a = false;
                if (key === 'd') this.keys.d = false;
                e.preventDefault();
            }
            
            startBatteryDrain() {
                if (this.batteryDrainer) clearInterval(this.batteryDrainer);
                this.batteryDrainer = setInterval(() => {
                    if (this.gameOver || this.win) return;
                    // если не заряжается активно и игрок движется (не стоит на месте)
                    let isMoving = this.isPlayerMoving();
                    if (!this.isCharging && isMoving && this.battery > 0) {
                        this.battery = Math.max(0, this.battery - 2.8);
                        if (this.battery <= 0) {
                            this.gameOver = true;
                            this.showMessage("❌ ЗАРЯД ИСЧЕРПАН! B-52 отключился... Амиго сбежал! ❌", 3);
                            this.clearDrainTimer();
                        }
                        this.updateUI();
                    } else if (!this.isCharging && !isMoving && this.battery > 0) {
                        // стоит без дела - небольшой штраф? нет, просто не теряет.
                    } else if (this.isCharging) {
                        // во время зарядки батарея не падает, а восстанавливается в update
                    }
                }, 420);
            }
            
            clearDrainTimer() {
                if (this.batteryDrainer) clearInterval(this.batteryDrainer);
                this.batteryDrainer = null;
            }
            
            isPlayerMoving() {
                let moveX = 0, moveY = 0;
                if (this.keys.ArrowLeft || this.keys.a) moveX = -1;
                if (this.keys.ArrowRight || this.keys.d) moveX = 1;
                if (this.keys.ArrowUp || this.keys.w) moveY = -1;
                if (this.keys.ArrowDown || this.keys.s) moveY = 1;
                return (moveX !== 0 || moveY !== 0);
            }
            
            updateMovement() {
                if (this.gameOver || this.win) return {dx:0, dy:0};
                let moveX = 0, moveY = 0;
                if (this.keys.ArrowLeft || this.keys.a) moveX = -1;
                if (this.keys.ArrowRight || this.keys.d) moveX = 1;
                if (this.keys.ArrowUp || this.keys.w) moveY = -1;
                if (this.keys.ArrowDown || this.keys.s) moveY = 1;
                if (moveX !== 0 || moveY !== 0) {
                    const len = Math.hypot(moveX, moveY);
                    moveX /= len; moveY /= len;
                }
                let currentSpeed = this.player.speed;
                if (this.battery < 15) currentSpeed *= 0.65;   // замедление при низком заряде
                else if (this.battery > 70) currentSpeed *= 1.1;
                let newX = this.player.x + moveX * currentSpeed;
                let newY = this.player.y + moveY * currentSpeed;
                // границы с учетом радиуса
                newX = Math.min(Math.max(newX, 20), this.width - 20);
                newY = Math.min(Math.max(newY, 20), this.height - 20);
                this.player.x = newX;
                this.player.y = newY;
                return {dx:moveX, dy:moveY};
            }
            
            updateCharging() {
                // проверить, стоит ли игрок в зоне фонаря
                let nearLamp = null;
                for (let lamp of this.lampPosts) {
                    const dx = this.player.x - lamp.x;
                    const dy = this.player.y - lamp.y;
                    const dist = Math.hypot(dx, dy);
                    if (dist < lamp.radius + 12) {
                        nearLamp = lamp;
                        break;
                    }
                }
                const moving = this.isPlayerMoving();
                if (nearLamp && !moving && !this.gameOver && !this.win && this.battery < 100) {
                    if (!this.isCharging) {
                        this.chargingTimer += 1/60;
                        if (this.chargingTimer >= this.chargingRequiredFrames) {
                            this.isCharging = true;
                            this.showMessage("🔌 ПОДЗАРЯДКА АКТИВНА! Стой на месте 🔋", 0.8);
                        }
                    } else {
                        // восстанавливаем батарею
                        this.battery = Math.min(100, this.battery + this.chargingGainRate);
                        this.updateUI();
                        if (this.battery >= 100) {
                            this.isCharging = false;
                            this.chargingTimer = 0;
                            this.showMessage("✅ ЗАРЯД ПОЛНОСТЬЮ ВОССТАНОВЛЕН!", 1);
                        }
                    }
                } else {
                    // сброс зарядки если ушли/двигаемся
                    this.isCharging = false;
                    this.chargingTimer = 0;
                }
                // если батарея 100 и стоит у столба, просто сбросить зарядку
                if (this.battery >= 100) this.isCharging = false;
            }
            
            updateEnemyAndDistance() {
                if (this.gameOver || this.win) return;
                // Амиго бежит вверх (увеличивая дистанцию) и немного меняет горизонталь
                let enemySpeed = this.enemyBaseSpeed + (this.distance / 450);
                enemySpeed = Math.min(enemySpeed, 2.6);
                // смещение в стороны для динамики
                let targetX = this.enemy.x + (Math.sin(Date.now() * 0.004) * 0.9);
                targetX = Math.min(Math.max(targetX, 40), this.width-40);
                this.enemy.x = targetX;
                this.enemy.y -= enemySpeed;
                if (this.enemy.y < 20) {
                    this.enemy.y = 25;
                    // увеличиваем пройденную дистанцию
                    this.distance += 8;
                    this.updateUI();
                }
                // если дистанция больше 500 метров — победа (догнал символически)
                if (this.distance >= 380 && !this.win && !this.gameOver) {
                    this.win = true;
                    this.showMessage("🏆 ПОБЕДА! B-52 догнал Амиго и вернул флешку! 🏆", 4);
                    this.clearDrainTimer();
                }
                // коллизия игрок-враг (если схватил)
                const distToEnemy = Math.hypot(this.player.x - this.enemy.x, this.player.y - this.enemy.y);
                if (distToEnemy < this.player.radius + this.enemy.radius && !this.gameOver && !this.win) {
                    this.win = true;
                    this.showMessage("🤝 Поймал! Флешка возвращена! B-52 победил! 🤖⚡", 3);
                    this.clearDrainTimer();
                }
                if(this.battery <= 0 && !this.gameOver){
                    this.gameOver = true;
                    this.showMessage("💀 Робот разрядился... Игра окончена 💀", 3);
                    this.clearDrainTimer();
                }
            }
            
            updateUI() {
                document.getElementById('batteryStat').innerText = Math.floor(this.battery);
                document.getElementById('distanceStat').innerText = Math.floor(this.distance);
            }
            
            showMessage(msg, durationSec = 2) {
                this.message = msg;
                this.messageTimer = durationSec;
                const msgDiv = document.getElementById('gameMsg');
                if(msgDiv) msgDiv.innerText = msg;
                setTimeout(() => {
                    if(document.getElementById('gameMsg') && this.message === msg) {
                        if(!this.gameOver && !this.win) document.getElementById('gameMsg').innerText = "⚡ Догони Амиго! Стой у фонарей для зарядки!";
                        else if(this.gameOver) document.getElementById('gameMsg').innerText = "❌ ИГРА ОКОНЧЕНА. Нажми РЕСТАРТ ❌";
                        else if(this.win) document.getElementById('gameMsg').innerText = "🏆 ПОБЕДА! Нажми рестарт чтобы играть снова 🏆";
                    }
                }, durationSec * 1000);
            }
            
            update() {
                if (this.gameOver || this.win) {
                    this.draw();
                    return;
                }
                this.updateMovement();
                this.updateCharging();
                this.updateEnemyAndDistance();
                if(this.battery <= 0) this.gameOver = true;
                this.draw();
                if(this.messageTimer > 0){
                    this.messageTimer -= 1/60;
                }
            }
            
            draw() {
                this.ctx.clearRect(0, 0, this.width, this.height);
                // Рисуем асфальт/газон в стиле among us
                this.ctx.fillStyle = '#2d5a4c';
                this.ctx.fillRect(0, 0, this.width, this.height);
                // линии дороги
                this.ctx.beginPath();
                this.ctx.strokeStyle = '#ffd966aa';
                this.ctx.lineWidth = 4;
                for(let i=0;i<12;i++) this.ctx.strokeRect(40+i*70,20,30,this.height-40);
                // фонарные столбы (круглая база + свет)
                for (let lamp of this.lampPosts) {
                    this.ctx.shadowBlur = 0;
                    this.ctx.fillStyle = '#6c7a5e';
                    this.ctx.beginPath();
                    this.ctx.arc(lamp.x, lamp.y, 12, 0, Math.PI*2);
                    this.ctx.fill();
                    this.ctx.fillStyle = '#f3c26b';
                    this.ctx.beginPath();
                    this.ctx.arc(lamp.x, lamp.y-5, 8, 0, Math.PI*2);
                    this.ctx.fill();
                    this.ctx.fillStyle = '#ffe6a3';
                    this.ctx.beginPath();
                    this.ctx.arc(lamp.x, lamp.y-3, 5, 0, Math.PI*2);
                    this.ctx.fill();
                    // зона зарядки
                    this.ctx.beginPath();
                    this.ctx.arc(lamp.x, lamp.y, 28, 0, Math.PI*2);
                    this.ctx.strokeStyle = '#aaffdd';
                    this.ctx.lineWidth = 2;
                    this.ctx.setLineDash([6, 8]);
                    this.ctx.stroke();
                    this.ctx.setLineDash([]);
                }
                // игрок B-52 (серый робот с антенной)
                this.ctx.fillStyle = '#4a6f7c';
                this.ctx.shadowBlur = 8;
                this.ctx.beginPath();
                this.ctx.arc(this.player.x, this.player.y, this.player.radius-2, 0, Math.PI*2);
                this.ctx.fill();
                this.ctx.fillStyle = '#aad4cf';
                this.ctx.beginPath();
                this.ctx.arc(this.player.x-4, this.player.y-4, 4, 0, Math.PI*2);
                this.ctx.arc(this.player.x+4, this.player.y-4, 4, 0, Math.PI*2);
                this.ctx.fill();
                this.ctx.fillStyle = '#000';
                this.ctx.beginPath();
                this.ctx.arc(this.player.x-4, this.player.y-4, 2, 0, Math.PI*2);
                this.ctx.arc(this.player.x+4, this.player.y-4, 2, 0, Math.PI*2);
                this.ctx.fill();
                this.ctx.fillStyle = '#f2bc5e';
                this.ctx.font = "bold 18 monospace";
                this.ctx.fillText("B-52", this.player.x-15, this.player.y-12);
                // враг Амиго
                this.ctx.fillStyle = '#c97e5a';
                this.ctx.beginPath();
                this.ctx.arc(this.enemy.x, this.enemy.y, this.enemy.radius-3, 0, Math.PI*2);
                this.ctx.fill();
                this.ctx.fillStyle = '#fff0b5';
                this.ctx.beginPath();
                this.ctx.arc(this.enemy.x-5, this.enemy.y-4, 3, 0, Math.PI*2);
                this.ctx.arc(this.enemy.x+5, this.enemy.y-4, 3, 0, Math.PI*2);
                this.ctx.fill();
                this.ctx.fillStyle = "#512e1a";
                this.ctx.fillText("AMIGO", this.enemy.x-15, this.enemy.y-12);
                if (this.isCharging) {
                    this.ctx.fillStyle = '#ffeb99';
                    this.ctx.font = "bold 18 monospace";
                    this.ctx.fillText("⚡ ЗАРЯДКА...", this.player.x-30, this.player.y-22);
                }
                if (this.gameOver) {
                    this.ctx.fillStyle = '#000000aa';
                    this.ctx.fillRect(0,0,this.width,this.height);
                    this.ctx.fillStyle = '#ffbc6e';
                    this.ctx.font = "32px monospace";
                    this.ctx.fillText("GAME OVER", this.width/2-100, this.height/2);
                }
                if (this.win) {
                    this.ctx.fillStyle = '#1a3f2edd';
                    this.ctx.fillRect(0,0,this.width,this.height);
                    this.ctx.fillStyle = '#ffdf8c';
                    this.ctx.font = "30px monospace";
                    this.ctx.fillText("VICTORY! ФЛЕШКА ВЕРНУТА!", this.width/2-160, this.height/2);
                }
                this.ctx.shadowBlur = 0;
            }
            
            destroy() {
                this.unbindControls();
                this.clearDrainTimer();
                if (this.batteryDrainer) clearInterval(this.batteryDrainer);
                this.gameOver = true;
            }
        }
    })();
</script>
</body>
</html>
