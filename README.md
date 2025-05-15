<!DOCTYPE html>
<html lang="nl">
<head>
  <meta charset="UTF-8">
  <title>Flappy Bird met Knop</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background: skyblue;
      font-family: sans-serif;
    }

    canvas {
      display: block;
      background: linear-gradient(skyblue, lightblue);
    }

    #vliegKnop {
      position: absolute;
      bottom: 30px;
      left: 50%;
      transform: translateX(-50%);
      padding: 15px 30px;
      font-size: 20px;
      background: yellow;
      border: 2px solid #333;
      border-radius: 10px;
      cursor: pointer;
      box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    }

    #gameOver, #win {
      position: absolute;
      top: 40%;
      left: 50%;
      transform: translate(-50%, -50%);
      background: white;
      padding: 20px 40px;
      border-radius: 10px;
      font-size: 24px;
      display: none;
    }
  </style>
</head>
<body>

  <canvas id="game" width="400" height="600"></canvas>
  <div id="gameOver">Game Over</div>
  <div id="win">Je hebt gewonnen!</div>
  <button id="vliegKnop">🕊️ Vlieg!</button>

  <script>
    const canvas = document.getElementById('game');
    const ctx = canvas.getContext('2d');
    const vliegKnop = document.getElementById('vliegKnop');

    let bird = { x: 50, y: 200, width: 30, height: 30, velocity: 0 };
    const gravity = 0.5;
    const lift = -8;

    let pipes = [];
    let score = 0;
    let isGameOver = false;
    let isWon = false;

    function drawBird() {
      ctx.fillStyle = "yellow";
      ctx.fillRect(bird.x, bird.y, bird.width, bird.height);
    }

    function drawPipes() {
      ctx.fillStyle = "green";
      for (let p of pipes) {
        ctx.fillRect(p.x, 0, p.width, p.top);
        ctx.fillRect(p.x, p.bottom, p.width, canvas.height - p.bottom);
      }
    }

    function drawScore() {
      ctx.fillStyle = "black";
      ctx.font = "20px Arial";
      ctx.fillText("Score: " + score, 10, 30);
    }

    function createPipe() {
      const gap = 130;
      const top = Math.random() * 200 + 50;
      pipes.push({
        x: canvas.width,
        width: 60,
        top: top,
        bottom: top + gap,
        passed: false
      });
    }

    function updateGame() {
      if (isGameOver || isWon) return;

      bird.velocity += gravity;
      bird.y += bird.velocity;

      if (bird.y + bird.height > canvas.height) {
        isGameOver = true;
        document.getElementById('gameOver').style.display = 'block';
      }

      for (let p of pipes) {
        p.x -= 2;

        if (
          bird.x < p.x + p.width &&
          bird.x + bird.width > p.x &&
          (bird.y < p.top || bird.y + bird.height > p.bottom)
        ) {
          isGameOver = true;
          document.getElementById('gameOver').style.display = 'block';
        }

        if (!p.passed && p.x + p.width < bird.x) {
          p.passed = true;
          score++;
          if (score >= 100) {
            isWon = true;
            document.getElementById('win').style.display = 'block';
          }
        }
      }

      pipes = pipes.filter(p => p.x + p.width > 0);

      if (pipes.length === 0 || pipes[pipes.length - 1].x < canvas.width - 200) {
        createPipe();
      }
    }

    function drawGame() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawBird();
      drawPipes();
      drawScore();
    }

    function gameLoop() {
      updateGame();
      drawGame();
      if (!isGameOver && !isWon) {
        requestAnimationFrame(gameLoop);
      }
    }

    function vlieg() {
      bird.velocity = lift;
    }

    document.addEventListener("keydown", function(e) {
      if (e.code === "Space") {
        vlieg();
      }
    });

    vliegKnop.addEventListener("click", vlieg);

    // Start
    createPipe();
    gameLoop();
  </script>

</body>
</html>
