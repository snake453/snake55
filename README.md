<!DOCTYPE html>
<html>
<head>
<title>Page Title</title>
</head>
<body>

<h1>This is a Heading</h1>
<p>This is a paragraph.</p>

</body>
</html>
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>霓虹貪吃蛇（音效增強版）</title>
    <style>
        :root {
            --snake-head: #34C759; /* 蛇頭主色 */
            --snake-body-start: #34C759; /* 蛇身起始色 */
            --snake-body-end: #009688; /* 蛇身結束色 */
            --food-color: #FF3B30; /* 食物主色 */
            --food-glow: rgba(255, 59, 48, 0.6); /* 食物發光 */
            --bg-color: #0A0E17; /* 背景色 */
            --grid-color: rgba(255, 255, 255, 0.05); /* 網格色 */
            --border-color: #2D3748; /* 邊框色 */
            --neon-glow: rgba(52, 199, 89, 0.4); /* 霓虹發光 */
            --text-primary: #FFFFFF; /* 主要文字色 */
            --text-secondary: #A0AEC0; /* 次要文字色 */
            --button-hover: #30D158; /* 按鈕懸停色 */
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            padding: 20px;
            overflow-x: hidden;
        }

        /* 標題美化 */
        .game-title {
            font-size: 2.5rem;
            font-weight: 700;
            margin-bottom: 15px;
            background: linear-gradient(90deg, var(--snake-head), var(--snake-body-end));
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            text-shadow: 0 0 15px var(--neon-glow);
            letter-spacing: 1px;
        }

        /* 遊戲容器 */
        .game-container {
            position: relative;
            border-radius: 12px;
            overflow: hidden;
            box-shadow: 0 0 30px rgba(0, 0, 0, 0.5), 0 0 15px var(--neon-glow);
            border: 2px solid var(--border-color);
            transition: all 0.3s ease;
        }

        .game-container:hover {
            box-shadow: 0 0 40px rgba(0, 0, 0, 0.6), 0 0 20px var(--neon-glow);
        }

        /* 遊戲畫布 */
        #game-board {
            background-color: var(--bg-color);
            /* 網格背景 */
            background-image: 
                linear-gradient(to right, var(--grid-color) 1px, transparent 1px),
                linear-gradient(to bottom, var(--grid-color) 1px, transparent 1px);
            background-size: 20px 20px;
        }

        /* 分數面板 */
        .score-panel {
            display: flex;
            gap: 40px;
            margin: 20px 0;
            padding: 12px 30px;
            background-color: rgba(255, 255, 255, 0.05);
            border-radius: 30px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
        }

        .score-item {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .score-label {
            font-size: 1rem;
            color: var(--text-secondary);
        }

        .score-value {
            font-size: 1.4rem;
            font-weight: 600;
            color: var(--text-primary);
            transition: transform 0.2s ease;
        }

        .score-value.pop {
            transform: scale(1.2);
        }

        /* 音效控制按鈕 */
        .sound-control {
            margin-left: 20px;
            padding: 8px 16px;
            background-color: rgba(255, 255, 255, 0.1);
            border-radius: 20px;
            border: 1px solid rgba(255, 255, 255, 0.2);
            color: var(--text-primary);
            cursor: pointer;
            transition: all 0.3s ease;
            font-size: 0.9rem;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .sound-control:hover {
            background-color: rgba(255, 255, 255, 0.15);
        }

        /* 遊戲結束畫面 */
        .game-over {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(10, 14, 23, 0.85);
            backdrop-filter: blur(8px);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            z-index: 10;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.5s ease;
        }

        .game-over.active {
            opacity: 1;
            pointer-events: all;
        }

        .game-over-title {
            font-size: 2.2rem;
            font-weight: 700;
            color: var(--food-color);
            margin-bottom: 10px;
            text-shadow: 0 0 10px var(--food-glow);
            animation: pulse 1.5s infinite;
        }

        .game-over-score {
            font-size: 1.3rem;
            color: var(--text-primary);
            margin-bottom: 30px;
            padding: 10px 20px;
            background-color: rgba(255, 255, 255, 0.08);
            border-radius: 20px;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        /* 按鈕美化 */
        .restart-btn {
            padding: 14px 36px;
            font-size: 1.1rem;
            font-weight: 600;
            color: white;
            background-color: var(--snake-head);
            border: none;
            border-radius: 30px;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px var(--neon-glow);
        }

        .restart-btn:hover {
            background-color: var(--button-hover);
            transform: translateY(-3px);
            box-shadow: 0 6px 20px var(--neon-glow);
        }

        .restart-btn:active {
            transform: translateY(1px);
        }

        /* 控制說明 */
        .controls {
            margin-top: 15px;
            font-size: 0.9rem;
            color: var(--text-secondary);
            text-align: center;
            max-width: 300px;
            line-height: 1.5;
        }

        /* 響應式調整 */
        @media (max-width: 640px) {
            .game-title {
                font-size: 2rem;
            }

            #game-board {
                width: 100%;
                height: calc(100vw * 0.7); /* 保持比例 */
            }

            .score-panel {
                flex-wrap: wrap;
                gap: 15px;
                padding: 10px 15px;
                width: 100%;
                justify-content: center;
            }

            .sound-control {
                margin-left: 0;
                margin-top: 10px;
            }

            .game-over-title {
                font-size: 1.8rem;
            }
        }

        /* 動畫關鍵幀 */
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }

        @keyframes foodGlow {
            0% { box-shadow: 0 0 5px var(--food-glow); }
            50% { box-shadow: 0 0 15px var(--food-glow), 0 0 20px var(--food-glow); }
            100% { box-shadow: 0 0 5px var(--food-glow); }
        }
    </style>
</head>
<body>
    <h1 class="game-title">霓虹貪吃蛇</h1>
    <div class="game-container">
        <canvas id="game-board" width="600" height="400"></canvas>
        <div class="game-over" id="game-over">
            <h2 class="game-over-title">遊戲結束！</h2>
            <p class="game-over-score">最終分數: <span id="final-score">0</span></p>
            <button class="restart-btn" onclick="restartGame()">再玩一次</button>
        </div>
    </div>
    <div class="score-panel">
        <div class="score-item">
            <span class="score-label">分數</span>
            <span class="score-value" id="score">0</span>
        </div>
        <div class="score-item">
            <span class="score-label">長度</span>
            <span class="score-value" id="length">1</span>
        </div>
        <!-- 音效控制按鈕 -->
        <button class="sound-control" id="sound-toggle" onclick="toggleSound()">
            🔊 音效開
        </button>
    </div>
    <div class="controls">
        操作：方向鍵控制移動 | 吃到食物加分 | 碰到邊界/自己結束
    </div>

    <script>
        // 遊戲核心設定
        const canvas = document.getElementById('game-board');
        const ctx = canvas.getContext('2d');
        const scoreElement = document.getElementById('score');
        const lengthElement = document.getElementById('length');
        const gameOverElement = document.getElementById('game-over');
        const finalScoreElement = document.getElementById('final-score');
        const soundToggleBtn = document.getElementById('sound-toggle');

        const BLOCK_SIZE = 20;
        const SPEED = 130; // 移動速度（毫秒/步）
        let WIDTH = canvas.width;
        let HEIGHT = canvas.height;

        // 音效設定（使用CDN音效文件，無需本地文件）
        const sounds = {
            move: new Audio('https://cdn.freesound.org/previews/501/501670_10226572-lq.mp3'), // 移動聲
            eat: new Audio('https://cdn.freesound.org/previews/412/412660_7320501-lq.mp3'),   // 吃食物聲
            gameOver: new Audio('https://cdn.freesound.org/previews/342/342746_6203784-lq.mp3'), // 結束聲
            restart: new Audio('https://cdn.freesound.org/previews/514/514603_10226572-lq.mp3') // 重新開始聲
        };

        // 音效音量控制（0-1之間，可調整）
        const soundVolumes = {
            move: 0.3,   // 移動聲音量（較小，避免擾人）
            eat: 0.7,    // 吃食物聲音量（較大，強調正反饋）
            gameOver: 0.6,
            restart: 0.5
        };

        // 初始化音效音量
        Object.keys(sounds).forEach(key => {
            sounds[key].volume = soundVolumes[key];
            sounds[key].preload = 'auto'; // 預加載音效，避免延遲
        });

        // 音效開關狀態
        let isSoundEnabled = true;

        // 遊戲狀態（使用物件統一管理）
        let gameState = {
            snake: [],
            direction: { x: 0, y: 0 },
            food: null,
            score: 0,
            gameLoopId: null,
            animationFrameId: null,
            isGameRunning: false,
            lastMoveSoundTime: 0 // 防止移動音效重疊
        };

        // 初始化遊戲狀態
        function initGameState() {
            gameState.snake = [
                { x: WIDTH / 2, y: HEIGHT / 2 }
            ];
            gameState.direction = { x: 0, y: 0 };
            gameState.food = generateFood();
            gameState.score = 0;
            gameState.gameLoopId = null;
            gameState.animationFrameId = null;
            gameState.isGameRunning = false;
            gameState.lastMoveSoundTime = 0;

            // 更新DOM顯示
            scoreElement.textContent = gameState.score;
            lengthElement.textContent = gameState.snake.length;
            gameOverElement.classList.remove('active');
        }

        // 生成食物（避免重疊蛇身）
        function generateFood() {
            let newFood;
            while (!newFood || gameState.snake.some(seg => seg.x === newFood.x && seg.y === newFood.y)) {
                newFood = {
                    x: Math.floor(Math.random() * (WIDTH / BLOCK_SIZE)) * BLOCK_SIZE,
                    y: Math.floor(Math.random() * (HEIGHT / BLOCK_SIZE)) * BLOCK_SIZE
                };
            }
            return newFood;
        }

        // 播放音效（帶開關控制）
        function playSound(soundKey) {
            if (!isSoundEnabled) return;
            const sound = sounds[soundKey];
            if (sound) {
                // 移動音效特殊處理：避免連續播放重疊
                if (soundKey === 'move') {
                    const now = Date.now();
                    if (now - gameState.lastMoveSoundTime > 100) { // 100毫秒內只播放一次
                        sound.currentTime = 0; // 重置播放位置
                        sound.play().catch(e => console.log('音效播放失敗:', e));
                        gameState.lastMoveSoundTime = now;
                    }
                } else {
                    sound.currentTime = 0; // 重置播放位置（支持連續觸發）
                    sound.play().catch(e => console.log('音效播放失敗:', e));
                }
            }
        }

        // 切換音效開關
        function toggleSound() {
            isSoundEnabled = !isSoundEnabled;
            soundToggleBtn.innerHTML = isSoundEnabled ? '🔊 音效開' : '🔇 音效關';
        }

        // 計算蛇身漸變色
        function getSnakeColor(index) {
            const totalSegments = gameState.snake.length;
            const ratio = index / totalSegments;
            const start = varColor('--snake-body-start');
            const end = varColor('--snake-body-end');
            const r = Math.round((end.r - start.r) * ratio + start.r);
            const g = Math.round((end.g - start.g) * ratio + start.g);
            const b = Math.round((end.b - start.b) * ratio + start.b);
            return `rgb(${r}, ${g}, ${b})`;
        }

        // 解析 CSS 變量顏色為 RGB
        function varColor(varName) {
            const color = getComputedStyle(document.documentElement).getPropertyValue(varName).trim();
            const hexMatch = color.match(/^#([0-9a-f]{2})([0-9a-f]{2})([0-9a-f]{2})$/i);
            if (hexMatch) {
                return {
                    r: parseInt(hexMatch[1], 16),
                    g: parseInt(hexMatch[2], 16),
                    b: parseInt(hexMatch[3], 16)
                };
            }
            return { r: 0, g: 0, b: 0 };
        }

        // 繪製元素
        function draw() {
            // 清空畫布（半透明實現拖影效果）
            ctx.fillStyle = 'rgba(10, 14, 23, 0.2)';
            ctx.fillRect(0, 0, WIDTH, HEIGHT);

            // 繪製蛇（漸變色 + 陰影）
            gameState.snake.forEach((segment, index) => {
                ctx.fillStyle = getSnakeColor(index);
                // 蛇頭特殊樣式（更大陰影）
                if (index === 0) {
                    ctx.shadowColor = varColor('--snake-head');
                    ctx.shadowBlur = 10;
                } else {
                    ctx.shadowBlur = 5;
                }
                // 圓角方塊（美化蛇身）
                roundedRect(ctx, segment.x, segment.y, BLOCK_SIZE - 2, BLOCK_SIZE - 2, 4);
                ctx.fill();
                ctx.shadowBlur = 0; // 重置陰影
            });

            // 繪製食物（閃爍動畫 + 發光）
            ctx.fillStyle = getComputedStyle(document.documentElement).getPropertyValue('--food-color');
            ctx.shadowColor = getComputedStyle(document.documentElement).getPropertyValue('--food-glow');
            ctx.shadowBlur = 15;
            roundedRect(ctx, gameState.food.x, gameState.food.y, BLOCK_SIZE - 2, BLOCK_SIZE - 2, 8);
            ctx.fill();
            ctx.shadowBlur = 0;

            // 只有遊戲運行時才繼續請求動畫幀
            if (gameState.isGameRunning) {
                gameState.animationFrameId = requestAnimationFrame(draw);
            }
        }

        // 繪製圓角矩形
        function roundedRect(ctx, x, y, width, height, radius) {
            ctx.beginPath();
            ctx.moveTo(x + radius, y);
            ctx.lineTo(x + width - radius, y);
            ctx.quadraticCurveTo(x + width, y, x + width, y + radius);
            ctx.lineTo(x + width, y + height - radius);
            ctx.quadraticCurveTo(x + width, y + height, x + width - radius, y + height);
            ctx.lineTo(x + radius, y + height);
            ctx.quadraticCurveTo(x, y + height, x, y + height - radius);
            ctx.lineTo(x, y + radius);
            ctx.quadraticCurveTo(x, y, x + radius, y);
            ctx.closePath();
        }

        // 更新遊戲狀態
        function update() {
            // 計算新蛇頭
            const head = { 
                x: gameState.snake[0].x + gameState.direction.x * BLOCK_SIZE, 
                y: gameState.snake[0].y + gameState.direction.y * BLOCK_SIZE 
            };
            gameState.snake.unshift(head);

            // 播放移動音效（只有移動時才播放）
            if (gameState.direction.x !== 0 || gameState.direction.y !== 0) {
                playSound('move');
            }

            // 吃到食物判斷
            if (head.x === gameState.food.x && head.y === gameState.food.y) {
                gameState.score += 10;
                updateScoreDisplay();
                gameState.food = generateFood();
                playSound('eat'); // 播放吃食物音效
            } else {
                gameState.snake.pop();
            }

            // 碰撞檢測
            if (
                head.x < 0 || head.x >= WIDTH ||
                head.y < 0 || head.y >= HEIGHT ||
                gameState.snake.slice(1).some(seg => seg.x === head.x && seg.y === head.y)
            ) {
                endGame();
                return; // 碰撞後不再繼續計時
            }

            // 繼續遊戲迴圈
            gameState.gameLoopId = setTimeout(update, SPEED);
        }

        // 分數顯示（帶跳動動畫）
        function updateScoreDisplay() {
            scoreElement.textContent = gameState.score;
            lengthElement.textContent = gameState.snake.length;
            
            // 跳動動畫
            scoreElement.classList.add('pop');
            lengthElement.classList.add('pop');
            setTimeout(() => {
                scoreElement.classList.remove('pop');
                lengthElement.classList.remove('pop');
            }, 200);
        }

        // 遊戲主迴圈
        function gameLoop() {
            if (gameState.isGameRunning) return; // 防止重複啟動
            gameState.isGameRunning = true;
            update();
            draw();
        }

        // 結束遊戲
        function endGame() {
            gameState.isGameRunning = false;
            // 清除計時器和動畫幀
            clearTimeout(gameState.gameLoopId);
            cancelAnimationFrame(gameState.animationFrameId);
            // 播放遊戲結束音效
            playSound('gameOver');
            // 顯示結束畫面
            finalScoreElement.textContent = gameState.score;
            gameOverElement.classList.add('active');
        }

        // 重新開始遊戲
        function restartGame() {
            // 1. 先清除所有殘留的計時器和動畫幀
            clearTimeout(gameState.gameLoopId);
            cancelAnimationFrame(gameState.animationFrameId);
            
            // 2. 重置畫布（避免殘影）
            ctx.clearRect(0, 0, WIDTH, HEIGHT);
            
            // 3. 播放重新開始音效
            playSound('restart');
            
            // 4. 重新初始化遊戲狀態
            initGameState();
            
            // 5. 啟動新遊戲
            gameLoop();
        }

        // 鍵盤控制（避免反向移動）
        document.addEventListener('keydown', (e) => {
            switch (e.key) {
                case 'ArrowUp':
                    if (gameState.direction.y === 0) gameState.direction = { x: 0, y: -1 };
                    break;
                case 'ArrowDown':
                    if (gameState.direction.y === 0) gameState.direction = { x: 0, y: 1 };
                    break;
                case 'ArrowLeft':
                    if (gameState.direction.x === 0) gameState.direction = { x: -1, y: 0 };
                    break;
                case 'ArrowRight':
                    if (gameState.direction.x === 0) gameState.direction = { x: 1, y: 0 };
                    break;
                case 'm': // 快捷鍵：按 M 切換音效
                    toggleSound();
                    break;
            }
        });

        // 窗口大小變化時調整畫布（響應式）
        window.addEventListener('resize', () => {
            if (window.innerWidth < 640) {
                canvas.width = window.innerWidth - 40;
                canvas.height = (window.innerWidth - 40) * 0.7;
            } else {
                canvas.width = 600;
                canvas.height = 400;
            }
            WIDTH = canvas.width;
            HEIGHT = canvas.height;
            // 重置遊戲
            restartGame();
        });

        // 初始化並啟動遊戲
        initGameState();
        gameLoop();
    </script>
</body>
</html>
