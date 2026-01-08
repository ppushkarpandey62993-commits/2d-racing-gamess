<!DOCTYPE html>
<html>
<head>
  <title>Ultimate 2D Racing Game</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body { margin:0; background:#000; color:white; font-family:Arial; text-align:center; }
    canvas { background:#666; display:block; margin:auto; border:3px solid white; }
    button { padding:12px 20px; font-size:18px; margin:5px; border-radius:10px; }
    #garage { background:#111; padding:10px; }
  </style>
</head>
<body>

<h2>🏁 Ultimate 2D Racing</h2>
<p id="info"></p>

<canvas id="game" width="300" height="500"></canvas>

<div>
  <button onclick="left()">⬅️</button>
  <button onclick="right()">➡️</button>
</div>

<div id="garage">
  <button onclick="changeCar()">🔄 Change Car</button>
</div>

<audio id="engine" loop>
  <source src="https://cdn.pixabay.com/audio/2022/03/15/audio_5c2c3c9a16.mp3">
</audio>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");
const engine = document.getElementById("engine");

let roadY = 0;
let score = 0;
let level = 1;
let lives = 3;

let cars = [
  { name:"Lamborghini", color:"yellow", speed:6, unlock:0 },
  { name:"Ferrari", color:"red", speed:7, unlock:500 },
  { name:"Bugatti", color:"blue", speed:9, unlock:1200 },
  { name:"McLaren", color:"orange", speed:8, unlock:2000 }
];

let carIndex = 0;

let player = { x:130, y:380, w:40, h:70 };
let obstacles = [];

engine.volume = 0.4;
engine.play();

function changeCar() {
  let next = (carIndex + 1) % cars.length;
  if (score >= cars[next].unlock) {
    carIndex = next;
  } else {
    alert("🔒 Locked! Need score: " + cars[next].unlock);
  }
}

function left() {
  player.x -= cars[carIndex].speed * 6;
  if (player.x < 0) player.x = 0;
}

function right() {
  player.x += cars[carIndex].speed * 6;
  if (player.x > 260) player.x = 260;
}

document.addEventListener("keydown", e => {
  if (e.key === "ArrowLeft") left();
  if (e.key === "ArrowRight") right();
});

function spawnObstacle() {
  obstacles.push({
    x: Math.random()*260,
    y: -80,
    w: 40,
    h: 70
  });
}

function drawRoad() {
  roadY += 6 + level;
  if (roadY > 40) roadY = 0;

  ctx.fillStyle = "#444";
  ctx.fillRect(0,0,300,500);

  ctx.strokeStyle = "white";
  ctx.setLineDash([20,20]);
  ctx.beginPath();
  ctx.moveTo(150, roadY);
  ctx.lineTo(150, 500);
  ctx.stroke();
  ctx.setLineDash([]);
}

function update() {
  ctx.clearRect(0,0,300,500);
  drawRoad();

  ctx.fillStyle = cars[carIndex].color;
  ctx.fillRect(player.x, player.y, player.w, player.h);

  ctx.fillStyle = "black";
  for (let o of obstacles) {
    o.y += 4 + level;
    ctx.fillRect(o.x, o.y, o.w, o.h);

    if (
      player.x < o.x + o.w &&
      player.x + player.w > o.x &&
      player.y < o.y + o.h &&
      player.y + player.h > o.y
    ) {
      lives--;
      obstacles = [];
      if (lives === 0) {
        alert("💥 GAME OVER\nScore: " + score);
        location.reload();
      }
    }
  }

  obstacles = obstacles.filter(o => o.y < 500);

  score++;
  if (score % 500 === 0) level++;

  document.getElementById("info").innerText =
    `🚗 ${cars[carIndex].name} | 🏁 Level ${level} | ⭐ Score ${score} | ❤️ ${lives}`;

  requestAnimationFrame(update);
}

setInterval(spawnObstacle, Math.max(400, 1200 - level*100));
update();
</script>

</body>
</html>
