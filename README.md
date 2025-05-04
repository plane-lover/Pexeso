<!DOCTYPE html>
<html lang="cs">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Futuristické Pexeso</title>
    <style>
        /* Reset defaultní styly */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        /* Globální styly */
        body {
            background: #121212;
            color: #fff;
            font-family: 'Arial', sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow: hidden;
            animation: backgroundAnimation 20s ease infinite;
        }

        /* Animace dynamického pozadí */
        @keyframes backgroundAnimation {
            0% { background: #8e2de2; }
            25% { background: #4a00e0; }
            50% { background: #00ffff; }
            75% { background: #ff007f; }
            100% { background: #8e2de2; }
        }

        .game-container {
            text-align: center;
            padding: 20px;
            border: 3px solid #fff;
            border-radius: 15px;
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.5);
            background: rgba(26, 26, 26, 0.8);
            max-width: 800px;
            width: 100%;
            z-index: 10;
        }

        .game-title {
            font-size: 2.5rem;
            margin-bottom: 20px;
            text-transform: uppercase;
            letter-spacing: 2px;
            color: #00ffff;
            text-shadow: 0 0 10px #00ffff, 0 0 20px #00ffff;
        }

        .game-board {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
            margin-bottom: 20px;
            position: relative;
        }

        .card {
            background-color: #000; /* Černá přední strana */
            border: 1px solid #00ffff;
            border-radius: 10px;
            width: 100px;
            height: 100px;
            position: relative;
            transform-style: preserve-3d;
            transition: transform 0.5s;
            cursor: pointer;
            opacity: 1;
        }

        .card.flipped {
            transform: rotateY(180deg);
        }

        .card .front, .card .back {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            border-radius: 10px;
        }

        .card .front {
            background-color: #000;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .card .back {
            background-color: #00ffff;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #121212;
            font-size: 2rem;
            font-weight: bold;
            transform: rotateY(180deg); /* Zadní strana bude otočená */
        }

        .reset-btn {
            background-color: #00ffff;
            border: none;
            color: #121212;
            padding: 15px 30px;
            font-size: 1.2rem;
            cursor: pointer;
            border-radius: 10px;
            transition: transform 0.3s, background-color 0.3s;
        }

        /* Animace pro tlačítko "Nová hra" */
        @keyframes buttonClickEffect {
            0% {
                transform: scale(1);
                background-color: #00ffff;
            }
            50% {
                transform: scale(1.2);
                background-color: #ff0000;
            }
            100% {
                transform: scale(1);
                background-color: #00ffff;
            }
        }

        .reset-btn.clicked {
            animation: buttonClickEffect 0.5s ease-out forwards;
        }

        /* Animace pro zmizení karet při nové hře */
        .card.remove {
            animation: removeCard 0.5s forwards;
        }

        @keyframes removeCard {
            0% {
                opacity: 1;
                transform: scale(1);
            }
            100% {
                opacity: 0;
                transform: scale(0);
            }
        }

        /* Animace pro znovuobjevení karet */
        .card.reappear {
            animation: reappearCard 1s forwards;
        }

        @keyframes reappearCard {
            0% {
                opacity: 0;
                transform: scale(0);
            }
            50% {
                opacity: 0.5;
                transform: scale(1.2);
            }
            100% {
                opacity: 1;
                transform: scale(1);
            }
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h1 class="game-title">Futuristické Pexeso</h1>
        <div id="game-board" class="game-board">
            <!-- Karty budou generovány pomocí JS -->
        </div>
        <button id="reset-btn" class="reset-btn">Nová Hra</button>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const cards = [
                'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H',
                'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H'
            ];

            let shuffledCards = shuffle(cards);
            const gameBoard = document.getElementById('game-board');
            const resetBtn = document.getElementById('reset-btn');

            let flippedCards = [];
            let matchedPairs = 0;

            // Vytvoření karet na základě zamíchaného pole
            function createBoard() {
                gameBoard.innerHTML = '';
                shuffledCards.forEach((cardValue, index) => {
                    const card = document.createElement('div');
                    card.classList.add('card');
                    card.setAttribute('data-id', index);

                    const front = document.createElement('div');
                    front.classList.add('front');

                    const back = document.createElement('div');
                    back.classList.add('back');
                    back.textContent = cardValue;

                    card.appendChild(front);
                    card.appendChild(back);
                    card.addEventListener('click', flipCard);

                    gameBoard.appendChild(card);
                });
            }

            createBoard();

            // Funkce pro zamíchání karet
            function shuffle(array) {
                return array.sort(() => Math.random() - 0.5);
            }

            // Funkce pro otočení karty
            function flipCard() {
                if (flippedCards.length < 2 && !this.classList.contains('flipped')) {
                    this.classList.add('flipped');
                    flippedCards.push(this);

                    if (flippedCards.length === 2) {
                        checkMatch();
                    }
                }
            }

            // Funkce pro kontrolu shody
            function checkMatch() {
                const [firstCard, secondCard] = flippedCards;

                if (firstCard.querySelector('.back').textContent === secondCard.querySelector('.back').textContent) {
                    matchedPairs++;

                    // Otočení karty na písmeno a následně animace zmizení
                    setTimeout(() => {
                        firstCard.classList.add('remove');
                        secondCard.classList.add('remove');
                    }, 1000);

                    flippedCards = [];

                    if (matchedPairs === cards.length / 2) {
                        setTimeout(() => alert('Gratulujeme, vyhráli jste!'), 500);
                    }
                } else {
                    setTimeout(() => {
                        firstCard.classList.remove('flipped');
                        secondCard.classList.remove('flipped');
                        flippedCards = [];
                    }, 1000);
                }
            }

            // Tlačítko Nová hra - animace
            resetBtn.addEventListener('click', () => {
                // Nejprve odstraníme třídu animace, pokud už existuje
                resetBtn.classList.remove('clicked');
                
                // Pak znovu přidáme třídu animace, aby se animace spustila
                void resetBtn.offsetWidth;  // Tento trik způsobí, že prohlížeč znovu "zresetuje" animaci
                resetBtn.classList.add('clicked');

                matchedPairs = 0;
                flippedCards = [];
                shuffledCards = shuffle(cards);

                // Animace zmizení karet
                const cardsToRemove = document.querySelectorAll('.card');
                cardsToRemove.forEach(card => card.classList.add('remove'));

                setTimeout(() => {
                    // Po animaci zmizení, nové karty a animace znovuobjevení
                    createBoard();
                    const newCards = document.querySelectorAll('.card');
                    newCards.forEach(card => card.classList.add('reappear'));

                    setTimeout(() => {
                        newCards.forEach(card => card.classList.remove('reappear'));
                    }, 1000);
                }, 500);
            });
        });
    </script>
</body>
</html>
