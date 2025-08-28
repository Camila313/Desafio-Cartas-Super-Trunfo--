<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Super Trunfo - Carros Esportivos</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#5D5CDE',
                        'dark-bg': '#181818',
                    }
                }
            }
        }
    </script>
    <style>
        .card-flip {
            transform-style: preserve-3d;
            transition: transform 0.6s;
        }
        .card-flip.flipped {
            transform: rotateY(180deg);
        }
        .card-front, .card-back {
            backface-visibility: hidden;
        }
        .card-back {
            transform: rotateY(180deg);
        }
        .winning-glow {
            box-shadow: 0 0 20px #10B981, 0 0 40px #10B981;
        }
        .losing-glow {
            box-shadow: 0 0 20px #EF4444, 0 0 40px #EF4444;
        }
    </style>
</head>
<body class="bg-white dark:bg-dark-bg min-h-screen transition-colors duration-300">
    <!-- Header -->
    <header class="bg-primary text-white p-4 shadow-lg">
        <div class="container mx-auto flex justify-between items-center">
            <h1 class="text-2xl md:text-3xl font-bold">🏎️ Super Trunfo</h1>
            <div class="text-right">
                <div class="text-sm opacity-90">Cartas Restantes</div>
                <div class="flex gap-4 text-lg font-bold">
                    <span>Você: <span id="playerCards">10</span></span>
                    <span>CPU: <span id="cpuCards">10</span></span>
                </div>
            </div>
        </div>
    </header>

    <div class="container mx-auto p-4 max-w-6xl">
        <!-- Game Status -->
        <div id="gameStatus" class="text-center mb-6 p-4 rounded-lg bg-gray-100 dark:bg-gray-800 text-gray-800 dark:text-gray-200">
            <h2 class="text-xl font-bold mb-2">Escolha um atributo para comparar!</h2>
            <p class="text-sm opacity-75">Clique em um dos valores da sua carta para desafiar o computador</p>
        </div>

        <!-- Game Board -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
            <!-- Player Card -->
            <div class="space-y-4">
                <h3 class="text-lg font-bold text-center text-gray-800 dark:text-gray-200">Sua Carta</h3>
                <div id="playerCard" class="card-flip relative mx-auto w-80 h-96">
                    <!-- Card Front -->
                    <div class="card-front absolute inset-0 bg-gradient-to-br from-blue-500 to-blue-700 rounded-xl shadow-lg border-4 border-white">
                        <div class="p-4 h-full flex flex-col">
                            <div id="playerCardName" class="text-white font-bold text-lg mb-2 text-center"></div>
                            <div id="playerCardImage" class="flex-1 bg-white rounded-lg mb-3 flex items-center justify-center text-4xl"></div>
                            <div id="playerCardStats" class="space-y-2"></div>
                        </div>
                    </div>
                    <!-- Card Back -->
                    <div class="card-back absolute inset-0 bg-gradient-to-br from-primary to-purple-700 rounded-xl shadow-lg border-4 border-white flex items-center justify-center">
                        <div class="text-white text-center">
                            <div class="text-6xl mb-2">🏎️</div>
                            <div class="text-xl font-bold">Super Trunfo</div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- CPU Card -->
            <div class="space-y-4">
                <h3 class="text-lg font-bold text-center text-gray-800 dark:text-gray-200">Carta do Computador</h3>
                <div id="cpuCard" class="card-flip relative mx-auto w-80 h-96">
                    <!-- Card Front -->
                    <div class="card-front absolute inset-0 bg-gradient-to-br from-red-500 to-red-700 rounded-xl shadow-lg border-4 border-white">
                        <div class="p-4 h-full flex flex-col">
                            <div id="cpuCardName" class="text-white font-bold text-lg mb-2 text-center"></div>
                            <div id="cpuCardImage" class="flex-1 bg-white rounded-lg mb-3 flex items-center justify-center text-4xl"></div>
                            <div id="cpuCardStats" class="space-y-2"></div>
                        </div>
                    </div>
                    <!-- Card Back -->
                    <div class="card-back absolute inset-0 bg-gradient-to-br from-primary to-purple-700 rounded-xl shadow-lg border-4 border-white flex items-center justify-center">
                        <div class="text-white text-center">
                            <div class="text-6xl mb-2">🏎️</div>
                            <div class="text-xl font-bold">Super Trunfo</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Action Buttons -->
        <div class="text-center space-y-4">
            <button id="nextRoundBtn" class="bg-primary hover:bg-purple-600 text-white px-8 py-3 rounded-lg font-bold text-lg transition-colors disabled:opacity-50 disabled:cursor-not-allowed" disabled>
                Próxima Rodada
            </button>
            <button id="newGameBtn" class="bg-green-600 hover:bg-green-700 text-white px-6 py-2 rounded-lg font-bold transition-colors hidden">
                Novo Jogo
            </button>
        </div>
    </div>

    <!-- Game Over Modal -->
    <div id="gameOverModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 hidden">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-xl shadow-2xl max-w-md w-full mx-4 text-center">
            <div id="gameOverContent"></div>
            <button id="newGameModalBtn" class="mt-6 bg-primary hover:bg-purple-600 text-white px-6 py-3 rounded-lg font-bold transition-colors">
                Jogar Novamente
            </button>
        </div>
    </div>

    <script>
        // Dark mode setup
        if (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) {
            document.documentElement.classList.add('dark');
        }
        window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', event => {
            if (event.matches) {
                document.documentElement.classList.add('dark');
            } else {
                document.documentElement.classList.remove('dark');
            }
        });

        // Game data
        const cars = [
            { name: "Ferrari F8 Tributo", emoji: "🏎️", speed: 340, power: 720, acceleration: 2.9, price: 280, design: 95 },
            { name: "Lamborghini Huracán", emoji: "🚗", speed: 325, power: 640, acceleration: 3.2, price: 250, design: 92 },
            { name: "McLaren 720S", emoji: "🏁", speed: 341, power: 710, acceleration: 2.8, price: 300, design: 88 },
            { name: "Porsche 911 Turbo S", emoji: "🚙", speed: 330, power: 650, acceleration: 2.7, price: 220, design: 85 },
            { name: "Audi R8 V10 Plus", emoji: "🛻", speed: 320, power: 610, acceleration: 3.1, price: 200, design: 87 },
            { name: "BMW i8", emoji: "🚗", speed: 250, power: 374, acceleration: 4.4, price: 150, design: 90 },
            { name: "Nissan GT-R Nismo", emoji: "🏎️", speed: 315, power: 600, acceleration: 2.5, price: 180, design: 83 },
            { name: "Chevrolet Corvette Z06", emoji: "🚗", speed: 312, power: 670, acceleration: 2.9, price: 170, design: 86 },
            { name: "Aston Martin Vantage", emoji: "🏁", speed: 314, power: 503, acceleration: 3.6, price: 190, design: 94 },
            { name: "Mercedes AMG GT S", emoji: "🚙", speed: 310, power: 630, acceleration: 3.7, price: 165, design: 89 },
            { name: "Jaguar F-Type SVR", emoji: "🛻", speed: 322, power: 575, acceleration: 3.5, price: 155, design: 88 },
            { name: "Maserati MC20", emoji: "🏎️", speed: 325, power: 630, acceleration: 2.9, price: 240, design: 91 },
            { name: "Bugatti Chiron", emoji: "🚗", speed: 420, power: 1500, acceleration: 2.4, price: 3000, design: 98 },
            { name: "Koenigsegg Agera RS", emoji: "🏁", speed: 447, power: 1360, acceleration: 2.8, price: 2500, design: 96 },
            { name: "Pagani Huayra", emoji: "🚙", speed: 383, power: 730, acceleration: 3.2, price: 2800, design: 99 },
            { name: "Hennessey Venom F5", emoji: "🛻", speed: 484, power: 1817, acceleration: 2.6, price: 1800, design: 94 },
            { name: "Tesla Roadster", emoji: "⚡", speed: 400, power: 800, acceleration: 1.9, price: 200, design: 85 },
            { name: "Ford GT", emoji: "🏎️", speed: 347, power: 647, acceleration: 3.5, price: 450, design: 93 },
            { name: "Alpine A110", emoji: "🚗", speed: 250, power: 252, acceleration: 4.5, price: 60, design: 82 },
            { name: "Lotus Evija", emoji: "⚡", speed: 320, power: 2000, acceleration: 2.9, price: 2100, design: 87 }
        ];

        const attributes = {
            speed: { name: "Velocidade", unit: "km/h", icon: "⚡" },
            power: { name: "Potência", unit: "cv", icon: "🔥" },
            acceleration: { name: "Aceleração", unit: "s", icon: "🚀", inverse: true },
            price: { name: "Preço", unit: "mil €", icon: "💰" },
            design: { name: "Design", unit: "/100", icon: "✨" }
        };

        class SuperTrunfoGame {
            constructor() {
                this.playerDeck = [];
                this.cpuDeck = [];
                this.currentPlayerCard = null;
                this.currentCpuCard = null;
                this.isPlayerTurn = true;
                this.gameOver = false;
                this.selectedAttribute = null;
                
                this.initializeGame();
                this.setupEventListeners();
            }

            initializeGame() {
                // Shuffle and distribute cards
                const shuffledCars = [...cars].sort(() => Math.random() - 0.5);
                this.playerDeck = shuffledCars.slice(0, 10);
                this.cpuDeck = shuffledCars.slice(10, 20);
                
                this.drawCards();
                this.updateCardCounts();
                this.updateGameStatus("Escolha um atributo para comparar!");
            }

            drawCards() {
                this.currentPlayerCard = this.playerDeck[0];
                this.currentCpuCard = this.cpuDeck[0];
                
                this.displayPlayerCard();
                this.displayCpuCard(false); // CPU card hidden initially
            }

            displayPlayerCard() {
                const cardElement = document.getElementById('playerCard');
                const nameElement = document.getElementById('playerCardName');
                const imageElement = document.getElementById('playerCardImage');
                const statsElement = document.getElementById('playerCardStats');

                nameElement.textContent = this.currentPlayerCard.name;
                imageElement.textContent = this.currentPlayerCard.emoji;
                
                statsElement.innerHTML = '';
                Object.entries(attributes).forEach(([key, attr]) => {
                    const value = this.currentPlayerCard[key];
                    const statDiv = document.createElement('div');
                    statDiv.className = 'bg-white bg-opacity-20 rounded p-2 cursor-pointer hover:bg-opacity-30 transition-colors text-base';
                    statDiv.innerHTML = `
                        <div class="flex justify-between items-center text-white">
                            <span class="font-medium">${attr.icon} ${attr.name}</span>
                            <span class="font-bold">${value}${attr.unit}</span>
                        </div>
                    `;
                    statDiv.addEventListener('click', () => this.selectAttribute(key));
                    statsElement.appendChild(statDiv);
                });

                cardElement.classList.remove('flipped');
            }

            displayCpuCard(revealed = false) {
                const cardElement = document.getElementById('cpuCard');
                const nameElement = document.getElementById('cpuCardName');
                const imageElement = document.getElementById('cpuCardImage');
                const statsElement = document.getElementById('cpuCardStats');

                if (revealed) {
                    nameElement.textContent = this.currentCpuCard.name;
                    imageElement.textContent = this.currentCpuCard.emoji;
                    
                    statsElement.innerHTML = '';
                    Object.entries(attributes).forEach(([key, attr]) => {
                        const value = this.currentCpuCard[key];
                        const statDiv = document.createElement('div');
                        statDiv.className = 'bg-white bg-opacity-20 rounded p-2 text-base';
                        statDiv.innerHTML = `
                            <div class="flex justify-between items-center text-white">
                                <span class="font-medium">${attr.icon} ${attr.name}</span>
                                <span class="font-bold">${value}${attr.unit}</span>
                            </div>
                        `;
                        statsElement.appendChild(statDiv);
                    });
                    cardElement.classList.remove('flipped');
                } else {
                    cardElement.classList.add('flipped');
                }
            }

            selectAttribute(attribute) {
                if (this.selectedAttribute || this.gameOver) return;
                
                this.selectedAttribute = attribute;
                this.displayCpuCard(true);
                
                setTimeout(() => {
                    this.compareCards(attribute);
                }, 1000);
            }

            compareCards(attribute) {
                const playerValue = this.currentPlayerCard[attribute];
                const cpuValue = this.currentCpuCard[attribute];
                const attr = attributes[attribute];
                
                let playerWins;
                if (attr.inverse) {
                    playerWins = playerValue < cpuValue; // Lower is better for acceleration
                } else {
                    playerWins = playerValue > cpuValue; // Higher is better for others
                }

                this.highlightWinner(playerWins);
                
                setTimeout(() => {
                    if (playerWins) {
                        this.playerWins();
                    } else if (playerValue === cpuValue) {
                        this.tie();
                    } else {
                        this.cpuWins();
                    }
                }, 1500);
            }

            highlightWinner(playerWins) {
                const playerCard = document.getElementById('playerCard');
                const cpuCard = document.getElementById('cpuCard');
                
                if (playerWins) {
                    playerCard.classList.add('winning-glow');
                    cpuCard.classList.add('losing-glow');
                    this.updateGameStatus("🎉 Você ganhou esta rodada!");
                } else {
                    playerCard.classList.add('losing-glow');
                    cpuCard.classList.add('winning-glow');
                    this.updateGameStatus("😔 O computador ganhou esta rodada!");
                }
            }

            playerWins() {
                // Player gets both cards
                const cpuCard = this.cpuDeck.shift();
                const playerCard = this.playerDeck.shift();
                this.playerDeck.push(playerCard, cpuCard);
                
                this.isPlayerTurn = true;
                this.endRound();
            }

            cpuWins() {
                // CPU gets both cards
                const playerCard = this.playerDeck.shift();
                const cpuCard = this.cpuDeck.shift();
                this.cpuDeck.push(cpuCard, playerCard);
                
                this.isPlayerTurn = false;
                this.endRound();
            }

            tie() {
                // Each player keeps their card
                this.playerDeck.shift();
                this.cpuDeck.shift();
                this.playerDeck.push(this.currentPlayerCard);
                this.cpuDeck.push(this.currentCpuCard);
                
                this.updateGameStatus("🤝 Empate! Cada um mantém sua carta.");
                this.endRound();
            }

            endRound() {
                this.updateCardCounts();
                
                if (this.playerDeck.length === 0) {
                    this.endGame(false);
                } else if (this.cpuDeck.length === 0) {
                    this.endGame(true);
                } else {
                    document.getElementById('nextRoundBtn').disabled = false;
                }
            }

            nextRound() {
                // Reset visual effects
                document.getElementById('playerCard').classList.remove('winning-glow', 'losing-glow');
                document.getElementById('cpuCard').classList.remove('winning-glow', 'losing-glow');
                
                this.selectedAttribute = null;
                document.getElementById('nextRoundBtn').disabled = true;
                
                this.drawCards();
                
                if (this.isPlayerTurn) {
                    this.updateGameStatus("Sua vez! Escolha um atributo para comparar.");
                } else {
                    this.updateGameStatus("Vez do computador! Aguarde...");
                    setTimeout(() => {
                        const randomAttr = Object.keys(attributes)[Math.floor(Math.random() * Object.keys(attributes).length)];
                        this.selectAttribute(randomAttr);
                    }, 2000);
                }
            }

            endGame(playerWon) {
                this.gameOver = true;
                const modal = document.getElementById('gameOverModal');
                const content = document.getElementById('gameOverContent');
                
                if (playerWon) {
                    content.innerHTML = `
                        <div class="text-6xl mb-4">🏆</div>
                        <h2 class="text-2xl font-bold text-green-600 dark:text-green-400 mb-2">Parabéns!</h2>
                        <p class="text-gray-600 dark:text-gray-300">Você venceu todas as cartas!</p>
                    `;
                } else {
                    content.innerHTML = `
                        <div class="text-6xl mb-4">😢</div>
                        <h2 class="text-2xl font-bold text-red-600 dark:text-red-400 mb-2">Fim de Jogo</h2>
                        <p class="text-gray-600 dark:text-gray-300">O computador venceu todas as cartas!</p>
                    `;
                }
                
                modal.classList.remove('hidden');
                document.getElementById('newGameBtn').classList.remove('hidden');
            }

            updateCardCounts() {
                document.getElementById('playerCards').textContent = this.playerDeck.length;
                document.getElementById('cpuCards').textContent = this.cpuDeck.length;
            }

            updateGameStatus(message) {
                document.getElementById('gameStatus').innerHTML = `
                    <h2 class="text-xl font-bold mb-2">${message}</h2>
                `;
            }

            newGame() {
                this.playerDeck = [];
                this.cpuDeck = [];
                this.currentPlayerCard = null;
                this.currentCpuCard = null;
                this.isPlayerTurn = true;
                this.gameOver = false;
                this.selectedAttribute = null;
                
                document.getElementById('gameOverModal').classList.add('hidden');
                document.getElementById('newGameBtn').classList.add('hidden');
                document.getElementById('nextRoundBtn').disabled = true;
                
                // Reset visual effects
                document.getElementById('playerCard').classList.remove('winning-glow', 'losing-glow');
                document.getElementById('cpuCard').classList.remove('winning-glow', 'losing-glow');
                
                this.initializeGame();
            }

            setupEventListeners() {
                document.getElementById('nextRoundBtn').addEventListener('click', () => this.nextRound());
                document.getElementById('newGameBtn').addEventListener('click', () => this.newGame());
                document.getElementById('newGameModalBtn').addEventListener('click', () => this.newGame());
            }
        }

        // Initialize game when page loads
        let game;
        document.addEventListener('DOMContentLoaded', () => {
            game = new SuperTrunfoGame();
        });
    </script>
</body>
</html>
