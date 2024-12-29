<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Run and Jump Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #87CEEB;
        }

        canvas {
            display: block;
            margin: 0 auto;
            background-color: #87CEEB;
        }

        #score {
            position: absolute;
            top: 20px;
            left: 20px;
            font-size: 24px;
            color: #fff;
        }
    </style>
</head>
<body>

<canvas id="gameCanvas"></canvas>
<div id="score">Score: 0</div>

<script>
    // Set up the game canvas
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    // Game Variables
    let score = 0;
    let gravity = 0.5;
    let friction = 0.98;
    let isJumping = false;
    let jumpPower = 10;
    let gameSpeed = 5;

    // Player variables
    const player = {
        width: 50,
        height: 50,
        x: 100,
        y: canvas.height - 100,
        speedX: 0,
        speedY: 0,
        color: '#FF6347',
        jumping: false,
    };

    // Ground variables
    const ground = {
        y: canvas.height - 50,
        width: canvas.width,
        height: 50,
        color: '#2E8B57'
    };

    // Obstacles array
    const obstacles = [];

    // Controls
    document.addEventListener('keydown', (e) => {
        if (e.code === 'Space' && !player.jumping) {
            player.jumping = true;
            player.speedY = -jumpPower;
        }
    });

    // Game loop
    function gameLoop() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        // Player mechanics
        player.speedY += gravity;
        player.y += player.speedY;
        if (player.y > ground.y - player.height) {
            player.y = ground.y - player.height;
            player.speedY = 0;
            player.jumping = false;
        }

        // Moving the player
        ctx.fillStyle = player.color;
        ctx.fillRect(player.x, player.y, player.width, player.height);

        // Moving obstacles
        for (let i = 0; i < obstacles.length; i++) {
            const obstacle = obstacles[i];
            obstacle.x -= gameSpeed;
            ctx.fillStyle = '#000000';
            ctx.fillRect(obstacle.x, obstacle.y, obstacle.width, obstacle.height);
            if (obstacle.x + obstacle.width < 0) {
                obstacles.splice(i, 1);
                score++;
            }

            // Collision detection
            if (player.x < obstacle.x + obstacle.width &&
                player.x + player.width > obstacle.x &&
                player.y < obstacle.y + obstacle.height &&
                player.y + player.height > obstacle.y) {
                    gameOver();
            }
        }

        // Score display
        document.getElementById('score').textContent = `Score: ${score}`;

        // Add new obstacles
        if (Math.random() < 0.02) {
            const height = Math.random() * (canvas.height / 2) + 20;
            obstacles.push({
                x: canvas.width,
                y: ground.y - height,
                width: 50,
                height: height
            });
        }

        requestAnimationFrame(gameLoop);
    }

    // Game Over
    function gameOver() {
        alert(`Game Over! Your score is ${score}`);
        score = 0;
        obstacles.length = 0;
        player.x = 100;
        player.y = canvas.height - 100;
        player.speedY = 0;
        player.jumping = false;
    }

    // Start the game
    gameLoop();
</script>
</body>
</html>
