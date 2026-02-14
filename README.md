<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="mobile-web-app-capable" content="yes">
    <title>Janito in Love</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
            user-select: none;
            -webkit-user-select: none;
        }

        body {
            font-family: 'Courier New', monospace;
            overflow: hidden;
            background: #000;
            touch-action: none;
            position: fixed;
            width: 100%;
            height: 100%;
        }

        #gameContainer {
            position: relative;
            width: 100vw;
            height: 100vh;
            display: flex;
            flex-direction: column;
            background: #87CEEB;
        }

        #gameCanvas {
            flex: 1;
            display: block;
            width: 100%;
            background: linear-gradient(to bottom, #87CEEB 0%, #E0F6FF 60%, #90EE90 100%);
            image-rendering: pixelated;
            image-rendering: crisp-edges;
        }

        /* HUD */
        #hud {
            position: absolute;
            top: 10px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(139, 69, 19, 0.95);
            padding: 8px 15px;
            border-radius: 10px;
            border: 3px solid #654321;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.5);
            display: flex;
            gap: 15px;
            align-items: center;
            z-index: 100;
            font-size: 18px;
            color: #FFD700;
        }

        .hud-section {
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .heart {
            font-size: 20px;
            animation: heartPulse 1s ease-in-out infinite;
        }

        @keyframes heartPulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.1); }
        }

        /* Mobile Controls */
        #controls {
            position: absolute;
            bottom: 0;
            width: 100%;
            height: 130px;
            background: rgba(0, 0, 0, 0.85);
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 20px;
            border-top: 3px solid #654321;
        }

        .control-group {
            display: flex;
            gap: 12px;
        }

        .control-btn {
            width: 65px;
            height: 65px;
            border-radius: 50%;
            background: linear-gradient(135deg, #8B4513 0%, #654321 100%);
            border: 4px solid #FFD700;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 28px;
            color: #FFD700;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.5), inset 0 2px 4px rgba(255, 255, 255, 0.3);
            cursor: pointer;
            touch-action: manipulation;
            transition: transform 0.1s;
        }

        .control-btn:active {
            transform: scale(0.9);
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.5), inset 0 1px 2px rgba(255, 255, 255, 0.3);
        }

        .control-btn.jump {
            width: 75px;
            height: 75px;
            background: linear-gradient(135deg, #e91e63 0%, #c71585 100%);
            font-size: 16px;
            font-weight: bold;
        }

        /* Question Dialog */
        #questionDialog {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(139, 69, 19, 0.98);
            padding: 20px;
            border-radius: 15px;
            border: 5px solid #FFD700;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.7);
            z-index: 200;
            display: none;
            max-width: 85vw;
            animation: dialogSlideIn 0.3s ease-out;
        }

        @keyframes dialogSlideIn {
            from { transform: translate(-50%, -60%); opacity: 0; }
            to { transform: translate(-50%, -50%); opacity: 1; }
        }

        #questionDialog h2 {
            color: #FFD700;
            margin-bottom: 12px;
            font-size: 20px;
            text-align: center;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
        }

        #questionDialog p {
            color: #FFF;
            margin-bottom: 15px;
            font-size: 16px;
            text-align: center;
        }

        .answer-options {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .answer-btn {
            padding: 12px;
            background: linear-gradient(135deg, #654321 0%, #4a2f1a 100%);
            border: 3px solid #FFD700;
            border-radius: 10px;
            color: #FFD700;
            font-size: 15px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s;
            text-align: center;
            font-family: 'Courier New', monospace;
        }

        .answer-btn:active {
            transform: scale(0.95);
            background: linear-gradient(135deg, #4a2f1a 0%, #654321 100%);
        }

        /* Title Screen */
        #titleScreen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #8B4513 0%, #654321 50%, #4a2f1a 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            z-index: 400;
        }

        #titleScreen h1 {
            color: #FFD700;
            font-size: 42px;
            text-shadow: 4px 4px 8px rgba(0, 0, 0, 0.7);
            margin-bottom: 10px;
            animation: titleGlow 2s ease-in-out infinite;
            text-align: center;
            padding: 0 20px;
        }

        @keyframes titleGlow {
            0%, 100% { text-shadow: 4px 4px 8px rgba(0, 0, 0, 0.7), 0 0 20px #FFD700; }
            50% { text-shadow: 4px 4px 8px rgba(0, 0, 0, 0.7), 0 0 40px #FFD700; }
        }

        .subtitle {
            color: #e91e63;
            font-size: 18px;
            margin-bottom: 35px;
            font-style: italic;
            text-align: center;
        }

        #startBtn {
            padding: 18px 45px;
            font-size: 22px;
            background: linear-gradient(135deg, #e91e63 0%, #c71585 100%);
            color: white;
            border: 4px solid #FFD700;
            border-radius: 15px;
            cursor: pointer;
            font-weight: bold;
            font-family: 'Courier New', monospace;
            box-shadow: 0 5px 15px rgba(233, 30, 99, 0.5);
            transition: transform 0.2s;
        }

        #startBtn:active {
            transform: scale(0.95);
        }

        /* Final Scene */
        #finalScene {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #ff69b4 0%, #ff1493 50%, #c71585 100%);
            display: none;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            z-index: 300;
            padding: 20px;
        }

        #finalScene h1 {
            color: white;
            font-size: 40px;
            text-shadow: 4px 4px 8px rgba(0, 0, 0, 0.5);
            animation: heartbeat 1.5s ease-in-out infinite;
            margin-bottom: 25px;
            text-align: center;
        }

        @keyframes heartbeat {
            0%, 100% { transform: scale(1); }
            25% { transform: scale(1.1); }
            50% { transform: scale(1); }
        }

        #restartBtn {
            padding: 18px 35px;
            font-size: 22px;
            background: white;
            color: #e91e63;
            border: 4px solid white;
            border-radius: 15px;
            cursor: pointer;
            font-weight: bold;
            font-family: 'Courier New', monospace;
            transition: transform 0.2s;
        }

        #restartBtn:active {
            transform: scale(0.95);
        }

        /* Game Over Screen */
        #gameOverScreen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.95);
            display: none;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            z-index: 350;
            padding: 20px;
        }

        #gameOverScreen h1 {
            color: #ff4444;
            font-size: 46px;
            text-shadow: 4px 4px 8px rgba(0, 0, 0, 0.7);
            margin-bottom: 18px;
            text-align: center;
        }

        #gameOverScreen p {
            color: white;
            font-size: 22px;
            margin-bottom: 25px;
            text-align: center;
        }

        /* Floating hearts */
        .floating-heart {
            position: absolute;
            font-size: 28px;
            animation: floatUp 4s ease-in-out infinite;
            opacity: 0.7;
            pointer-events: none;
        }

        @keyframes floatUp {
            0% { transform: translateY(0) rotate(0deg); opacity: 0.7; }
            100% { transform: translateY(-100vh) rotate(360deg); opacity: 0; }
        }

        /* Particle effect */
        .particle {
            position: absolute;
            pointer-events: none;
            font-size: 18px;
            animation: particleFly 1s ease-out forwards;
        }

        @keyframes particleFly {
            0% { transform: translate(0, 0) scale(1); opacity: 1; }
            100% { transform: translate(var(--tx), var(--ty)) scale(0); opacity: 0; }
        }
    </style>
</head>
<body>
    <div id="gameContainer">
        <!-- Title Screen -->
        <div id="titleScreen">
            <h1>🌹 Janito in Love 🦆</h1>
            <p class="subtitle">A Valentine's Day Adventure</p>
            <button id="startBtn">Start Game ❤️</button>
        </div>

        <!-- HUD -->
        <div id="hud">
            <div class="hud-section">
                <span id="lives">
                    <span class="heart">❤️</span>
                    <span class="heart">❤️</span>
                    <span class="heart">❤️</span>
                </span>
            </div>
            <div class="hud-section">
                <span>🌹</span>
                <span id="roseCount">0/10</span>
            </div>
            <div class="hud-section">
                <span>Level 1</span>
            </div>
        </div>

        <!-- Game Canvas -->
        <canvas id="gameCanvas"></canvas>

        <!-- Mobile Controls -->
        <div id="controls">
            <div class="control-group">
                <button class="control-btn" id="leftBtn">◄</button>
                <button class="control-btn" id="rightBtn">►</button>
            </div>
            <div class="control-group">
                <button class="control-btn jump" id="jumpBtn">JUMP</button>
            </div>
        </div>

        <!-- Question Dialog -->
        <div id="questionDialog">
            <h2 id="questionTitle">🍷 Wine Question</h2>
            <p id="questionText"></p>
            <div class="answer-options" id="answerOptions"></div>
        </div>

        <!-- Game Over Screen -->
        <div id="gameOverScreen">
            <h1>💔 GAME OVER 💔</h1>
            <p>You ran out of hearts!</p>
            <button id="gameOverRestartBtn" class="answer-btn" style="width: 200px; font-size: 20px;">Try Again</button>
        </div>

        <!-- Final Scene -->
        <div id="finalScene">
            <h1>HAPPY VALENTINE'S DAY ❤️</h1>
            <button id="restartBtn">Play Again 🌹</button>
        </div>
    </div>

    <script>
        // ===========================================
        // GAME SETUP
        // ===========================================
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        
        // Set canvas size properly
        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight - 130; // Account for controls
        }
        resizeCanvas();
        window.addEventListener('resize', resizeCanvas);

        // Audio Context
        const audioContext = new (window.AudioContext || window.webkitAudioContext)();

        // Game State
        let gameStarted = false;
        let gameState = {
            roses: 0,
            maxRoses: 10,
            lives: 3,
            currentTable: null,
            paused: false,
            cameraX: 0
        };

        // ===========================================
        // WINE KNOWLEDGE BASE
        // ===========================================
        const wineQuestions = [
            {
                wine: 'Merlot',
                question: '¿Qué tipo de vino es Merlot?',
                correct: 'Vino Tinto',
                options: ['Vino Tinto', 'Vino Blanco', 'Vino Rosado', 'Vino Espumoso']
            },
            {
                wine: 'Chardonnay',
                question: '¿Qué tipo de vino es Chardonnay?',
                correct: 'Vino Blanco',
                options: ['Vino Tinto', 'Vino Blanco', 'Vino Rosado', 'Vino Espumoso']
            },
            {
                wine: 'Cabernet Sauvignon',
                question: '¿Qué tipo de vino es Cabernet Sauvignon?',
                correct: 'Vino Tinto',
                options: ['Vino Tinto', 'Vino Blanco', 'Vino Rosado', 'Vino Dulce']
            },
            {
                wine: 'Sauvignon Blanc',
                question: '¿Qué tipo de vino es Sauvignon Blanc?',
                correct: 'Vino Blanco',
                options: ['Vino Tinto', 'Vino Blanco', 'Vino Rosado', 'Vino Oporto']
            },
            {
                wine: 'Pinot Noir',
                question: '¿Qué tipo de vino es Pinot Noir?',
                correct: 'Vino Tinto',
                options: ['Vino Tinto', 'Vino Blanco', 'Vino Rosado', 'Jerez']
            },
            {
                wine: 'Riesling',
                question: '¿Qué tipo de vino es Riesling?',
                correct: 'Vino Blanco',
                options: ['Vino Tinto', 'Vino Blanco', 'Vino Rosado', 'Vino Oporto']
            },
            {
                wine: 'Malbec',
                question: '¿Qué tipo de vino es Malbec?',
                correct: 'Vino Tinto',
                options: ['Vino Tinto', 'Vino Blanco', 'Vino Rosado', 'Vino Espumoso']
            },
            {
                wine: 'Prosecco',
                question: '¿Qué tipo de vino es Prosecco?',
                correct: 'Vino Espumoso',
                options: ['Vino Tinto', 'Vino Blanco', 'Vino Rosado', 'Vino Espumoso']
            },
            {
                wine: 'Rosé',
                question: '¿Qué tipo de vino es Rosé?',
                correct: 'Vino Rosado',
                options: ['Vino Tinto', 'Vino Blanco', 'Vino Rosado', 'Vino Espumoso']
            },
            {
                wine: 'Syrah',
                question: '¿Qué tipo de vino es Syrah?',
                correct: 'Vino Tinto',
                options: ['Vino Tinto', 'Vino Blanco', 'Vino Rosado', 'Vino Dulce']
            }
        ];

        // ===========================================
        // PLAYER (JANITO) - POSICIÓN CORREGIDA
        // ===========================================
        const player = {
            x: 100,
            y: canvas.height - 200, // Posición inicial correcta
            width: 40,
            height: 40,
            velocityY: 0,
            velocityX: 0,
            speed: 5,
            jumpPower: -13,
            gravity: 0.6,
            onGround: false,
            direction: 1
        };

        // ===========================================
        // LEVEL DESIGN
        // ===========================================
        let platforms = [];
        
        function initPlatforms() {
            platforms = [
                { x: -100, y: canvas.height - 60, width: 3000, height: 60 } // Ground
            ];
        }

        // Wine tables positioned along the level
        let wineTables = [];

        function initWineTables() {
            wineTables = [];
            const startX = 300;
            const spacing = 350;
            const groundY = canvas.height - 60;
            
            for (let i = 0; i < 10; i++) {
                wineTables.push({
                    x: startX + (i * spacing),
                    y: groundY - 80,
                    width: 80,
                    height: 80,
                    collected: false,
                    question: wineQuestions[i]
                });
            }
        }

        // ===========================================
        // MOBILE CONTROLS
        // ===========================================
        const controls = {
            left: false,
            right: false,
            jump: false
        };

        // Touch control handlers
        document.getElementById('leftBtn').addEventListener('touchstart', (e) => {
            e.preventDefault();
            controls.left = true;
        });
        document.getElementById('leftBtn').addEventListener('touchend', (e) => {
            e.preventDefault();
            controls.left = false;
        });

        document.getElementById('rightBtn').addEventListener('touchstart', (e) => {
            e.preventDefault();
            controls.right = true;
        });
        document.getElementById('rightBtn').addEventListener('touchend', (e) => {
            e.preventDefault();
            controls.right = false;
        });

        document.getElementById('jumpBtn').addEventListener('touchstart', (e) => {
            e.preventDefault();
            if (player.onGround && !gameState.paused && gameStarted) {
                player.velocityY = player.jumpPower;
                playJumpSound();
            }
        });

        // Fallback for desktop testing
        document.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowLeft') controls.left = true;
            if (e.key === 'ArrowRight') controls.right = true;
            if (e.key === 'ArrowUp' && player.onGround && !gameState.paused) {
                player.velocityY = player.jumpPower;
                playJumpSound();
            }
        });
        document.addEventListener('keyup', (e) => {
            if (e.key === 'ArrowLeft') controls.left = false;
            if (e.key === 'ArrowRight') controls.right = false;
        });

        // ===========================================
        // SOUND EFFECTS
        // ===========================================
        function playSound(frequency, duration, type = 'sine', volume = 0.3) {
            try {
                const oscillator = audioContext.createOscillator();
                const gainNode = audioContext.createGain();
                
                oscillator.connect(gainNode);
                gainNode.connect(audioContext.destination);
                
                oscillator.frequency.value = frequency;
                oscillator.type = type;
                
                gainNode.gain.setValueAtTime(volume, audioContext.currentTime);
                gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + duration);
                
                oscillator.start(audioContext.currentTime);
                oscillator.stop(audioContext.currentTime + duration);
            } catch (e) {
                console.log('Audio not available');
            }
        }

        function playJumpSound() {
            playSound(400, 0.1);
            setTimeout(() => playSound(500, 0.1), 50);
        }

        function playCorrectSound() {
            playSound(523, 0.1);
            setTimeout(() => playSound(659, 0.1), 100);
            setTimeout(() => playSound(784, 0.2), 200);
        }

        function playWrongSound() {
            playSound(200, 0.3, 'sawtooth');
        }

        function playHurtSound() {
            playSound(150, 0.2, 'sawtooth');
            setTimeout(() => playSound(100, 0.2, 'sawtooth'), 100);
        }

        // ===========================================
        // DRAWING FUNCTIONS
        // ===========================================
        function drawPlayer() {
            const screenX = player.x - gameState.cameraX;
            
            // Chick body (bigger and more visible)
            ctx.fillStyle = '#FFD700';
            ctx.fillRect(screenX + 5, player.y + 5, 30, 30);
            
            // Chick eyes
            ctx.fillStyle = '#000';
            const eyeOffset = player.direction > 0 ? 5 : 0;
            ctx.fillRect(screenX + 12 + eyeOffset, player.y + 12, 4, 4);
            
            // Chick beak
            ctx.fillStyle = '#FF8C00';
            if (player.direction > 0) {
                ctx.fillRect(screenX + 35, player.y + 18, 6, 5);
            } else {
                ctx.fillRect(screenX, player.y + 18, 6, 5);
            }
            
            // Chick feet
            ctx.fillStyle = '#FF8C00';
            ctx.fillRect(screenX + 10, player.y + 35, 5, 8);
            ctx.fillRect(screenX + 25, player.y + 35, 5, 8);
        }

        function drawPlatforms() {
            platforms.forEach(platform => {
                const screenX = platform.x - gameState.cameraX;
                
                // Grass
                ctx.fillStyle = '#90EE90';
                ctx.fillRect(screenX, platform.y, platform.width, 12);
                
                // Dirt
                ctx.fillStyle = '#8B4513';
                ctx.fillRect(screenX, platform.y + 12, platform.width, platform.height - 12);
                
                // Grass detail (pixel style)
                ctx.fillStyle = '#7CCD7C';
                for (let i = 0; i < platform.width; i += 25) {
                    ctx.fillRect(screenX + i, platform.y, 10, 12);
                }
            });
        }

        function drawWineTable(table) {
            if (table.collected) return;
            
            const screenX = table.x - gameState.cameraX;
            const time = Date.now() / 1000;
            const bounce = Math.sin(time * 2) * 4;
            
            // Table (wooden) - más grande
            ctx.fillStyle = '#8B4513';
            ctx.fillRect(screenX + 10, table.y + 45, 60, 10);
            
            // Table legs
            ctx.fillRect(screenX + 15, table.y + 55, 10, 25);
            ctx.fillRect(screenX + 55, table.y + 55, 10, 25);
            
            // Wine glass - más grande y visible
            ctx.fillStyle = '#8B0000';
            ctx.fillRect(screenX + 28, table.y + 25 + bounce, 24, 20);
            ctx.fillStyle = '#654321';
            ctx.fillRect(screenX + 36, table.y + 45 + bounce, 8, 10);
            
            // Sparkle
            ctx.fillStyle = 'rgba(255, 255, 255, 0.9)';
            ctx.fillRect(screenX + 32, table.y + 28 + bounce, 4, 4);
            
            // Wine name label - más grande
            ctx.fillStyle = '#FFD700';
            ctx.font = 'bold 14px Courier';
            ctx.textAlign = 'center';
            ctx.fillText(table.question.wine, screenX + 40, table.y + 15);
        }

        function drawBackground() {
            // Clouds (pixel style)
            ctx.fillStyle = 'rgba(255, 255, 255, 0.7)';
            const time = Date.now() / 3000;
            
            for (let i = 0; i < 5; i++) {
                const x = ((time * 20 + i * 250) % (canvas.width + 100)) - 50;
                const y = 40 + i * 40;
                
                ctx.fillRect(x, y, 35, 18);
                ctx.fillRect(x + 12, y - 10, 35, 18);
                ctx.fillRect(x + 25, y, 35, 18);
            }
            
            // Vineyard posts
            ctx.fillStyle = '#654321';
            for (let i = -2; i < 15; i++) {
                const x = i * 200 - gameState.cameraX;
                if (x > -50 && x < canvas.width + 50) {
                    ctx.fillRect(x, canvas.height - 250, 10, 190);
                    
                    // Vines
                    ctx.fillStyle = '#228B22';
                    ctx.fillRect(x - 35, canvas.height - 180, 80, 5);
                    ctx.fillRect(x - 25, canvas.height - 200, 5, 20);
                    ctx.fillRect(x + 12, canvas.height - 195, 5, 15);
                    ctx.fillRect(x + 35, canvas.height - 190, 5, 10);
                }
            }
            ctx.fillStyle = '#654321';
        }

        function drawHUD() {
            // Lives
            const livesSpan = document.getElementById('lives');
            livesSpan.innerHTML = '';
            for (let i = 0; i < gameState.lives; i++) {
                livesSpan.innerHTML += '<span class="heart">❤️</span>';
            }
            
            // Roses
            document.getElementById('roseCount').textContent = `${gameState.roses}/${gameState.maxRoses}`;
        }

        // ===========================================
        // PARTICLE EFFECTS
        // ===========================================
        function createParticles(x, y, emoji) {
            for (let i = 0; i < 8; i++) {
                const particle = document.createElement('div');
                particle.className = 'particle';
                particle.textContent = emoji;
                particle.style.left = x + 'px';
                particle.style.top = y + 'px';
                
                const angle = (Math.PI * 2 * i) / 8;
                const distance = 50 + Math.random() * 30;
                const tx = Math.cos(angle) * distance;
                const ty = Math.sin(angle) * distance;
                
                particle.style.setProperty('--tx', tx + 'px');
                particle.style.setProperty('--ty', ty + 'px');
                
                document.getElementById('gameContainer').appendChild(particle);
                
                setTimeout(() => particle.remove(), 1000);
            }
        }

        // ===========================================
        // GAME LOGIC
        // ===========================================
        function updatePlayer() {
            if (gameState.paused) return;
            
            // Horizontal movement
            if (controls.left) {
                player.velocityX = -player.speed;
                player.direction = -1;
            } else if (controls.right) {
                player.velocityX = player.speed;
                player.direction = 1;
            } else {
                player.velocityX = 0;
            }
            
            player.x += player.velocityX;
            
            // Apply gravity
            player.velocityY += player.gravity;
            player.y += player.velocityY;
            
            // Platform collision
            player.onGround = false;
            platforms.forEach(platform => {
                if (player.x + player.width > platform.x &&
                    player.x < platform.x + platform.width &&
                    player.y + player.height > platform.y &&
                    player.y + player.height < platform.y + platform.height &&
                    player.velocityY > 0) {
                    
                    player.y = platform.y - player.height;
                    player.velocityY = 0;
                    player.onGround = true;
                }
            });
            
            // Camera follow
            const targetCameraX = player.x - canvas.width / 3;
            gameState.cameraX = Math.max(0, targetCameraX);
            
            // Check table collision
            wineTables.forEach(table => {
                if (!table.collected &&
                    Math.abs(player.x - table.x) < 70 &&
                    Math.abs(player.y - table.y) < 70) {
                    showQuestion(table);
                }
            });
        }

        function showQuestion(table) {
            gameState.paused = true;
            gameState.currentTable = table;
            
            const dialog = document.getElementById('questionDialog');
            const questionText = document.getElementById('questionText');
            const answerOptions = document.getElementById('answerOptions');
            
            questionText.textContent = table.question.question;
            
            // Shuffle answers
            const shuffled = [...table.question.options].sort(() => Math.random() - 0.5);
            
            answerOptions.innerHTML = '';
            shuffled.forEach(option => {
                const btn = document.createElement('button');
                btn.className = 'answer-btn';
                btn.textContent = option;
                btn.onclick = () => checkAnswer(option, table.question.correct);
                answerOptions.appendChild(btn);
            });
            
            dialog.style.display = 'block';
        }

        function checkAnswer(selected, correct) {
            const dialog = document.getElementById('questionDialog');
            
            if (selected === correct) {
                // Correct answer
                playCorrectSound();
                gameState.roses++;
                gameState.currentTable.collected = true;
                
                // Particle effect
                const tableScreenX = gameState.currentTable.x - gameState.cameraX + canvas.offsetLeft;
                const tableScreenY = gameState.currentTable.y + canvas.offsetTop;
                createParticles(tableScreenX, tableScreenY, '🌹');
                
                drawHUD();
                
                // Check win condition
                if (gameState.roses >= gameState.maxRoses) {
                    setTimeout(showFinalScene, 500);
                }
            } else {
                // Wrong answer
                playWrongSound();
                playHurtSound();
                gameState.lives--;
                
                drawHUD();
                
                // Check game over
                if (gameState.lives <= 0) {
                    setTimeout(showGameOver, 500);
                }
            }
            
            dialog.style.display = 'none';
            gameState.paused = false;
            gameState.currentTable = null;
        }

        function showGameOver() {
            gameState.paused = true;
            document.getElementById('gameOverScreen').style.display = 'flex';
        }

        function showFinalScene() {
            gameState.paused = true;
            const finalScene = document.getElementById('finalScene');
            
            // Draw final scene on canvas
            ctx.fillStyle = 'rgba(255, 105, 180, 0.95)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            setTimeout(() => {
                // Janito (chick) - larger
                ctx.fillStyle = '#FFD700';
                const janitoX = canvas.width * 0.3;
                const janitoY = canvas.height * 0.4;
                ctx.fillRect(janitoX - 20, janitoY - 20, 40, 40);
                
                // Duck with red boots - larger
                ctx.fillStyle = '#FFEB3B';
                const duckX = canvas.width * 0.7;
                const duckY = canvas.height * 0.4;
                ctx.fillRect(duckX - 25, duckY - 25, 50, 50);
                
                // Duck beak
                ctx.fillStyle = '#FF9800';
                ctx.fillRect(duckX + 25, duckY - 5, 15, 10);
                
                // Red boots
                ctx.fillStyle = '#FF0000';
                ctx.fillRect(duckX - 20, duckY + 25, 15, 20);
                ctx.fillRect(duckX + 5, duckY + 25, 15, 20);
                
                // Giant bouquet
                ctx.font = '80px Arial';
                ctx.fillText('💐', canvas.width / 2 - 40, canvas.height / 2 + 20);
                
                // Hearts everywhere
                ctx.font = '40px Arial';
                for (let i = 0; i < 20; i++) {
                    ctx.fillText('❤️', Math.random() * canvas.width, Math.random() * canvas.height);
                }
            }, 100);
            
            // Create floating hearts
            for (let i = 0; i < 15; i++) {
                setTimeout(() => {
                    const heart = document.createElement('div');
                    heart.className = 'floating-heart';
                    heart.textContent = '❤️';
                    heart.style.left = Math.random() * 100 + '%';
                    heart.style.bottom = '-50px';
                    heart.style.animationDelay = Math.random() * 2 + 's';
                    finalScene.appendChild(heart);
                }, i * 200);
            }
            
            finalScene.style.display = 'flex';
            playCorrectSound();
            setTimeout(playCorrectSound, 300);
            setTimeout(playCorrectSound, 600);
        }

        // ===========================================
        // GAME LOOP
        // ===========================================
        function gameLoop() {
            if (!gameStarted) return;
            
            if (!gameState.paused) {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                
                drawBackground();
                drawPlatforms();
                wineTables.forEach(drawWineTable);
                updatePlayer();
                drawPlayer();
            }
            
            requestAnimationFrame(gameLoop);
        }

        // ===========================================
        // EVENT LISTENERS
        // ===========================================
        document.getElementById('startBtn').addEventListener('click', () => {
            document.getElementById('titleScreen').style.display = 'none';
            gameStarted = true;
            initPlatforms();
            initWineTables();
            player.y = canvas.height - 200; // Reset player position
            gameLoop();
        });

        document.getElementById('restartBtn').addEventListener('click', restartGame);
        document.getElementById('gameOverRestartBtn').addEventListener('click', restartGame);

        function restartGame() {
            // Reset game state
            gameState = {
                roses: 0,
                maxRoses: 10,
                lives: 3,
                currentTable: null,
                paused: false,
                cameraX: 0
            };
            
            player.x = 100;
            player.y = canvas.height - 200;
            player.velocityY = 0;
            
            document.getElementById('finalScene').style.display = 'none';
            document.getElementById('gameOverScreen').style.display = 'none';
            document.getElementById('finalScene').innerHTML = '<h1>HAPPY VALENTINE\'S DAY ❤️</h1><button id="restartBtn">Play Again 🌹</button>';
            document.getElementById('restartBtn').addEventListener('click', restartGame);
            
            initPlatforms();
            initWineTables();
            drawHUD();
        }

        // ===========================================
        // INITIALIZATION
        // ===========================================
        console.log('🌹 Janito in Love - Mobile Game Loaded! 🦆');
        console.log('Use on-screen touch controls to play!');
    </script>
</body>
</html>
