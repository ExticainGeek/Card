<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Card Game - Race to 100</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
        }

        h1 {
            text-align: center;
            color: white;
            margin-bottom: 10px;
            font-size: 2.5em;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }

        .subtitle {
            text-align: center;
            color: #f0f0f0;
            margin-bottom: 30px;
            font-size: 1.1em;
        }

        .players-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }

        .player-card {
            background: white;
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 8px 16px rgba(0,0,0,0.2);
            transition: transform 0.2s, box-shadow 0.2s;
        }

        .player-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 12px 24px rgba(0,0,0,0.3);
        }

        .player-card.loser {
            background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%);
            color: white;
            opacity: 0.7;
        }

        .player-card.winner {
            background: linear-gradient(135deg, #51cf66 0%, #37b24d 100%);
            color: white;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }

        .player-name {
            font-size: 1.5em;
            font-weight: bold;
            margin-bottom: 10px;
            color: #667eea;
        }

        .player-card.loser .player-name {
            color: white;
        }

        .player-card.winner .player-name {
            color: white;
        }

        .player-score {
            font-size: 3em;
            font-weight: bold;
            text-align: center;
            margin: 15px 0;
            color: #333;
        }

        .player-card.loser .player-score {
            color: white;
        }

        .player-card.winner .player-score {
            color: white;
        }

        .score-controls {
            display: flex;
            gap: 10px;
            margin-bottom: 10px;
        }

        .score-input {
            flex: 1;
            padding: 10px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 1.1em;
            text-align: center;
        }

        .add-btn {
            padding: 10px 20px;
            background: #667eea;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 1em;
            font-weight: bold;
            cursor: pointer;
            transition: background 0.3s;
        }

        .add-btn:hover {
            background: #5568d3;
        }

        .add-btn:active {
            transform: scale(0.95);
        }

        .reset-btn {
            display: block;
            margin: 0 auto;
            padding: 15px 40px;
            background: white;
            color: #667eea;
            border: none;
            border-radius: 10px;
            font-size: 1.2em;
            font-weight: bold;
            cursor: pointer;
            box-shadow: 0 4px 8px rgba(0,0,0,0.2);
            transition: all 0.3s;
        }

        .reset-btn:hover {
            background: #f0f0f0;
            transform: translateY(-2px);
            box-shadow: 0 6px 12px rgba(0,0,0,0.3);
        }

        .game-over {
            background: white;
            border-radius: 15px;
            padding: 30px;
            text-align: center;
            margin-bottom: 20px;
            box-shadow: 0 8px 16px rgba(0,0,0,0.2);
        }

        .game-over h2 {
            color: #ff6b6b;
            font-size: 2.5em;
            margin-bottom: 15px;
        }

        .game-over p {
            font-size: 1.5em;
            color: #333;
            margin-bottom: 20px;
        }

        .loser-badge {
            background: #ff6b6b;
            color: white;
            padding: 5px 15px;
            border-radius: 20px;
            font-size: 0.9em;
            display: inline-block;
            margin-top: 5px;
        }

        .winner-badge {
            background: #37b24d;
            color: white;
            padding: 5px 15px;
            border-radius: 20px;
            font-size: 0.9em;
            display: inline-block;
            margin-top: 5px;
            font-weight: bold;
        }

        @media (max-width: 600px) {
            h1 {
                font-size: 2em;
            }
            
            .players-grid {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🎴 Card Game 100</h1>
        <p class="subtitle">First to reach 100 points loses!</p>

        <div id="gameOver" class="game-over" style="display: none;">
            <h2>🎮 Game Over!</h2>
            <p id="loserMessage"></p>
        </div>

        <div class="players-grid" id="playersGrid"></div>

        <button class="reset-btn" onclick="resetGame()">🔄 Reset Game</button>
    </div>

    <script>
        const players = [
            { name: 'Atharva', score: 0 },
            { name: 'Akshat', score: 0 },
            { name: 'Rohan', score: 0 },
            { name: 'Vallabh', score: 0 },
            { name: 'Rishika', score: 0 },
            { name: 'Unnati', score: 0 },
            { name: 'Surita', score: 0 }
        ];

        let gameOver = false;

        function renderPlayers() {
            const grid = document.getElementById('playersGrid');
            grid.innerHTML = '';

            const playersUnder100 = players.filter(p => p.score < 100);
            const winner = gameOver && playersUnder100.length === 1 ? playersUnder100[0] : null;

            players.forEach((player, index) => {
                const isLoser = player.score >= 100;
                const isWinner = winner && player.name === winner.name;
                
                const card = document.createElement('div');
                card.className = `player-card ${isLoser ? 'loser' : ''} ${isWinner ? 'winner' : ''}`;
                
                let badge = '';
                if (isWinner) {
                    badge = '<div class="winner-badge">🏆 WINNER!</div>';
                } else if (isLoser) {
                    badge = '<div class="loser-badge">OUT</div>';
                }
                
                card.innerHTML = `
                    <div class="player-name">
                        ${player.name}
                        ${badge}
                    </div>
                    <div class="player-score">${player.score}</div>
                    <div class="score-controls">
                        <input 
                            type="number" 
                            class="score-input" 
                            id="input-${index}" 
                            placeholder="Add points"
                            min="0"
                            ${gameOver || isLoser ? 'disabled' : ''}
                        >
                        <button 
                            class="add-btn" 
                            onclick="addScore(${index})"
                            ${gameOver || isLoser ? 'disabled' : ''}
                        >
                            Add
                        </button>
                    </div>
                `;
                
                grid.appendChild(card);
            });
        }

        function addScore(playerIndex) {
            if (gameOver || players[playerIndex].score >= 100) return;

            const input = document.getElementById(`input-${playerIndex}`);
            const points = parseInt(input.value);

            if (isNaN(points) || points < 0) {
                alert('Please enter a valid positive number!');
                return;
            }

            players[playerIndex].score += points;
            input.value = '';

            // Check how many players are still under 100
            const playersStillIn = players.filter(p => p.score < 100);

            // Game ends when only 1 player remains under 100
            if (playersStillIn.length === 1) {
                gameOver = true;
                showGameOver(playersStillIn[0].name);
            }

            renderPlayers();
        }

        function showGameOver(winnerName) {
            const gameOverDiv = document.getElementById('gameOver');
            const message = document.getElementById('loserMessage');
            
            message.textContent = `${winnerName} is the last one standing and WINS the game! 🎉`;
            gameOverDiv.style.display = 'block';
            
            // Scroll to top to show game over message
            window.scrollTo({ top: 0, behavior: 'smooth' });
        }

        function resetGame() {
            gameOver = false;
            players.forEach(player => player.score = 0);
            document.getElementById('gameOver').style.display = 'none';
            renderPlayers();
        }

        // Allow Enter key to add score
        document.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                const activeElement = document.activeElement;
                if (activeElement.classList.contains('score-input')) {
                    const index = parseInt(activeElement.id.split('-')[1]);
                    addScore(index);
                }
            }
        });

        // Initial render
        renderPlayers();
    </script>
</body>
</html>
