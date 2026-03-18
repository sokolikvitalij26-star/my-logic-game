# my-logic-game
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>My Cool Logic Game</title>
    <style>
        body { margin: 0; overflow: hidden; background: #2c3e50; font-family: sans-serif; }
        canvas { display: block; background: #ecf0f1; margin: auto; }
        #ui { position: absolute; top: 10px; left: 10px; color: white; font-size: 20px; pointer-events: none; }
    </style>
</head>
<body>

<div id="ui">Рівень: <span id="lvl">1</span> | Кроки: <span id="steps">0</span></div>
<canvas id="gameCanvas"></canvas>

<script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    // Налаштування екрана
    function resize() {
        canvas.width = window.innerWidth > 600 ? 600 : window.innerWidth;
        canvas.height = canvas.width; // Квадратне ігрове поле
    }
    window.onresize = resize;
    resize();

    // Параметри гри
    let gridSize = 5;
    let tileSize = canvas.width / gridSize;
    let player = { x: 0, y: 0 };
    let goal = { x: 4, y: 4 };
    let steps = 0;
    let level = 1;

    function draw() {
        tileSize = canvas.width / gridSize;
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        // Малюємо сітку
        ctx.strokeStyle = "#bdc3c7";
        for (let i = 0; i < gridSize; i++) {
            for (let j = 0; j < gridSize; j++) {
                ctx.strokeRect(i * tileSize, j * tileSize, tileSize, tileSize);
            }
        }

        // Малюємо ціль (фініш)
        ctx.fillStyle = "#27ae60";
        ctx.fillRect(goal.x * tileSize + 5, goal.y * tileSize + 5, tileSize - 10, tileSize - 10);

        // Малюємо гравця
        ctx.fillStyle = "#e74c3c";
        ctx.shadowBlur = 10;
        ctx.shadowColor = "rgba(0,0,0,0.3)";
        ctx.fillRect(player.x * tileSize + 10, player.y * tileSize + 10, tileSize - 20, tileSize - 20);
        ctx.shadowBlur = 0;
    }

    function move(dx, dy) {
        let nextX = player.x + dx;
        let nextY = player.y + dy;

        if (nextX >= 0 && nextX < gridSize && nextY >= 0 && nextY < gridSize) {
            player.x = nextX;
            player.y = nextY;
            steps++;
            document.getElementById("steps").innerText = steps;
            checkWin();
        }
        draw();
    }

    function checkWin() {
        if (player.x === goal.x && player.y === goal.y) {
            alert("Перемога! Рівень пройдено за " + steps + " кроків.");
            level++;
            document.getElementById("lvl").innerText = level;
            resetLevel();
        }
    }

    function resetLevel() {
        player = { x: 0, y: 0 };
        steps = 0;
        document.getElementById("steps").innerText = steps;
        // З кожним рівнем поле стає складнішим
        if(gridSize < 10) gridSize++; 
        goal.x = gridSize - 1;
        goal.y = gridSize - 1;
    }

    // Керування клавішами (для ПК)
    window.addEventListener("keydown", (e) => {
        if (e.key === "ArrowUp") move(0, -1);
        if (e.key === "ArrowDown") move(0, 1);
        if (e.key === "ArrowLeft") move(-1, 0);
        if (e.key === "ArrowRight") move(1, 0);
    });

    // Керування тапами (для телефона)
    canvas.addEventListener("click", (e) => {
        const rect = canvas.getBoundingClientRect();
        const touchX = e.clientX - rect.left;
        const touchY = e.clientY - rect.top;
        
        const pX = player.x * tileSize + tileSize/2;
        const pY = player.y * tileSize + tileSize/2;

        if (Math.abs(touchX - pX) > Math.abs(touchY - pY)) {
            move(touchX > pX ? 1 : -1, 0);
        } else {
            move(0, touchY > pY ? 1 : -1);
        }
    });

    draw();
</script>

</body>
</html>
