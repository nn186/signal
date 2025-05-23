<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Minesweeper Adventure</title>
    <style>
        :root {
            --primary-color: #4a6bff;
            --danger-color: #ff4a4a;
            --success-color: #ffd700;
            --dark-bg: #0a0a2a;
            --darker-bg: #1a1a4a;
            --cell-size: min(12vw, 70px);
            --gap-size: min(2vw, 10px);
            --font-size: calc(var(--cell-size) * 0.4);
            --button-padding: min(4vw, 15px) min(8vw, 30px);
        }
        
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            padding: 20px;
            background: linear-gradient(135deg, var(--dark-bg), var(--darker-bg));
            color: white;
            overflow-x: hidden;
        }
        
        .game-container {
            width: 100%;
            max-width: 500px;
            margin: 20px 0;
        }
        
        .controls {
            margin-bottom: 25px;
            display: flex;
            flex-direction: column;
            align-items: center;
            text-align: center;
            width: 100%;
        }
        
        .button {
            padding: var(--button-padding);
            margin: 10px;
            font-size: min(5vw, 18px);
            cursor: pointer;
            color: white;
            border: none;
            border-radius: 50px;
            transition: all 0.3s ease;
            width: 100%;
            max-width: 250px;
            font-weight: 600;
            position: relative;
            overflow: hidden;
        }
        
        .button::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(145deg, 
                rgba(255,255,255,0.2), 
                rgba(255,255,255,0.05));
            border-radius: 50px;
            z-index: 1;
        }
        
        .button-primary {
            background: linear-gradient(145deg, var(--primary-color), #3a5bef);
            box-shadow: 0 5px 15px rgba(0, 0, 255, 0.3);
        }
        
        .button-success {
            background: linear-gradient(145deg, var(--success-color), #ffaa00);
            color: #1a1a4a;
            box-shadow: 0 5px 15px rgba(255, 215, 0, 0.4);
        }
        
        .button:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(0, 0, 255, 0.4);
        }
        
        .button:active {
            transform: translateY(1px);
        }
        
        .difficulty-selector {
            margin-top: 15px;
            display: flex;
            gap: var(--gap-size);
            flex-wrap: wrap;
            justify-content: center;
            width: 100%;
            max-width: 300px;
        }
        
        .difficulty-option {
            width: var(--cell-size);
            height: var(--cell-size);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            background: rgba(0, 0, 100, 0.3);
            border: 2px solid var(--primary-color);
            transition: all 0.3s ease;
            font-weight: bold;
            font-size: var(--font-size);
            position: relative;
        }
        
        .difficulty-option:hover {
            background: rgba(74, 107, 255, 0.3);
        }
        
        .difficulty-option.selected {
            background: linear-gradient(145deg, var(--danger-color), #ef5a3a);
            border-color: var(--danger-color);
            box-shadow: 0 0 15px rgba(255, 74, 74, 0.5);
        }
        
        .game-board {
            display: grid;
            grid-template-columns: repeat(5, var(--cell-size));
            grid-gap: var(--gap-size);
            width: 100%;
            justify-content: center;
            margin: 0 auto;
        }
        
        .cell {
            width: var(--cell-size);
            height: var(--cell-size);
            background: linear-gradient(145deg, #6b8cff, var(--primary-color));
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: var(--font-size);
            cursor: pointer;
            border-radius: 10px;
            box-shadow: 0 5px 15px rgba(0, 0, 100, 0.4);
            transition: all 0.2s ease;
            position: relative;
            overflow: hidden;
            aspect-ratio: 1/1;
        }
        
        .cell:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(0, 0, 150, 0.6);
        }
        
        .cell::after {
            content: '';
            position: absolute;
            width: 100%;
            height: 100%;
            background: radial-gradient(circle, 
                rgba(100,200,255,0.4) 0%, 
                rgba(0,100,255,0.1) 70%);
            border-radius: 10px;
            opacity: 0;
            transition: opacity 0.3s;
        }
        
        .cell:hover::after {
            opacity: 1;
        }
        
        .cell.revealed {
            background: linear-gradient(145deg, #3a4b9f, #2a3b8f);
            box-shadow: inset 0 5px 15px rgba(0, 0, 0, 0.4);
        }
        
        .cell.bomb {
            background: linear-gradient(145deg, var(--danger-color), #ff2a2a);
            box-shadow: 0 0 20px #ff0000;
            animation: bombPulse 1.5s infinite;
        }
        
        @keyframes bombPulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }
        
        .bomb-icon {
            font-size: var(--font-size);
            position: relative;
            animation: bombFloat 2s infinite ease-in-out;
        }
        
        @keyframes bombFloat {
            0%, 100% { transform: translateY(0) rotate(0deg); }
            50% { transform: translateY(-10px) rotate(5deg); }
        }

        @media (max-width: 400px) {
            :root {
                --cell-size: min(16vw, 60px);
                --gap-size: min(2vw, 8px);
            }
        }

        @media (max-width: 300px) {
            :root {
                --cell-size: min(18vw, 50px);
            }
            
            .game-board {
                grid-template-columns: repeat(5, 1fr);
            }
        }
    </style>
</head>
<body>
    <div class="controls">
        <button class="button button-primary" id="startButton">GO TO SIGNAL</button>
        <div class="difficulty-selector">
            <div class="difficulty-option selected" data-bombs="1">1</div>
            <div class="difficulty-option" data-bombs="3">3</div>
            <div class="difficulty-option" data-bombs="5">5</div>
            <div class="difficulty-option" data-bombs="7">7</div>
        </div>
    </div>
    
    <div class="game-container">
        <div class="game-board" id="gameBoard"></div>
    </div>
    
    <button class="button button-success" id="winButton">
        GO 1WIN 🚀
    </button>
    
    <script>
        document.addEventListener('DOMContentLoaded', () => {
            // DOM Elements
            const gameBoard = document.getElementById('gameBoard');
            const startButton = document.getElementById('startButton');
            const winButton = document.getElementById('winButton');
            const difficultyOptions = document.querySelectorAll('.difficulty-option');
            
            // Game State
            const gameState = {
                board: [],
                bombPositions: [],
                gameStarted: false,
                selectedBombs: 1,
                revealedCells: 0,
                boardSize: 5
            };
            
            // Initialize difficulty selection
            difficultyOptions.forEach(option => {
                option.addEventListener('click', () => {
                    difficultyOptions.forEach(opt => opt.classList.remove('selected'));
                    option.classList.add('selected');
                    gameState.selectedBombs = parseInt(option.dataset.bombs);
                });
            });
            
            // Initialize game board
            function initializeBoard() {
                gameBoard.innerHTML = '';
                gameState.board = [];
                gameState.bombPositions = [];
                gameState.gameStarted = false;
                gameState.revealedCells = 0;
                
                // Create board matrix
                for (let i = 0; i < gameState.boardSize; i++) {
                    gameState.board[i] = [];
                    for (let j = 0; j < gameState.boardSize; j++) {
                        gameState.board[i][j] = {
                            isBomb: false,
                            revealed: false
                        };
                        
                        // Create cell element
                        const cell = createCell(i, j);
                        gameBoard.appendChild(cell);
                    }
                }
            }
            
            // Create a single cell
            function createCell(row, col) {
                const cell = document.createElement('div');
                cell.className = 'cell';
                cell.dataset.row = row;
                cell.dataset.col = col;
                
                cell.addEventListener('click', () => handleCellClick(cell, row, col));
                
                return cell;
            }
            
            // Handle cell click
            function handleCellClick(cell, row, col) {
                if (!gameState.gameStarted || gameState.board[row][col].revealed) return;
                
                gameState.board[row][col].revealed = true;
                gameState.revealedCells++;
                cell.classList.add('revealed');
                
                if (gameState.board[row][col].isBomb) {
                    cell.innerHTML = '<div class="bomb-icon">💣</div>';
                    cell.classList.add('bomb');
                }
            }
            
            // Place bombs randomly
            function placeBombs() {
                const bombCount = gameState.selectedBombs;
                gameState.bombPositions = [];
                
                while (gameState.bombPositions.length < bombCount) {
                    const row = Math.floor(Math.random() * gameState.boardSize);
                    const col = Math.floor(Math.random() * gameState.boardSize);
                    
                    if (!gameState.board[row][col].isBomb) {
                        gameState.board[row][col].isBomb = true;
                        gameState.bombPositions.push({row, col});
                    }
                }
            }
            
            // Start game
            startButton.addEventListener('click', () => {
                initializeBoard();
                placeBombs();
                gameState.gameStarted = true;
                
                // For demo purposes, show bombs (in a real game you wouldn't do this)
                gameState.bombPositions.forEach(pos => {
                    const cell = document.querySelector(`.cell[data-row="${pos.row}"][data-col="${pos.col}"]`);
                    cell.innerHTML = '<div class="bomb-icon">💣</div>';
                    cell.classList.add('bomb');
                });
            });
            
            // Win button action
            winButton.addEventListener('click', () => {
                window.location.href = 'https://1wmndv.life/v3/2158/1win-mines?p=xcof';
            });
            
            // Initialize the board on page load
            initializeBoard();
        });
    </script>
</body>
</html>
