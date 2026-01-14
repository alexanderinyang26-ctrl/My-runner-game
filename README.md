# My-runner-game
A runner
<!DOCTYPE html>
<html>
<head>
    <title>My Personal Runner</title>
    <style>
        body { text-align: center; background: #1a1a1a; color: white; font-family: 'Segoe UI', sans-serif; }
        canvas { background: #87CEEB; display: block; margin: 20px auto; border: 5px solid #333; border-radius: 10px; }
        .stats { font-size: 24px; font-weight: bold; }
    </style>
</head>
<body>
    <h1>The Legend of You</h1>
    <p class="stats">Score: <span id="score">0</span></p>
    <canvas id="gameCanvas" width="800" height="300"></canvas>
    <p>Press <strong>SPACE</strong> to Jump! (Double Jump enabled)</p>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        
        let score = 0;
        let gameActive = true;
        let frame = 0;

        // CHARACTER SETUP
        const playerImg = new Image();
        playerImg.src = 'me.png'; // NAME YOUR IMAGE FILE 'me.png'

        const character = {
            x: 80, y: 200, w: 60, h: 60,
            velocityY: 0, gravity: 0.7, jumpPower: -14,
            jumps: 0
        };

        let obstacles = [];

        function update() {
            if (!gameActive) return;
            frame++;
            
            character.velocityY += character.gravity;
            character.y += character.velocityY;

            if (character.y > 200) {
                character.y = 200;
                character.velocityY = 0;
                character.jumps = 0;
            }

            if (frame % 90 === 0) {
                obstacles.push({ x: 800, y: 230, w: 30, h: 30, color: '#e74c3c' });
            }

            for (let i = obstacles.length - 1; i >= 0; i--) {
                obstacles[i].x -= (7 + (score / 5));

                // Collision
                if (character.x < obstacles[i].x + obstacles[i].w &&
                    character.x + character.w > obstacles[i].x &&
                    character.y < obstacles[i].y + obstacles[i].h &&
                    character.y + character.h > obstacles[i].y) {
                    gameActive = false;
                    alert("Game Over! Your Score: " + score);
                    location.reload();
                }

                if (obstacles[i].x + obstacles[i].w < 0) {
                    obstacles.splice(i, 1);
                    score++;
                    document.getElementById("score").innerText = score;
                }
            }
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // Draw Ground
            ctx.fillStyle = "#2ecc71";
            ctx.fillRect(0, 260, 800, 40);

            // DRAW YOU
            if (playerImg.complete && playerImg.naturalWidth !== 0) {
                ctx.drawImage(playerImg, character.x, character.y, character.w, character.h);
            } else {
                // Placeholder if image 'me.png' isn't found
                ctx.fillStyle = "#f1c40f";
                ctx.fillRect(character.x, character.y, character.w, character.h);
                ctx.fillStyle = "black";
                ctx.fillText("ME", character.x + 20, character.y + 35);
            }

            // Draw Obstacles
            obstacles.forEach(obs => {
                ctx.fillStyle = obs.color;
                ctx.fillRect(obs.x, obs.y, obs.w, obs.h);
            });
        }

        function jump() {
            if (character.jumps < 2) {
                character.velocityY = character.jumpPower;
                character.jumps++;
            }
        }

        window.addEventListener("keydown", (e) => { if(e.code === "Space") jump(); });
        canvas.addEventListener("touchstart", jump);

        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }
        gameLoop();
    </script>
</body>
</html>
