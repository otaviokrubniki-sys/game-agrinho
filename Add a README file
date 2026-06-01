<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Eco-Colheita: Agrinho 2026</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            background: #e8f5e9;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            overflow: hidden;
        }

        h1 {
            color: #2e7d32;
            margin-bottom: 5px;
            font-size: 24px;
            text-align: center;
        }

        p.sub {
            color: #558b2f;
            margin-bottom: 15px;
            font-size: 14px;
            text-align: center;
            max-width: 90%;
        }

        #game-container {
            width: 400px;
            height: 500px;
            background: linear-gradient(to bottom, #81d4fa, #a5d6a7);
            position: relative;
            overflow: hidden;
            border: 4px solid #2e7d32;
            border-radius: 12px;
            box-shadow: 0 8px 16px rgba(0,0,0,0.2);
            cursor: none; /* Esconde a seta do mouse dentro do jogo */
        }

        #tractor {
            width: 60px;
            height: 50px;
            position: absolute;
            bottom: 15px;
            left: 170px;
            font-size: 40px;
            text-align: center;
            line-height: 50px;
            transition: left 0.1s ease-out;
            user-select: none;
        }

        .item {
            position: absolute;
            font-size: 30px;
            user-select: none;
            width: 40px;
            height: 40px;
            text-align: center;
        }

        #hud {
            position: absolute;
            top: 10px;
            left: 10px;
            right: 10px;
            display: flex;
            justify-content: space-between;
            font-weight: bold;
            font-size: 18px;
            color: #1b5e20;
            background: rgba(255, 255, 255, 0.7);
            padding: 5px 10px;
            border-radius: 20px;
            pointer-events: none;
        }

        #game-over-screen {
            display: none;
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            color: white;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            text-align: center;
            padding: 20px;
            z-index: 10;
        }

        #game-over-screen h2 {
            color: #ff8a65;
            font-size: 32px;
            margin-bottom: 10px;
        }

        #game-over-screen button {
            background: #4caf50;
            color: white;
            border: none;
            padding: 10px 20px;
            font-size: 18px;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 20px;
            font-weight: bold;
        }

        #game-over-screen button:hover {
            background: #45a049;
        }

        .controls-tip {
            margin-top: 15px;
            font-size: 12px;
            color: #555;
        }
    </style>
</head>
<body>

    <h1>Eco-Colheita 🚜</h1>
    <p class="sub">Equilíbrio entre Produção e Meio Ambiente - Agrinho 2026</p>

    <div id="game-container">
        <div id="hud">
            <div id="score-display">Pontos: 0</div>
            <div id="lives-display">Vidas: ❤️❤️❤️</div>
        </div>
        
        <div id="tractor">🚜</div>

        <div id="game-over-screen">
            <h2>Fim de Jogo!</h2>
            <p id="final-message"></p>
            <p id="final-score"></p>
            <button onclick="resetGame()">Jogar Novamente</button>
        </div>
    </div>

    <p class="controls-tip">Mova o mouse para os lados dentro do quadro para controlar o trator.</p>

    <script>
        const container = document.getElementById('game-container');
        const tractor = document.getElementById('tractor');
        const scoreDisplay = document.getElementById('score-display');
        const livesDisplay = document.getElementById('lives-display');
        const gameOverScreen = document.getElementById('game-over-screen');
        const finalMessage = document.getElementById('final-message');
        const finalScore = document.getElementById('final-score');

        let score = 0;
        let lives = 3;
        let gameActive = true;
        let itemSpeed = 3;
        let spawnRate = 1200; // ms
        let gameLoopInterval;
        let spawnInterval;

        // Lista de itens do jogo
        const itemsConfig = [
            { emoji: '🌳', type: 'good', points: 10, text: 'Você plantou uma árvore!' },
            { emoji: '💧', type: 'good', points: 10, text: 'Água preservada com sucesso!' },
            { emoji: '☀️', type: 'good', points: 20, text: 'Energia limpa gerada!' },
            { emoji: '🐝', type: 'good', points: 15, text: 'Polinização protegida!' },
            { emoji: '🔥', type: 'bad', points: -15, text: 'Cuidado com as queimadas!' },
            { emoji: '☣️', type: 'bad', points: -20, text: 'Evite o uso excessivo de químicos!' },
            { emoji: '💨', type: 'bad', points: -10, text: 'Poluição reduz a produção.' }
        ];

        // Movimentação do Trator pelo Mouse
        container.addEventListener('mousemove', (e) => {
            if (!gameActive) return;
            const rect = container.getBoundingClientRect();
            let relativeX = e.clientX - rect.left;
            
            // Centraliza o trator no mouse e limita dentro das bordas
            let tractorLeft = relativeX - 30; 
            if (tractorLeft < 0) tractorLeft = 0;
            if (tractorLeft > 340) tractorLeft = 340; // 400 largura - 60 do trator
            
            tractor.style.left = tractorLeft + 'px';
        });

        // Função para criar um item caindo
        function spawnItem() {
            if (!gameActive) return;

            const itemEl = document.createElement('div');
            itemEl.classList.add('item');
            
            // Escolhe um item aleatório da lista
            const config = itemsConfig[Math.floor(Math.random() * itemsConfig.length)];
            itemEl.innerText = config.emoji;
            itemEl.dataset.type = config.type;
            itemEl.dataset.points = config.points;
            itemEl.dataset.text = config.text;

            // Posição X aleatória
            const randomX = Math.floor(Math.random() * 360);
            itemEl.style.left = randomX + 'px';
            itemEl.style.top = '-40px';

            container.appendChild(itemEl);

            // Lógica de queda do item
            let currentTop = -40;
            const fallTimer = setInterval(() => {
                if (!gameActive) {
                    clearInterval(fallTimer);
                    return;
                }

                currentTop += itemSpeed;
                itemEl.style.top = currentTop + 'px';

                // Verifica colisão com o trator
                const tractorLeft = parseInt(tractor.style.left || 170);
                const itemLeft = parseInt(itemEl.style.left);

                // Detecção de colisão simples (caixa de proximidade)
                if (currentTop >= 435 && currentTop <= 470) {
                    if (itemLeft + 30 >= tractorLeft && itemLeft <= tractorLeft + 60) {
                        handleCollision(config);
                        itemEl.remove();
                        clearInterval(fallTimer);
                    }
                }

                // Se passou do fundo da tela
                if (currentTop > 500) {
                    if (config.type === 'good') {
                        // Perde vida se deixar passar item sustentável importante
                        updateLives(-1);
                    }
                    itemEl.remove();
                    clearInterval(fallTimer);
                }
            }, 20);
        }

        function handleCollision(item) {
            score += parseInt(item.points);
            if (score < 0) score = 0;
            scoreDisplay.innerText = `Pontos: ${score}`;

            if (item.type === 'bad') {
                updateLives(-1);
                // Feedback visual de erro piscando a tela
                container.style.border = '4px solid #d32f2f';
                setTimeout(() => container.style.border = '4px solid #2e7d32', 200);
            }

            // Aumenta a velocidade gradualmente conforme ganha pontos
            if (score > 0 && score % 100 === 0) {
                itemSpeed += 0.5;
            }
        }

        function updateLives(change) {
            lives += change;
            if (lives <= 0) {
                lives = 0;
                endGame();
            }
            livesDisplay.innerText = `Vidas: ${'❤️'.repeat(lives)}${'🖤'.repeat(3 - lives)}`;
        }

        function endGame() {
            gameActive = false;
            clearInterval(spawnInterval);
            
            // Remove itens restantes na tela
            document.querySelectorAll('.item').forEach(el => el.remove());

            finalScore.innerText = `Sua Pontuação: ${score} pontos`;
            
            if (score >= 200) {
                finalMessage.innerText = "Excelente! Você provou que o Agro Forte e o Futuro Sustentável caminham juntos! 🌾✨";
            } else {
                finalMessage.innerText = "Bom esforço! Mas precisamos proteger mais o meio ambiente para garantir a produção futura. 🌎";
            }

            gameOverScreen.style.display = 'flex';
        }

        function resetGame() {
            score = 0;
            lives = 3;
            itemSpeed = 3;
            gameActive = true;
            scoreDisplay.innerText = `Pontos: ${score}`;
            livesDisplay.innerText = `Vidas: ❤️❤️❤️`;
            gameOverScreen.style.display = 'none';
            tractor.style.left = '170px';
            
            // Reinicia os geradores
            clearInterval(spawnInterval);
            spawnInterval = setInterval(spawnItem, spawnRate);
        }

        // Inicia o jogo
        spawnInterval = setInterval(spawnItem, spawnRate);
    </script>
</body>
</html>
