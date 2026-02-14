<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>Janito in Love</title>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body { 
    overflow: hidden; 
    background: #000; 
    font-family: Arial;
    touch-action: none;
}
#game { 
    position: fixed;
    top: 0; 
    left: 0;
    width: 100vw; 
    height: 100vh; 
}
canvas { 
    display: block; 
    width: 100%; 
    height: 100%; 
    background: linear-gradient(#87CEEB, #90EE90);
}
#hud {
    position: fixed;
    top: 10px;
    left: 50%;
    transform: translateX(-50%);
    background: rgba(139,69,19,0.9);
    padding: 10px 20px;
    border-radius: 10px;
    color: gold;
    font-size: 20px;
    z-index: 10;
}
#controls {
    position: fixed;
    bottom: 0;
    width: 100%;
    height: 120px;
    background: rgba(0,0,0,0.8);
    display: flex;
    justify-content: space-between;
    padding: 20px;
    z-index: 10;
}
.btn {
    width: 60px;
    height: 60px;
    background: #8B4513;
    border: 3px solid gold;
    border-radius: 50%;
    color: gold;
    font-size: 24px;
    display: flex;
    align-items: center;
    justify-content: center;
    user-select: none;
}
.btn:active { background: #654321; }
.jump { width: 70px; height: 70px; background: #e91e63; }
#dialog {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: rgba(139,69,19,0.95);
    padding: 20px;
    border: 4px solid gold;
    border-radius: 15px;
    display: none;
    max-width: 80vw;
    z-index: 20;
}
#dialog h2 { color: gold; text-align: center; margin-bottom: 15px; }
#dialog p { color: white; text-align: center; margin-bottom: 15px; }
.answer {
    padding: 12px;
    margin: 8px 0;
    background: #654321;
    border: 2px solid gold;
    border-radius: 8px;
    color: gold;
    font-size: 16px;
    text-align: center;
}
.answer:active { background: #4a2f1a; }
#start {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(#8B4513, #4a2f1a);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    z-index: 30;
}
#start h1 { color: gold; font-size: 36px; margin-bottom: 20px; text-align: center; }
#start button {
    padding: 15px 40px;
    font-size: 20px;
    background: #e91e63;
    color: white;
    border: 3px solid gold;
    border-radius: 10px;
}
#final {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(#ff69b4, #c71585);
    display: none;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    z-index: 30;
}
#final h1 { color: white; font-size: 36px; text-align: center; margin-bottom: 20px; }
#final button {
    padding: 15px 40px;
    font-size: 20px;
    background: white;
    color: #e91e63;
    border: 3px solid white;
    border-radius: 10px;
}
</style>
</head>
<body>
<div id="start">
    <h1>🌹 Janito in Love 🦆</h1>
    <button onclick="startGame()">Start Game ❤️</button>
</div>
<div id="game">
    <canvas id="canvas"></canvas>
    <div id="hud">
        <span id="hearts">❤️❤️❤️</span> | 🌹 <span id="roses">0/10</span>
    </div>
    <div id="controls">
        <div style="display:flex;gap:10px;">
            <div class="btn" ontouchstart="moveLeft=true" ontouchend="moveLeft=false">◄</div>
            <div class="btn" ontouchstart="moveRight=true" ontouchend="moveRight=false">►</div>
        </div>
        <div class="btn jump" ontouchstart="jump()">JUMP</div>
    </div>
</div>
<div id="dialog">
    <h2 id="qtitle"></h2>
    <p id="qtext"></p>
    <div id="answers"></div>
</div>
<div id="final">
    <h1>HAPPY VALENTINE'S DAY ❤️</h1>
    <button onclick="location.reload()">Play Again 🌹</button>
</div>
<script>
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');

function resize() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
}
resize();
window.addEventListener('resize', resize);

let moveLeft = false, moveRight = false;
let game = {
    lives: 3,
    roses: 0,
    paused: false,
    camera: 0
};

let player = { x: 100, y: 100, vx: 0, vy: 0, w: 40, h: 40, onGround: false };

const wines = [
    {name:'Merlot',type:'Tinto',q:'¿Qué tipo de vino es Merlot?'},
    {name:'Chardonnay',type:'Blanco',q:'¿Qué tipo de vino es Chardonnay?'},
    {name:'Cabernet',type:'Tinto',q:'¿Qué tipo de vino es Cabernet?'},
    {name:'Riesling',type:'Blanco',q:'¿Qué tipo de vino es Riesling?'},
    {name:'Pinot Noir',type:'Tinto',q:'¿Qué tipo de vino es Pinot Noir?'},
    {name:'Sauvignon',type:'Blanco',q:'¿Qué tipo de vino es Sauvignon?'},
    {name:'Malbec',type:'Tinto',q:'¿Qué tipo de vino es Malbec?'},
    {name:'Prosecco',type:'Espumoso',q:'¿Qué tipo de vino es Prosecco?'},
    {name:'Rosé',type:'Rosado',q:'¿Qué tipo de vino es Rosé?'},
    {name:'Syrah',type:'Tinto',q:'¿Qué tipo de vino es Syrah?'}
];

let tables = [];
function initTables() {
    tables = [];
    for(let i=0; i<10; i++) {
        tables.push({
            x: 300 + i*350,
            y: canvas.height - 200,
            wine: wines[i],
            got: false
        });
    }
}

function startGame() {
    document.getElementById('start').style.display = 'none';
    initTables();
    gameLoop();
}

function jump() {
    if(player.onGround && !game.paused) {
        player.vy = -15;
    }
}

function draw() {
    ctx.clearRect(0,0,canvas.width,canvas.height);
    
    // Ground
    ctx.fillStyle = '#90EE90';
    ctx.fillRect(-game.camera, canvas.height-150, 5000, 150);
    
    // Tables
    tables.forEach(t => {
        if(t.got) return;
        let x = t.x - game.camera;
        ctx.fillStyle = '#8B4513';
        ctx.fillRect(x, t.y, 60, 10);
        ctx.fillRect(x+5, t.y+10, 10, 30);
        ctx.fillRect(x+45, t.y+10, 10, 30);
        ctx.fillStyle = '#8B0000';
        ctx.fillRect(x+20, t.y-20, 20, 20);
        ctx.fillStyle = 'gold';
        ctx.font = '12px Arial';
        ctx.fillText(t.wine.name, x+5, t.y-25);
    });
    
    // Player
    let px = player.x - game.camera;
    ctx.fillStyle = 'gold';
    ctx.fillRect(px+5, player.y+5, 30, 30);
    ctx.fillStyle = '#000';
    ctx.fillRect(px+12, player.y+12, 4, 4);
    ctx.fillRect(px+24, player.y+12, 4, 4);
    ctx.fillStyle = 'orange';
    ctx.fillRect(px+15, player.y+22, 10, 5);
}

function update() {
    if(game.paused) return;
    
    // Move
    if(moveLeft) player.vx = -5;
    else if(moveRight) player.vx = 5;
    else player.vx = 0;
    
    player.x += player.vx;
    player.vy += 0.6;
    player.y += player.vy;
    
    // Ground collision
    let ground = canvas.height - 150;
    if(player.y + player.h > ground) {
        player.y = ground - player.h;
        player.vy = 0;
        player.onGround = true;
    } else {
        player.onGround = false;
    }
    
    // Camera
    game.camera = Math.max(0, player.x - canvas.width/3);
    
    // Check tables
    tables.forEach(t => {
        if(!t.got && Math.abs(player.x - t.x) < 50 && Math.abs(player.y - t.y) < 50) {
            showQuestion(t);
        }
    });
}

function showQuestion(table) {
    game.paused = true;
    document.getElementById('qtitle').textContent = '🍷 ' + table.wine.name;
    document.getElementById('qtext').textContent = table.wine.q;
    
    let opts = ['Tinto','Blanco','Rosado','Espumoso'].sort(() => Math.random()-0.5);
    let html = '';
    opts.forEach(o => {
        html += `<div class="answer" onclick="answer('${o}','${table.wine.type}',${tables.indexOf(table)})">${o}</div>`;
    });
    document.getElementById('answers').innerHTML = html;
    document.getElementById('dialog').style.display = 'block';
}

function answer(selected, correct, idx) {
    document.getElementById('dialog').style.display = 'none';
    
    if(selected === correct) {
        game.roses++;
        tables[idx].got = true;
        document.getElementById('roses').textContent = game.roses + '/10';
        if(game.roses >= 10) {
            setTimeout(() => {
                document.getElementById('final').style.display = 'flex';
            }, 500);
        }
    } else {
        game.lives--;
        let h = '';
        for(let i=0; i<game.lives; i++) h += '❤️';
        document.getElementById('hearts').textContent = h;
        if(game.lives <= 0) {
            alert('Game Over! Reloading...');
            location.reload();
        }
    }
    
    game.paused = false;
}

function gameLoop() {
    update();
    draw();
    requestAnimationFrame(gameLoop);
}

// Keyboard for desktop
document.addEventListener('keydown', e => {
    if(e.key === 'ArrowLeft') moveLeft = true;
    if(e.key === 'ArrowRight') moveRight = true;
    if(e.key === 'ArrowUp' || e.key === ' ') jump();
});
document.addEventListener('keyup', e => {
    if(e.key === 'ArrowLeft') moveLeft = false;
    if(e.key === 'ArrowRight') moveRight = false;
});
</script>
</body>
</html>
