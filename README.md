<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>除夕快乐 - 自然与烟花</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: linear-gradient(to bottom, #FFD700, #FF6347, #8B0000);
            font-family: "华文行楷", cursive;
        }
        canvas {
            display: block;
        }
        .text {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            color: white;
            font-size: 2rem;
            text-align: center;
            opacity: 0;
            animation: fadeIn 5s forwards;
        }
        @keyframes fadeIn {
            to { opacity: 1; }
        }
    </style>
</head>
<body>
    <canvas id="scene"></canvas>
    <div class="text">除夕快乐，岁岁如意</div>

    <script>
        const canvas = document.getElementById('scene');
        const ctx = canvas.getContext('2d');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        // 夕阳背景
        function drawSunset() {
            const gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
            gradient.addColorStop(0, '#FFD700');
            gradient.addColorStop(0.5, '#FF6347');
            gradient.addColorStop(1, '#8B0000');
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
        }

        // 海洋波浪
        function drawOcean() {
            ctx.beginPath();
            ctx.moveTo(0, canvas.height * 0.7);
            for (let x = 0; x < canvas.width; x += 10) {
                const y = canvas.height * 0.7 + Math.sin(x * 0.02) * 10;
                ctx.lineTo(x, y);
            }
            ctx.lineTo(canvas.width, canvas.height);
            ctx.lineTo(0, canvas.height);
            ctx.closePath();
            ctx.fillStyle = '#000080';
            ctx.fill();
        }

        // 烟花粒子
        const fireworks = [];
        class Firework {
            constructor() {
                this.x = Math.random() * canvas.width;
                this.y = canvas.height;
                this.color = `hsl(${Math.random() * 360}, 100%, 50%)`;
                this.particles = [];
                this.exploded = false;
            }
            update() {
                if (!this.exploded) {
                    this.y -= 5;
                    if (this.y < canvas.height * 0.3) {
                        this.explode();
                    }
                } else {
                    this.particles.forEach(p => {
                        p.x += Math.cos(p.angle) * p.speed;
                        p.y += Math.sin(p.angle) * p.speed;
                        p.speed *= 0.95;
                    });
                }
            }
            explode() {
                this.exploded = true;
                for (let i = 0; i < 100; i++) {
                    const angle = (Math.PI * 2) * (i / 100);
                    this.particles.push({
                        x: this.x,
                        y: this.y,
                        angle: angle,
                        speed: Math.random() * 5 + 2
                    });
                }
            }
            draw() {
                if (!this.exploded) {
                    ctx.fillStyle = this.color;
                    ctx.beginPath();
                    ctx.arc(this.x, this.y, 3, 0, Math.PI * 2);
                    ctx.fill();
                } else {
                    this.particles.forEach(p => {
                        ctx.fillStyle = this.color;
                        ctx.beginPath();
                        ctx.arc(p.x, p.y, 2, 0, Math.PI * 2);
                        ctx.fill();
                    });
                }
            }
        }

        // 动画循环
        function animate() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            drawSunset();
            drawOcean();

            // 添加烟花
            if (Math.random() < 0.05) {
                fireworks.push(new Firework());
            }

            // 更新和绘制烟花
            fireworks.forEach((firework, index) => {
                firework.update();
                firework.draw();
                if (firework.exploded && firework.particles[0].speed < 0.1) {
                    fireworks.splice(index, 1);
                }
            });

            requestAnimationFrame(animate);
        }

        // 启动动画
        animate();
    </script>
</body>
</html>
