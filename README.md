# MEMO-FIT-
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jogo da Memória Personalizável</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
            color: white;
        }

        h1 {
            margin: 20px 0;
            text-align: center;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }

        .container {
            display: flex;
            flex-direction: column;
            align-items: center;
            max-width: 1200px;
            width: 100%;
        }

        .controls {
            background-color: rgba(255, 255, 255, 0.1);
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
            width: 100%;
            max-width: 800px;
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 15px;
            backdrop-filter: blur(5px);
        }

        .control-group {
            display: flex;
            flex-direction: column;
            gap: 5px;
        }

        label {
            font-weight: bold;
            font-size: 14px;
        }

        input, select, button {
            padding: 8px 12px;
            border: none;
            border-radius: 5px;
            font-size: 14px;
        }

        button {
            background-color: #FFD700;
            color: #333;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        button:hover {
            background-color: #FFEC8B;
            transform: translateY(-2px);
        }

        button:disabled {
            background-color: #cccccc;
            cursor: not-allowed;
            transform: none;
        }

        .game-info {
            display: flex;
            justify-content: space-between;
            width: 100%;
            max-width: 800px;
            margin-bottom: 20px;
        }

        .info-box {
            background-color: rgba(255, 255, 255, 0.2);
            padding: 10px 20px;
            border-radius: 10px;
            text-align: center;
            min-width: 120px;
        }

        .memory-game {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
            max-width: 800px;
            width: 100%;
            margin: 0 auto;
        }

        .card {
            height: 150px;
            background-color: #1a1a2e;
            border-radius: 10px;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            transform-style: preserve-3d;
            transition: transform 0.5s;
            position: relative;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }

        .card.flipped {
            transform: rotateY(180deg);
        }

        .card-front, .card-back {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            border-radius: 10px;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow: hidden;
        }

        .card-front {
            background-color: #1a1a2e;
            transform: rotateY(180deg);
        }

        .card-front img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        .card-back {
            background: linear-gradient(45deg, #16213e, #0f3460);
            color: white;
            font-size: 2rem;
        }

        .matched {
            visibility: hidden;
            cursor: default;
        }

        .win-message {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.8);
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            z-index: 100;
            display: none;
        }

        .win-content {
            background: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%);
            padding: 40px;
            border-radius: 15px;
            text-align: center;
            max-width: 500px;
            width: 90%;
        }

        .win-content h2 {
            font-size: 2.5rem;
            margin-bottom: 20px;
        }

        .win-content p {
            font-size: 1.2rem;
            margin-bottom: 20px;
        }

        .image-preview {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            margin-top: 10px;
            max-width: 100%;
        }

        .image-preview img {
            width: 60px;
            height: 60px;
            object-fit: cover;
            border-radius: 5px;
            border: 2px solid white;
        }

        .message {
            padding: 10px;
            border-radius: 5px;
            margin-top: 5px;
            text-align: center;
            font-size: 14px;
        }

        .error {
            background-color: rgba(255, 0, 0, 0.2);
            color: #ff6b6b;
        }

        .success {
            background-color: rgba(0, 255, 0, 0.2);
            color: #51cf66;
        }

        @media (max-width: 768px) {
            .memory-game {
                grid-template-columns: repeat(3, 1fr);
            }
            
            .card {
                height: 120px;
            }
        }

        @media (max-width: 480px) {
            .memory-game {
                grid-template-columns: repeat(2, 1fr);
            }
            
            .card {
                height: 100px;
            }
            
            .controls {
                flex-direction: column;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Jogo da Memória Personalizável</h1>
        
        <div class="controls">
            <div class="control-group">
                <label for="imageUpload">Carregar Imagens (mínimo 2):</label>
                <input type="file" id="imageUpload" accept="image/*" multiple>
                <div class="image-preview" id="imagePreview"></div>
                <div id="uploadMessage" class="message"></div>
            </div>
            
            <div class="control-group">
                <label for="gridSize">Tamanho do Tabuleiro:</label>
                <select id="gridSize">
                    <option value="4">2x2 (4 cartas)</option>
                    <option value="6">3x2 (6 cartas)</option>
                    <option value="8" selected>4x2 (8 cartas)</option>
                    <option value="12">4x3 (12 cartas)</option>
                    <option value="16">4x4 (16 cartas)</option>
                </select>
            </div>
            
            <div class="control-group">
                <label for="theme">Tema (se não usar imagens próprias):</label>
                <select id="theme">
                    <option value="default">Padrão</option>
                    <option value="nature">Natureza</option>
                    <option value="animals">Animais</option>
                    <option value="food">Comida</option>
                </select>
            </div>
            
            <button id="startGame" disabled>Iniciar Jogo</button>
            <button id="resetGame">Reiniciar</button>
        </div>
        
        <div class="game-info">
            <div class="info-box">
                <p>Movimentos: <span id="moves">0</span></p>
            </div>
            <div class="info-box">
                <p>Pares Encontrados: <span id="pairs">0</span>/<span id="totalPairs">0</span></p>
            </div>
            <div class="info-box">
                <p>Tempo: <span id="timer">0</span>s</p>
            </div>
        </div>
        
        <div class="memory-game" id="memoryGame"></div>
    </div>
    
    <div class="win-message" id="winMessage">
        <div class="win-content">
            <h2>Parabéns!</h2>
            <p>Você completou o jogo da memória!</p>
            <p>Movimentos: <span id="finalMoves">0</span></p>
            <p>Tempo: <span id="finalTime">0</span> segundos</p>
            <button id="playAgain">Jogar Novamente</button>
        </div>
    </div>

    <script>
        // Elementos do DOM
        const memoryGame = document.getElementById('memoryGame');
        const imageUpload = document.getElementById('imageUpload');
        const imagePreview = document.getElementById('imagePreview');
        const uploadMessage = document.getElementById('uploadMessage');
        const gridSize = document.getElementById('gridSize');
        const theme = document.getElementById('theme');
        const startGame = document.getElementById('startGame');
        const resetGame = document.getElementById('resetGame');
        const movesDisplay = document.getElementById('moves');
        const pairsDisplay = document.getElementById('pairs');
        const totalPairsDisplay = document.getElementById('totalPairs');
        const timerDisplay = document.getElementById('timer');
        const winMessage = document.getElementById('winMessage');
        const finalMoves = document.getElementById('finalMoves');
        const finalTime = document.getElementById('finalTime');
        const playAgain = document.getElementById('playAgain');

        // Variáveis do jogo
        let cards = [];
        let flippedCards = [];
        let matchedPairs = 0;
        let totalPairs = 0;
        let moves = 0;
        let timer = 0;
        let timerInterval;
        let gameStarted = false;
        let userImages = [];

        // Imagens padrão por tema
        const defaultImages = {
            default: [
                '😀', '🎮', '🚀', '⭐',
                '🐱', '🐶', '🐼', '🐯',
                '🍎', '🍕', '🍦', '🍩',
                '⚽', '🎸', '🎨', '📚'
            ],
            nature: [
                '🌲', '🌻', '🌊', '⛰️',
                '🌈', '☀️', '🌙', '⭐',
                '🌸', '🍂', '❄️', '🌧️',
                '🍀', '🌵', '🐚', '🌺'
            ],
            animals: [
                '🐶', '🐱', '🐭', '🐹',
                '🐰', '🦊', '🐻', '🐼',
                '🐨', '🐯', '🦁', '🐮',
                '🐷', '🐸', '🐵', '🐔'
            ],
            food: [
                '🍎', '🍌', '🍇', '🍓',
                '🍕', '🍔', '🌭', '🍿',
                '🍩', '🍪', '🍰', '🧁',
                '🍦', '🍫', '🍬', '🍭'
            ]
        };

        // Event Listeners
        startGame.addEventListener('click', initializeGame);
        resetGame.addEventListener('click', resetGameState);
        playAgain.addEventListener('click', () => {
            winMessage.style.display = 'none';
            resetGameState();
        });

        imageUpload.addEventListener('change', handleImageUpload);

        // Função para carregar imagens do usuário
        function handleImageUpload(event) {
            const files = event.target.files;
            userImages = [];
            imagePreview.innerHTML = '';
            uploadMessage.textContent = '';
            
            if (files.length > 0) {
                let loadedImages = 0;
                const totalImages = files.length;
                
                for (let i = 0; i < files.length; i++) {
                    const reader = new FileReader();
                    reader.onload = function(e) {
                        userImages.push({
                            id: i,
                            src: e.target.result
                        });
                        
                        // Mostrar preview da imagem
                        const img = document.createElement('img');
                        img.src = e.target.result;
                        img.alt = `Imagem ${i+1}`;
                        imagePreview.appendChild(img);
                        
                        loadedImages++;
                        
                        // Verificar se todas as imagens foram carregadas
                        if (loadedImages === totalImages) {
                            const minImages = parseInt(gridSize.value) / 2;
                            
                            if (userImages.length >= minImages) {
                                uploadMessage.textContent = `Pronto! ${userImages.length} imagens carregadas.`;
                                uploadMessage.className = 'message success';
                                startGame.disabled = false;
                            } else {
                                uploadMessage.textContent = `Carregue pelo menos ${minImages} imagens para este tamanho de tabuleiro.`;
                                uploadMessage.className = 'message error';
                                startGame.disabled = true;
                            }
                        }
                    };
                    reader.onerror = function() {
                        uploadMessage.textContent = 'Erro ao carregar uma das imagens.';
                        uploadMessage.className = 'message error';
                    };
                    reader.readAsDataURL(files[i]);
                }
            } else {
                startGame.disabled = false;
            }
        }

        // Inicializar o jogo
        function initializeGame() {
            resetGameState();
            
            const selectedGridSize = parseInt(gridSize.value);
            const selectedTheme = theme.value;
            
            // Determinar o número de pares
            totalPairs = selectedGridSize / 2;
            totalPairsDisplay.textContent = totalPairs;
            
            // Obter imagens para o jogo
            let gameImages = [];
            
            if (userImages.length >= totalPairs) {
                // Usar imagens do usuário
                gameImages = userImages.slice(0, totalPairs).map(img => img.src);
            } else {
                // Usar imagens padrão do tema selecionado
                const themeImages = defaultImages[selectedTheme] || defaultImages.default;
                gameImages = themeImages.slice(0, totalPairs);
                
                // Se não há imagens suficientes, repetir algumas
                while (gameImages.length < totalPairs) {
                    gameImages.push(...themeImages.slice(0, totalPairs - gameImages.length));
                }
            }
            
            // Duplicar as imagens para formar pares
            cards = [...gameImages, ...gameImages];
            
            // Embaralhar as cartas
            shuffleCards();
            
            // Criar o tabuleiro do jogo
            createBoard(selectedGridSize);
            
            // Iniciar o temporizador
            startTimer();
            gameStarted = true;
        }

        // Embaralhar as cartas
        function shuffleCards() {
            for (let i = cards.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [cards[i], cards[j]] = [cards[j], cards[i]];
            }
        }

        // Criar o tabuleiro do jogo
        function createBoard(size) {
            memoryGame.innerHTML = '';
            const columns = Math.sqrt(size);
            memoryGame.style.gridTemplateColumns = `repeat(${columns}, 1fr)`;
            
            for (let i = 0; i < size; i++) {
                const card = document.createElement('div');
                card.classList.add('card');
                card.dataset.index = i;
                card.dataset.value = cards[i];
                
                const cardFront = document.createElement('div');
                cardFront.classList.add('card-front');
                
                // Verificar se é uma imagem (data URL) ou um emoji
                if (typeof cards[i] === 'string' && cards[i].startsWith('data:image')) {
                    const img = document.createElement('img');
                    img.src = cards[i];
                    img.alt = `Imagem ${i}`;
                    cardFront.appendChild(img);
                } else {
                    cardFront.textContent = cards[i];
                    cardFront.style.fontSize = '3rem';
                }
                
                const cardBack = document.createElement('div');
                cardBack.classList.add('card-back');
                cardBack.textContent = '?';
                
                card.appendChild(cardFront);
                card.appendChild(cardBack);
                
                card.addEventListener('click', flipCard);
                memoryGame.appendChild(card);
            }
        }

        // Virar uma carta
        function flipCard() {
            if (!gameStarted) return;
            
            const card = this;
            
            // Não permitir virar cartas já viradas ou combinadas
            if (card.classList.contains('flipped') || card.classList.contains('matched')) {
                return;
            }
            
            // Não permitir virar mais de duas cartas ao mesmo tempo
            if (flippedCards.length >= 2) {
                return;
            }
            
            // Virar a carta
            card.classList.add('flipped');
            flippedCards.push(card);
            
            // Verificar se duas cartas foram viradas
            if (flippedCards.length === 2) {
                moves++;
                movesDisplay.textContent = moves;
                
                checkForMatch();
            }
        }

        // Verificar se as cartas viradas formam um par
        function checkForMatch() {
            const [card1, card2] = flippedCards;
            const value1 = card1.dataset.value;
            const value2 = card2.dataset.value;
            
            if (value1 === value2) {
                // Cartas combinam
                setTimeout(() => {
                    card1.classList.add('matched');
                    card2.classList.add('matched');
                    flippedCards = [];
                    
                    matchedPairs++;
                    pairsDisplay.textContent = matchedPairs;
                    
                    // Verificar se o jogo terminou
                    if (matchedPairs === totalPairs) {
                        endGame();
                    }
                }, 500);
            } else {
                // Cartas não combinam
                setTimeout(() => {
                    card1.classList.remove('flipped');
                    card2.classList.remove('flipped');
                    flippedCards = [];
                }, 1000);
            }
        }

        // Iniciar o temporizador
        function startTimer() {
            timer = 0;
            timerDisplay.textContent = timer;
            
            clearInterval(timerInterval);
            timerInterval = setInterval(() => {
                timer++;
                timerDisplay.textContent = timer;
            }, 1000);
        }

        // Finalizar o jogo
        function endGame() {
            clearInterval(timerInterval);
            gameStarted = false;
            
            finalMoves.textContent = moves;
            finalTime.textContent = timer;
            winMessage.style.display = 'flex';
        }

        // Reiniciar o jogo
        function resetGameState() {
            clearInterval(timerInterval);
            memoryGame.innerHTML = '';
            flippedCards = [];
            matchedPairs = 0;
            moves = 0;
            timer = 0;
            
            movesDisplay.textContent = moves;
            pairsDisplay.textContent = matchedPairs;
            timerDisplay.textContent = timer;
            totalPairsDisplay.textContent = '0';
            
            gameStarted = false;
        }

        // Inicializar com um jogo padrão
        window.addEventListener('DOMContentLoaded', () => {
            // Iniciar com um jogo usando emojis por padrão
            initializeGame();
        });
    </script>
</body>
</html>
