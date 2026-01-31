<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <title>Card Game - Race to 100</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 15px;
            overflow-x: hidden;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
        }

        h1 {
            text-align: center;
            color: white;
            margin-bottom: 8px;
            font-size: clamp(1.8em, 5vw, 2.5em);
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }

        .subtitle {
            text-align: center;
            color: #f0f0f0;
            margin-bottom: 20px;
            font-size: clamp(0.9em, 3vw, 1.1em);
        }

        .players-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(min(100%, 280px), 1fr));
            gap: 15px;
            margin-bottom: 25px;
        }

        .player-card {
            background: white;
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 8px 16px rgba(0,0,0,0.2);
            transition: transform 0.2s, box-shadow 0.2s;
            touch-action: manipulation;
        }

        .player-card:active {
            transform: scale(0.98);
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
            font-size: clamp(1.3em, 4vw, 1.5em);
            font-weight: bold;
            margin-bottom: 10px;
            color: #667eea;
        }

        .player-card.loser .player-name,
        .player-card.winner .player-name {
            color: white;
        }

        .player-score {
            font-size: clamp(2.5em, 8vw, 3em);
            font-weight: bold;
            text-align: center;
            margin: 15px 0;
            color: #333;
        }

        .player-card.loser .player-score,
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
            padding: 12px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 16px;
            text-align: center;
            min-width: 0;
        }

        .score-input:disabled {
            background: #f5f5f5;
            color: #999;
        }

        .add-btn {
            padding: 12px 20px;
            background: #667eea;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 1em;
            font-weight: bold;
            cursor: pointer;
            transition: background 0.3s;
            touch-action: manipulation;
            min-width: 70px;
        }

        .add-btn:active {
            background: #5568d3;
            transform: scale(0.95);
        }

        .add-btn:disabled {
            background: #ccc;
            cursor: not-allowed;
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
            touch-action: manipulation;
        }

        .reset-btn:active {
            background: #f0f0f0;
            transform: scale(0.95);
        }

        .game-over {
            background: white;
            border-radius: 15px;
            padding: 25px;
            text-align: center;
            margin-bottom: 20px;
            box-shadow: 0 8px 16px rgba(0,0,0,0.2);
        }

        .game-over h2 {
            color: #ff6b6b;
            font-size: clamp(1.8em, 5vw, 2.5em);
            margin-bottom: 15px;
        }

        .game-over p {
            font-size: clamp(1.1em, 4vw, 1.5em);
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

        /* iOS Safari specific fixes */
        @supports (-webkit-touch-callout: none) {
            .score-input {
                font-size: 16px;
            }
        }

        @media (max-width: 600px) {
            body {
                padding: 10px;
            }

            .players-grid {
                gap: 12px;
            }

            .player-card {
                padding: 15px;
            }

            .reset-btn {
                padding: 12px 30px;
                font-size: 1.1em;
            }
        }

        /* Landscape mode for mobile */
        @media (max-height: 500px) and (orientation: landscape) {
            .players-grid {
                grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
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
                            inputmode="numeric"
                            pattern="[0-9]*"
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

            // Blur input to hide keyboard on mobile
            input.blur();

            const playersStillIn = players.filter(p => p.score < 100);

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

        // Prevent zoom on double tap for iOS
        let lastTouchEnd = 0;
        document.addEventListener('touchend', (e) => {
            const now = Date.now();
            if (now - lastTouchEnd <= 300) {
                e.preventDefault();
            }
            lastTouchEnd = now;
        }, false);

        // Initial render
        renderPlayers();
    </script>
</body>
</html>            color: white;
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
