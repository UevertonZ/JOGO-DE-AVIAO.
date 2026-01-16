
<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Avião Turbo</title>
    <style>
        body { margin: 0; overflow: hidden; background: #4db8ff; font-family: sans-serif; }
        #game-ui { position: absolute; top: 10px; left: 10px; color: white; font-size: 20px; font-weight: bold; text-shadow: 2px 2px #000; }
        canvas { display: block; }
    </style>
</head>
<body>
    <div id="game-ui">DISTÂNCIA: <span id="dist">0</span>m</div>
    <canvas id="planeCanvas"></canvas>

    <script>
        const canvas = document.getElementById("planeCanvas");
        const ctx = canvas.getContext("2d");
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        let plane = { x: 50, y: canvas.height/2, w: 40, h: 20, v: 0 };
        let gravity = 0.4;
        let lift = -8;
        let obstacles = [];
        let distance = 0;

        // Criar obstáculos (Nuvens ou Prédios)
        function spawnObstacle() {
            let h = Math.random() * (canvas.height / 2);
            obstacles.push({ x: canvas.width, y: 0, w: 50, h: h, type: 'top' });
            obstacles.push({ x: canvas.width, y: h + 150, w: 50, h: canvas.height, type: 'bottom' });
        }

        function update() {
            plane.v += gravity;
            plane.y += plane.v;
            distance++;
            document.getElementById("dist").innerText = Math.floor(distance/10);

            // Mover obstáculos
            obstacles.forEach((obs, i) => {
                obs.x -= 5;
                if(obs.x + obs.w < 0) obstacles.splice(i, 1);

                // Colisão simples
                if (plane.x < obs.x + obs.w && plane.x + plane.w > obs.x &&
                    plane.y < obs.y + obs.h && plane.y + plane.h > obs.y) {
                    reset();
                }
            });

            if(distance % 100 === 0) spawnObstacle();
            if(plane.y > canvas.height || plane.y < 0) reset();
        }

        function reset() {
            plane.y = canvas.height/2;
            plane.v = 0;
            obstacles = [];
            distance = 0;
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Desenhar Avião (Corpinho Vermelho)
            ctx.fillStyle = "red";
            ctx.fillRect(plane.x, plane.y, plane.w, plane.h);
            ctx.fillStyle = "white"; // Asa
            ctx.fillRect(plane.x + 10, plane.y - 5, 10, 30);

            // Desenhar Obstáculos
            ctx.fillStyle = "rgba(255,255,255,0.8)";
            obstacles.forEach(obs => {
                ctx.roundRect ? ctx.fill(new Path2D(`M${obs.x},${obs.y} h${obs.w} v${obs.h} h-${obs.w} z`)) : ctx.fillRect(obs.x, obs.y, obs.w, obs.h);
                ctx.fillRect(obs.x, obs.y, obs.w, obs.h);
            });

            update();
            requestAnimationFrame(draw);
        }

        // Toque para voar
        window.addEventListener("touchstart", () => {
            plane.v = lift;
        });

        spawnObstacle();
        draw();
    </script>
</body>
</html>
