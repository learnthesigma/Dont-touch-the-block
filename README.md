const canvas = document.getElementById('gameCanvas');
const context = canvas.getContext('2d');

// Player object
const player = {
    x: canvas.width / 2 - 25,
    y: canvas.height / 2 - 25,
    width: 50,
    height: 50,
    color: 'blue',
    speed: 5
};

// Red block (obstacle) object
const redBlock = {
    x: Math.random() * (canvas.width - 50),
    y: Math.random() * (canvas.height - 50),
    width: 50,
    height: 50,
    color: 'red',
    speedX: 2,
    speedY: 2
};

// Game state
let score = 0;
let highScore = 0;

// Key press state
const keys = {
    left: false,
    right: false,
    up: false,
    down: false
};

// Event listeners for key presses
window.addEventListener('keydown', (e) => {
    if (e.key === 'ArrowLeft') keys.left = true;
    if (e.key === 'ArrowRight') keys.right = true;
    if (e.key === 'ArrowUp') keys.up = true;
    if (e.key === 'ArrowDown') keys.down = true;
});

window.addEventListener('keyup', (e) => {
    if (e.key === 'ArrowLeft') keys.left = false;
    if (e.key === 'ArrowRight') keys.right = false;
    if (e.key === 'ArrowUp') keys.up = false;
    if (e.key === 'ArrowDown') keys.down = false;
});

// Update game objects
function update() {
    // Update player position
    if (keys.left) player.x -= player.speed;
    if (keys.right) player.x += player.speed;
    if (keys.up) player.y -= player.speed;
    if (keys.down) player.y += player.speed;

    // Keep player within canvas bounds
    if (player.x < 0) player.x = 0;
    if (player.x + player.width > canvas.width) player.x = canvas.width - player.width;
    if (player.y < 0) player.y = 0;
    if (player.y + player.height > canvas.height) player.y = canvas.height - player.height;

    // Update red block position
    redBlock.x += redBlock.speedX;
    redBlock.y += redBlock.speedY;

    // Bounce the red block off the walls
    if (redBlock.x < 0 || redBlock.x + redBlock.width > canvas.width) {
        redBlock.speedX = -redBlock.speedX;
    }
    if (redBlock.y < 0 || redBlock.y + redBlock.height > canvas.height) {
        redBlock.speedY = -redBlock.speedY;
    }

    // Check collision with red block
    if (player.x < redBlock.x + redBlock.width &&
        player.x + player.width > redBlock.x &&
        player.y < redBlock.y + redBlock.height &&
        player.y + player.height > redBlock.y) {
        // Collision detected, reset game
        if (score > highScore) {
            highScore = score;
        }
        score = 0;
        player.x = canvas.width / 2 - 25;
        player.y = canvas.height / 2 - 25;
        redBlock.x = Math.random() * (canvas.width - 50);
        redBlock.y = Math.random() * (canvas.height - 50);
    } else {
        // Increment score if no collision
        score++;
    }

    // Update score display
    document.getElementById('score').textContent = `Score: ${score}`;
    document.getElementById('highScore').textContent = `High Score: ${highScore}`;
}

// Draw game objects
function draw() {
    context.clearRect(0, 0, canvas.width, canvas.height);
    context.fillStyle = player.color;
    context.fillRect(player.x, player.y, player.width, player.height);

    context.fillStyle = redBlock.color;
    context.fillRect(redBlock.x, redBlock.y, redBlock.width, redBlock.height);
}

// Game loop
function gameLoop() {
    update();
    draw();
    requestAnimationFrame(gameLoop);
}

// Start the game loop
gameLoop();
