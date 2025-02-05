<!DOCTYPE html>
<html>
<head>
    <title>Tic Tac Toe</title>
    <style>
        /* Previous styles remain the same... */
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #43cea2, #185a9d);
        }

        .container {
            text-align: center;
            background: rgba(255, 255, 255, 0.1);
            padding: 2rem;
            border-radius: 20px;
            backdrop-filter: blur(10px);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
        }

        .menu-container {
            text-align: center;
            color: white;
        }

        .menu-container h1 {
            font-size: 2.5rem;
            margin-bottom: 2rem;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
        }

        .menu-button {
            display: block;
            width: 250px;
            margin: 1rem auto;
            padding: 15px 30px;
            font-size: 1.2rem;
            background: linear-gradient(45deg, #FF6B6B, #4ECDC4);
            color: white;
            border: none;
            border-radius: 25px;
            cursor: pointer;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 1px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
        }

        .menu-button:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
        }

        .game-container {
            display: none;
            text-align: center;
        }

        .status {
            margin: 20px 0;
            font-size: 40px;  /* Increased font size */
            font-weight: bold;
            color: white;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
        }

        .board {
            display: grid;
            grid-template-columns: repeat(3, 100px);
            gap: 8px;
            background: rgba(255, 255, 255, 0.2);
            padding: 10px;
            border-radius: 15px;
            margin: 20px 0;  /* Added margin */
        }

        .cell {
            width: 100px;
            height: 100px;
            background: rgba(255, 255, 255, 0.9);
            border: none;
            font-size: 48px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.3s ease;
            border-radius: 10px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
        }

        .cell:hover {
            transform: scale(1.05);
            box-shadow: 0 0 15px rgba(255, 255, 255, 0.5);
        }

        .cell.x {
            color: #FF6B6B;
        }

        .cell.o {
            color: #4ECDC4;
        }

        .button {
            margin: 10px;
            padding: 12px 30px;
            font-size: 18px;
            background: linear-gradient(45deg, #FF6B6B, #4ECDC4);
            color: white;
            border: none;
            border-radius: 25px;
            cursor: pointer;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 1px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
        }

        .button:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
        }

        @keyframes win-animation {
            0% { transform: scale(1); }
            50% { transform: scale(1.1); }
            100% { transform: scale(1); }
        }

        .winner {
            animation: win-animation 0.5s ease-in-out;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="menu-container" id="menu">
            <h1>Tic Tac Toe</h1>
            <button class="menu-button" onclick="startGame('2player')">Play with Friend</button>
            <button class="menu-button" onclick="startGame('computer')">Play with Computer</button>
        </div>

        <div class="game-container" id="game">
            <div class="status"></div>
            <div class="board">
                <button class="cell" data-index="0"></button>
                <button class="cell" data-index="1"></button>
                <button class="cell" data-index="2"></button>
                <button class="cell" data-index="3"></button>
                <button class="cell" data-index="4"></button>
                <button class="cell" data-index="5"></button>
                <button class="cell" data-index="6"></button>
                <button class="cell" data-index="7"></button>
                <button class="cell" data-index="8"></button>
            </div>
            <button class="button" onclick="returnToMenu()">Menu</button>
            <button class="button" onclick="game.resetGame()">Reset</button>
        </div>
    </div>

    <script>
        class TicTacToe {
            constructor(gameMode) {
                this.gameMode = gameMode;
                this.board = Array(9).fill('');
                this.currentPlayer = 'X';
                this.gameActive = true;
                this.computerPlayer = 'O';
                
                this.cells = document.querySelectorAll('.cell');
                this.status = document.querySelector('.status');
                
                this.initializeGame();
                this.updateStatus();
            }

            updateStatus() {
                if (this.gameActive) {
                    this.status.textContent = this.currentPlayer;
                }
            }

            initializeGame() {
                this.cells.forEach(cell => {
                    cell.addEventListener('click', () => this.handleCellClick(cell));
                });
                this.updateStatus();
            }

            handleCellClick(cell) {
                const index = cell.getAttribute('data-index');
                
                if (this.board[index] || !this.gameActive) return;
                
                this.makeMove(index);

                if (this.gameMode === 'computer' && this.gameActive && this.currentPlayer === this.computerPlayer) {
                    setTimeout(() => this.makeComputerMove(), 500);
                }
            }

            makeMove(index) {
                this.board[index] = this.currentPlayer;
                this.cells[index].textContent = this.currentPlayer;
                this.cells[index].classList.add(this.currentPlayer.toLowerCase());
                
                if (this.checkWin()) {
                    this.status.textContent = `${this.currentPlayer} WON 🏆`;
                    this.gameActive = false;
                    this.highlightWinningCells();
                    return;
                }
                
                if (this.checkDraw()) {
                    this.status.textContent = "DRAW 🤝";
                    this.gameActive = false;
                    return;
                }
                
                this.currentPlayer = this.currentPlayer === 'X' ? 'O' : 'X';
                this.updateStatus();
            }

            // Rest of the methods remain the same...
            makeComputerMove() {
                if (!this.gameActive) return;

                // Try to win
                const winningMove = this.findBestMove('O');
                if (winningMove !== -1) {
                    this.makeMove(winningMove);
                    return;
                }

                // Block player's winning move
                const blockingMove = this.findBestMove('X');
                if (blockingMove !== -1) {
                    this.makeMove(blockingMove);
                    return;
                }

                // Take center if available
                if (this.board[4] === '') {
                    this.makeMove(4);
                    return;
                }

                // Take any available corner
                const corners = [0, 2, 6, 8];
                const availableCorners = corners.filter(i => this.board[i] === '');
                if (availableCorners.length > 0) {
                    const randomCorner = availableCorners[Math.floor(Math.random() * availableCorners.length)];
                    this.makeMove(randomCorner);
                    return;
                }

                // Take any available side
                const sides = [1, 3, 5, 7];
                const availableSides = sides.filter(i => this.board[i] === '');
                if (availableSides.length > 0) {
                    const randomSide = availableSides[Math.floor(Math.random() * availableSides.length)];
                    this.makeMove(randomSide);
                }
            }

            findBestMove(player) {
                const winPatterns = [
                    [0, 1, 2], [3, 4, 5], [6, 7, 8],
                    [0, 3, 6], [1, 4, 7], [2, 5, 8],
                    [0, 4, 8], [2, 4, 6]
                ];

                for (let pattern of winPatterns) {
                    const [a, b, c] = pattern;
                    const line = [this.board[a], this.board[b], this.board[c]];
                    const playerCount = line.filter(cell => cell === player).length;
                    const emptyCount = line.filter(cell => cell === '').length;

                    if (playerCount === 2 && emptyCount === 1) {
                        return pattern[line.indexOf('')];
                    }
                }

                return -1;
            }

            checkWin() {
                const winPatterns = [
                    [0, 1, 2], [3, 4, 5], [6, 7, 8],
                    [0, 3, 6], [1, 4, 7], [2, 5, 8],
                    [0, 4, 8], [2, 4, 6]
                ];

                return winPatterns.some(pattern => {
                    const [a, b, c] = pattern;
                    if (this.board[a] && 
                        this.board[a] === this.board[b] && 
                        this.board[a] === this.board[c]) {
                        this.winningPattern = pattern;
                        return true;
                    }
                    return false;
                });
            }

            highlightWinningCells() {
                if (this.winningPattern) {
                    this.winningPattern.forEach(index => {
                        this.cells[index].classList.add('winner');
                    });
                }
            }

            checkDraw() {
                return this.board.every(cell => cell !== '');
            }

            resetGame() {
                this.board = Array(9).fill('');
                this.currentPlayer = 'X';
                this.gameActive = true;
                this.winningPattern = null;
                
                this.cells.forEach(cell => {
                    cell.textContent = '';
                    cell.classList.remove('x', 'o', 'winner');
                });
                
                this.updateStatus();

                if (this.gameMode === 'computer' && this.currentPlayer === this.computerPlayer) {
                    setTimeout(() => this.makeComputerMove(), 500);
                }
            }
        }

        let game;

        function startGame(mode) {
            document.getElementById('menu').style.display = 'none';
            document.getElementById('game').style.display = 'block';
            game = new TicTacToe(mode);
        }

        function returnToMenu() {
            document.getElementById('game').style.display = 'none';
            document.getElementById('menu').style.display = 'block';
        }
    </script>
</body>
</html>
