# Airplanes
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Cartoon Airplane Game</title>
<style>
    body {
        margin: 0;
        overflow: hidden;
        font-family: Arial;
        background: linear-gradient(to bottom, #6ec6ff, #b3ecff);
    }

    canvas {
        display: block;
    }

    #score {
        position: absolute;
        top: 10px;
        left: 10px;
        font-size: 20px;
        color: white;
        font-weight: bold;
    }
</style>
</head>
<body>

<div id="score">Score: 0</div>
<canvas id="gameCanvas"></canvas>

<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

canvas.width = window.innerWidth;
canvas.height = window.innerHeight;

let plane = {
    x: 100,
    y: canvas.height / 2,
    size: 40,
    velocity: 0
};

let gravity = 0.5;
let lift = -10;

let obstacles = [];
let speed = 3;
let score = 0;

// Increase speed every 5 seconds
setInterval(() => {
    speed += 1;
}, 5000);

// Control (tap / click)
window.addEventListener("click", () => {
    plane.velocity = lift;
});

function spawnObstacle() {
    let type = Math.random() > 0.5 ? "bird" : "cloud";
    obstacles.push({
        x: canvas.width,
        y: Math.random() * canvas.height,
        size: 40,
        type: type
    });
}

setInterval(spawnObstacle, 1500);

function drawPlane() {
    // airplane body
    ctx.fillStyle = "yellow";
    ctx.fillRect(plane.x, plane.y, plane.size, plane.size / 2);

    // boy (emoji style)
    ctx.font = "20px Arial";
    ctx.fillText("👦", plane.x + 5, plane.y + 20);
}

function drawObstacle(obs) {
    if (obs.type === "bird") {
        ctx.font = "30px Arial";
        ctx.fillText("🐦", obs.x, obs.y);
    } else {
        ctx.fillStyle = "black";
        ctx.beginPath();
        ctx.arc(obs.x, obs.y, 20, 0, Math.PI * 2);
        ctx.fill();
    }
}

function update() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // plane physics
    plane.velocity += gravity;
    plane.y += plane.velocity;

    drawPlane();

    // obstacles
    obstacles.forEach((obs, index) => {
        obs.x -= speed;
        drawObstacle(obs);

        // collision
        if (
            plane.x < obs.x + obs.size &&
            plane.x + plane.size > obs.x &&
            plane.y < obs.y + obs.size &&
            plane.y + plane.size > obs.y
        ) {
            alert("Game Over! Score: " + score);
            location.reload();
        }

        // remove off screen
        if (obs.x < 0) {
            obstacles.splice(index, 1);
            score++;
        }
    });

    // ground limit
    if (plane.y > canvas.height) {
        alert("Game Over! Score: " + score);
        location.reload();
    }

    document.getElementById("score").innerText = "Score: " + score;

    requestAnimationFrame(update);
}

update();
</script>

</body>
</html>
