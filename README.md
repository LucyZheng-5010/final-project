# final-project
Although I like challenging games, this one might be a little too difficult. 
I think the reason is that sometimes the ball falls onto the flames in a way that feels out of control. 
If I allow the paddle to move across the entire canvas to catch the ball, it might become easier！
because the player would have a chance to save the ball before it lands on a randomly appearing flame.


```js
let ballX, ballY;
let speedX, speedY;
let score = 0;
let paddleW = 100;
let paddleAngle = 0; 
let gameState = "START"; 
let hitCount = 0; 
let enemies = []; 
let lastSpawnTime = 0;

function setup() {
  createCanvas(400, 600);
  resetGame();
}

function draw() {
  background(240);

  if (gameState === "START") {
    showStartScreen();
  } else if (gameState === "PLAYING") {
    playGame();
  } else if (gameState === "GAMEOVER") {
    showGameOverScreen();
  }
}



function playGame() {
  let paddleX = constrain(mouseX, paddleW / 2, width - paddleW / 2);
  let paddleY = height - 40;
  
  push();
  translate(paddleX, paddleY);
  rotate(paddleAngle);
  fill(50);
  rectMode(CENTER);
  rect(0, 0, paddleW, 15, 5);
  pop();

  ballX += speedX;
  ballY += speedY;
  textSize(30);
  textAlign(CENTER, CENTER);
  text("☄️", ballX, ballY);


  if (ballX < 15 || ballX > width - 15) speedX *= -1;
  if (ballY < 15) speedY *= -1;


  if (ballY > paddleY - 20 && ballY < paddleY && abs(ballX - paddleX) < paddleW / 2) {
    speedY = -abs(speedY) * 1.05; 
    speedX += paddleAngle * 8;   
    ballY = paddleY - 21;
    score++;
    hitCount++;
  }

  if (hitCount >= 3) {
    if (millis() - lastSpawnTime > 3000) { 
      spawnEnemy();
      lastSpawnTime = millis();
    }
  }

 
  for (let i = enemies.length - 1; i >= 0; i--) {
    let e = enemies[i];
    

    if (millis() > e.nextFlipTime) {
      e.showFire = !e.showFire;
      e.nextFlipTime = millis() + random(1000, 4000); // 1-4秒变换一次
    }

    textSize(45);
    text("🦖", e.x, e.y);


    if (e.showFire) {
      textSize(25);
      text("🔥", e.x - 40, e.y + 5); 


      if (dist(ballX, ballY, e.x - 40, e.y + 5) < 20) {
        gameState = "GAMEOVER";
      }
    }

    if (dist(ballX, ballY, e.x, e.y) < 30) {
      enemies.splice(i, 1);
      score += 5;
      speedY *= -1; 
    }
  }


  if (ballY > height) {
    gameState = "GAMEOVER";
  }

  drawScore();
}



function mouseWheel(event) {
  paddleAngle += event.delta * 0.001;
  paddleAngle = constrain(paddleAngle, -0.7, 0.7); // 稍微加大了倾斜上限
  return false;
}

function spawnEnemy() {
  let newEnemy = {
    x: random(60, width - 40),
    y: random(50, height / 2.5),
    showFire: false,
    nextFlipTime: millis() + random(1000, 4000)
  };
  enemies.push(newEnemy);
}

function resetGame() {
  ballX = width / 2;
  ballY = 150;
  speedX = random(-3, 3);
  speedY = 4;
  score = 0;
  hitCount = 0;
  enemies = [];
  paddleAngle = 0;
  lastSpawnTime = millis();
}

function mousePressed() {
  if (gameState !== "PLAYING") {
    resetGame();
    gameState = "PLAYING";
    loop(); 
  }
}

function drawScore() {
  fill(50);
  noStroke();
  textAlign(LEFT);
  textSize(20);
  text("Score: " + score, 20, 30);
  text("Hits: " + hitCount, 20, 55);
}

function showStartScreen() {
  textAlign(CENTER);
  fill(50);
  textSize(40);
  text("DINO PADDLE", width / 2, height / 2 - 20);
  textSize(18);
  text("Use Mouse to move", width / 2, height / 2 + 20);
  text("Scroll to tilt the paddle", width / 2, height / 2 + 45);
  fill(0, 102, 204);
  text("Click to Start", width / 2, height / 2 + 90);
}

function showGameOverScreen() {
  background(255, 200, 200, 10);
  fill(200, 0, 0);
  textAlign(CENTER);
  textSize(45);
  text("GAME OVER", width / 2, height / 2);
  fill(50);
  textSize(25);
  text("Final Score: " + score, width / 2, height / 2 + 50);
  textSize(15);
  text("Click to restart", width / 2, height / 2 + 90);
  noLoop();
}
