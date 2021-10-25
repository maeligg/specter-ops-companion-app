<script>
    import '../app.css';
    import InfoPanel from '../lib/InfoPanel.svelte';

    let agent = '';
    let numPlayers;
    let numPlayersConfirmed = false;
    let objectivesPlaced = false;
    let board = [
        ' ', ' ', '.', ' ', '.', ' ', '.', ' ', '.', ' ', '.', ' ', ' ', 'a', ' ', ' ', ' ', ' ', '.', '.', '.', '.', '.',
        '.', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', '.', ' ', ' ', ' ', '.', '.', ' ', '.', '.', '.', ' ',
        ' ', ' ', '.', '.', ' ', '.', ' ', '.', '.', ' ', ' ', ' ', '.', ' ', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', ' ',
        '.', ' ', '.', ' ', ' ', ' ', ' ', ' ', '.', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', '.', ' ', '.', '.',
        ' ', ' ', ' ', ' ', '.', ' ', '.', ' ', ' ', ' ', ' ', ' ', '.', ' ', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', ' ',
        ' ', '.', '.', ' ', '.', ' ', '.', '.', ' ', '.', ' ', '.', '.', ' ', ' ', ' ', ' ', '.', '.', ' ', '.', '.', ' ',
        ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', '.', ' ', ' ', ' ', ' ', ' ',
        ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '.', '.', ' ', '.',
        '.', ' ', '.', '.', ' ', '.', ' ', '.', '.', ' ', '.', ' ', '.', ' ', ' ', '.', ' ', '.', ' ', ' ', ' ', ' ', ' ',
        ' ', ' ', ' ', '.', ' ', ' ', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', '.', ' ',
        ' ', '.', ' ', ' ', ' ', '.', ' ', ' ', ' ', '.', '.', ' ', '.', ' ', ' ', '.', '.', ' ', '.', '.', ' ', '.', ' ',
        ' ', '.', ' ', '.', ' ', ' ', ' ', '.', ' ', '.', '.', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ',
        ' ', ' ', ' ', ' ', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ',
        '.', ' ', '.', ' ', '.', '.', '.', ' ', '.', '.', ' ', '.', '.', ' ', ' ', '.', '.', ' ', '.', ' ', '.', ' ', '.',
        ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', ' ', ' ', ' ',
        ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '.', '.', ' ', '.', '.', ' ', '.', ' ',
        '.', ' ', '.', ' ', '.', ' ', '.', ' ', '.', ' ', ' ', '.', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ',
        ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', ' ', '.', ' ', ' ', ' ', '.', ' ', '.', ' ', '.', ' ', '.', ' ',
        ' ', '.', ' ', '.', ' ', '.', '.', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ',
        ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', '.', ' ', ' ', '.', ' ', '.', '.', ' ', '.', ' ', '.', ' ', '.', ' ', '.',
        '.', ' ', '.', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ',
        '.', ' ', '.', ' ', '.', '.', ' ', '.', '.', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ',
        ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', '.', ' ', '.', ' ', '.', '.', ' ', '.', '.', ' ',
        ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', ' ', ' ', ' ',
        ' ', '.', '.', ' ', '.', '.', ' ', '.', '.', ' ', ' ', ' ', ' ', '.', '.', ' ', '.', ' ', ' ', ' ', '.', ' ', '.',
        ' ', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', ' ', '.', ' ', ' ', ' ', '.', ' ', ' ', ' ', ' ',
        '.', ' ', ' ', ' ', '.', ' ', '.', ' ', '.', ' ', ' ', '.', '.', ' ', ' ', ' ', '.', ' ', '.', '.', ' ', '.', ' ',
        '.', ' ', '.', ' ', ' ', ' ', ' ', ' ', '.', ' ', ' ', ' ', ' ', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ',
        ' ', ' ', ' ', ' ', '.', ' ', '.', ' ', ' ', ' ', ' ', '.', ' ', '.', '.', ' ', '.', ' ', '.', '.', ' ', ' ', ' ',
        ' ', '.', ' ', '.', '.', ' ', ' ', ' ', '.', ' ', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ',
        ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', ' ', ' ', ' ', ' ', ' ', '.', ' ', '.', ' ', '.', ' ', '.', ' ', ' ', ' ',
        ' ', '.', '.', ' ', '.', ' ', '.', ' ', '.', ' ', ' ', '.', ' ', ' ', ' ', '.', ' ', ' ', ' ', '.', ' ', '.', ' ',
    ];
    const roadSegments = [
        [13, 14, 36, 37, 59, 60, 82, 83, 105, 106, 128, 129, 151, 152, 174, 175, 197, 198, 220, 221, 243, 244, 266, 267, 289, 290, 312, 313, 335, 336, 358, 359],
        [138, 139, 140, 141, 142, 143, 144, 145, 146, 147, 148, 149, 150, 151, 152, 161, 162, 163, 164, 165, 166, 167, 168, 169, 170, 171, 172, 173, 174, 175],
        [266, 267, 268, 269, 270, 271, 272, 273, 274, 275, 289, 290, 291, 292, 293, 294, 295, 296, 297, 298],
        [322, 323, 324, 325, 326, 327, 328, 329, 330, 331, 332, 333, 334, 335, 336, 345, 346, 347, 348, 349, 350, 351, 352, 353, 354, 355, 356, 357, 358, 359],
        [331, 332, 354, 355, 377, 378, 400, 401, 423, 424, 446, 447, 469, 470, 492, 493, 515, 516, 538, 539, 561, 562, 584, 585, 607, 608, 630, 631, 653, 654, 676, 677, 699, 700, 722, 723],
        [469, 470, 471, 472, 473, 474, 475, 476, 477, 478, 479, 480, 481, 482, 492, 493, 494, 495, 496, 497, 498, 499, 500, 501, 502, 503, 504, 505],
        [506, 507, 508, 509, 510, 511, 512, 513, 514, 515, 516, 529, 530, 531, 532, 533, 534, 535, 536, 537, 538, 539]
    ];
    let exits = [13, 46, 68];
    const sectorObjectives = [
        [415, 370, 417, 301, 235, 260],
        [668, 623, 602, 604, 650, 675],
        [703, 568, 660, 707, 572, 688],
        [406, 408, 410, 412, 366, 319]
    ];
    let validMoves = [];
    let moveHistory = [13];
    let hunterLineOfSight = [];
    let placingHunters = false;

    const setupGame = () => {
        // Place objectives
        sectorObjectives.forEach(sector => {
            const selectedObjective = sector[Math.floor(Math.random() * sector.length)];
            board[selectedObjective] = 'oi';
            board = board;
        });

        // Place hunters starting position
        board[numPlayers === 4 ? 516 : 378] = 'h';
        board = board;

        // Place additional exit
        if (numPlayers === 4) {
            exits.push(7);
            exits = exits;
        }

        // Moves and LoS
        getValidMoves();
        getHunterLineOfSight();
    }

    const getCellFromIndex = (i) => {
        const adjustedIndex = (i % 23) + 10;
        return `${adjustedIndex.toString(36).toUpperCase()}${Math.ceil((i + 1) / 23)}`;
    };

    const updateAgentPosition = (i) => {
        if (validMoves.indexOf(i) === -1) return;

        board[board.indexOf('a')] = ' ';
        board = board;
        board[i] = 'a';
        board = board;

        moveHistory = [...moveHistory, i];

        getValidMoves();
    }

    const placeHunter = (i) => {
        if (board[i] !== '.' && board[i] !== 'a') {
            board[i] = 'h';
            board = board;

            getHunterLineOfSight();
        }
    }

    const togglePlacingHunters = () => {
        placingHunters = !placingHunters;

        if (placingHunters) {
            board = [...board.map(cell => cell === 'h' ? ' ' : cell)];
            hunterLineOfSight = [];
        }

        getValidMoves();
    }

    const calcValidSingleMove = (boardCells) => {
        const obstacles = ['.', 'h', 'oi', 'oc'];

        boardCells.forEach(cell => {
            // Left
            if (cell % 23 !== 0 && !obstacles.includes(board[cell - 1])) {
                validMoves.push(cell - 1)
            }

            // Right
            if ((cell + 1) % 23 !== 0 && !obstacles.includes(board[cell + 1])) {
                validMoves.push(cell + 1)
            }

            // Top
            if (!obstacles.includes(board[cell - 23])) {
                validMoves.push(cell - 23)
            }

            // Bottom
            if (!obstacles.includes(board[cell + 23])) {
                validMoves.push(cell + 23)
            }

            // Top left
            if (cell % 23 !== 0 && !obstacles.includes(board[cell - 24])) {
                validMoves.push(cell - 24)
            }

            // Top right
            if ((cell + 1) % 23 !== 0 && !obstacles.includes(board[cell - 22])) {
                validMoves.push(cell - 22)
            }

            // Bottom left
            if (cell % 23 !== 0 && !obstacles.includes(board[cell + 22])) {
                validMoves.push(cell + 22)
            }

            // Bottom right
            if ((cell + 1) % 23 !== 0 && !obstacles.includes(board[cell + 24])) {
                validMoves.push(cell + 24)
            }
        })
    }

    const getValidMoves = () => {
        validMoves = [];

        calcValidSingleMove([board.indexOf('a')]);
        calcValidSingleMove(validMoves);
        calcValidSingleMove(validMoves);
        calcValidSingleMove(validMoves);
    }

    const undoLastMove = () => {
        if (moveHistory.length === 1) return;

        board[board.indexOf('a')] = ' ';
        board = board;
        board[moveHistory[moveHistory.length - 2]] = 'a';
        board = board;

        moveHistory.pop();
        moveHistory = moveHistory;

        getValidMoves();
    }

    const getHunterLineOfSight = () => {
        const obstacles = ['.', 'h', 'oi', 'oc'];
        const hunterPositions = board.map((cell, i) => cell === 'h' ? i : '').filter(String);

        hunterPositions.forEach(hunterPosition => {
            let northVisible = true;
            let southVisible = true;
            let eastVisible = true;
            let westVisible = true;

            let northLineOfSight = 1;
            let southLineOfSight = 1;
            let eastLineOfSight = 1;
            let westLineOfSight = 1;

            while(westVisible) {
                if ((hunterPosition - westLineOfSight + 1) % 23 !== 0 && !obstacles.includes(board[hunterPosition - westLineOfSight])) {
                    hunterLineOfSight = [...hunterLineOfSight, hunterPosition - westLineOfSight];
                    westLineOfSight++;
                } else {
                    westVisible = false;
                }
            }

            while(eastVisible) {
                if ((hunterPosition + eastLineOfSight) % 23 !== 0 && !obstacles.includes(board[hunterPosition + eastLineOfSight])) {
                    hunterLineOfSight = [...hunterLineOfSight, hunterPosition + eastLineOfSight];
                    eastLineOfSight++;
                } else {
                    eastVisible = false;
                }
            }

            while(northVisible) {
                if ((hunterPosition - northLineOfSight * 23) >= 0 && !obstacles.includes(board[hunterPosition - northLineOfSight * 23])) {
                    hunterLineOfSight = [...hunterLineOfSight, hunterPosition - northLineOfSight * 23];
                    northLineOfSight++;
                } else {
                    northVisible = false;
                }
            }

            while(southVisible) {
                if ((hunterPosition + southLineOfSight * 23) <= 32 * 23 && !obstacles.includes(board[hunterPosition + southLineOfSight * 23])) {
                    hunterLineOfSight = [...hunterLineOfSight, hunterPosition + southLineOfSight * 23];
                    southLineOfSight++;
                } else {
                    southVisible = false;
                }
            }

            // Checking for roads
            roadSegments.forEach(segment => {
                if (segment.includes(hunterPosition)) {
                    hunterLineOfSight = [...hunterLineOfSight, ...segment.filter(cell => board[cell] !== 'h')];
                }
            })
        });
    }

    const handleCellClick = (i) => {
        if (placingHunters) {
            placeHunter(i)
        } else {
            if (board[i] === 'oi' && (
                board.indexOf('a') === i - 1 ||
                board.indexOf('a') === i + 1 ||
                board.indexOf('a') >= i - 24 && board.indexOf('a') <= i - 22 ||
                board.indexOf('a') >= i + 22 && board.indexOf('a') <= i + 24
            )) {
                board[i] = 'oc';
                board = board;
            } else if (board[i] === 'oc') {
                board[i] = 'oi';
                board = board;
            } else {
                updateAgentPosition(i)
            }
        }
    }
</script>

<main>
    {#if !numPlayersConfirmed}
        <h1>Number of players</h1>

        <form on:submit={() => numPlayersConfirmed = true}>
            <div class="container">
                <ul class="players-wrapper">
                    <li>
                        <input type="radio" id="3" name="number-players" value="3" checked on:change={() => numPlayers = 3}>
                        <label for="3">2-3 players</label>
                    </li>
    
                    <li>
                        <input type="radio" id="4" name="number-players" value="4" on:change={() => numPlayers = 4}>
                        <label for="4">4 players</label>
                    </li>
    
                    <li>
                        <input type="radio" id="5" name="number-players" value="5" on:change={() => numPlayers = 5}>
                        <label for="5">5 players</label>
                    </li>
                </ul>

                <button class="button">Confirm</button>
            </div>
        </form>
    {:else if !agent}
        <div class="container">
            <h1>Choose your agent</h1>

            <ul class="agent-wrapper">
                <li>
                    <button class="select-agent" on:click="{() => {
                        agent = 'spider';
                        setupGame();
                    }}">
                        <img src="img/spider.jpg" alt="Spider" width="100" height="100" />
                        Spider
                    </button>
                </li>
                <li>
                    <button class="select-agent" on:click="{() => {
                        agent = 'bluejay';
                        setupGame();
                    }}">
                        <img src="img/bluejay.jpg" alt="Blue Jay" width="100" height="100" />
                        Blue Jay
                    </button>
                </li>
                <li>
                    <button class="select-agent" on:click="{() => {
                        agent = 'cobra';
                        setupGame();
                    }}">
                        <img src="img/cobra.jpg" alt="Cobra" width="100" height="100" />
                        Cobra
                    </button>
                </li>
                <li>
                    <button class="select-agent" on:click="{() => {
                        agent = 'orangutan';
                        setupGame();
                    }}">
                        <img src="img/orangutan.jpg" alt="Orangutan" width="100" height="100" />
                        Orangutan
                    </button>
                </li>
            </ul>
        </div>
    {:else if !objectivesPlaced && numPlayers !== 4}
        <div class="container">
            <h1>Place objectives</h1>

            <p>Place ojective tiles on the following spaces (picked randomly for each sector) :</p>
            <p>{board.map((cell, i) => cell === 'oi' ? getCellFromIndex(i) : '').filter(String).join(', ')}</p>
            <p>Note that the objectives are also shown directly on the map on the next screen, you may prefer to mark them that way.</p>

            <button class="button" on:click={() => objectivesPlaced = true}>Done</button>
        </div>
    {:else}
        <div class="board">
            {#each board as cell, i}
                <button class="cell" on:click={() => handleCellClick(i)}>
                    <span class="cell-inner">
                        {getCellFromIndex(i)}
                    </span>

                    <!-- Exits -->
                    {#if exits.includes(i)}
                        <div class="exit" />
                    {/if}

                    <!-- Objects -->
                    {#if cell === 'oi'}
                        <img class="objective-incomplete" src="img/objective-incomplete.jpg" alt="Incomplete objective" />
                    {:else if cell === 'oc'}
                        <img class="objective-complete" src="img/objective-complete.jpg" alt="Completed objective" />
                    {:else if cell === '.'}
                        <div class="wall" />
                    {:else if cell === 'a'}
                        <img class="agent" src="{`img/${agent}-thumbnail.jpg`}" alt={agent}>
                    {:else if cell === 'h'}
                        <div class="hunter">H</div>
                    {/if}

                    <!-- Moves -->
                    {#if validMoves.includes(i) && cell !== 'a'}
                        <div class="valid-move" />
                    {/if}

                    <!-- Hunter line of sight -->
                    {#if hunterLineOfSight.includes(i)}
                        <div class="line-of-sight" />
                    {/if}

                    <!-- Compass -->
                    {#if i === 239}
                        <img class="compass" src="{`img/compass.png`}" alt="compass">
                    {/if}
                </button>
            {/each}
        </div>
        <InfoPanel
            moveHistory={moveHistory.map(index => getCellFromIndex(index))}
            undoLastMove={undoLastMove}
            agent={agent}
            placingHunters={placingHunters}
            togglePlacingHunters={togglePlacingHunters}
            numPlayers={numPlayers}
        />
    {/if}
</main>

<style>
    main {
        padding: 1rem;
    }

    .container {
        width: 100%;
        max-width: 400px;
        margin: 0 auto;
        padding: 0 1rem;
    }

    .players-wrapper, .agent-wrapper {
        list-style: none;
        margin: 0 0 2rem;
        padding: 0;
        display: flex;
        flex-direction: column;
        gap: 1rem;
        font-size: 1.2rem;
    }

    .players-wrapper input[type="radio"] {
        width: 1.2rem;
        height: 1.2rem;
    }

    .select-agent {
        width: 100%;
        display: flex;
        align-items: center;
        padding: 0;
        font-size: 1.4rem;
        border: 1px solid var(--secondary-color);
        background-color: var(--secondary-color-transparent);
        color: var(--white);
        border-radius: 4px;
        cursor: pointer;
        overflow: hidden;
    }

    .select-agent img {
        margin-right: 1rem;
    }

    .board {
        max-width: 800px;
        margin: 0 auto 80px;
        display: grid;
        grid-template-columns: repeat(23, 1fr);
        gap: 1px;
        background-color: var(--dark-grey);
    }

    .cell {
        position: relative;
        padding: 0;
        padding-top: 100%;
        background-color: transparent;
        border: none;
        width: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        background-color: var(--white);
        color: var(--light-grey);
        font-size: .7rem;
        cursor: pointer;
    }

    .cell-inner {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
    }

    .agent, .hunter, .exit, .objective-incomplete, .objective-complete, .wall, .valid-move, .line-of-sight {
        position: absolute;
        inset: 0;
        width: 100%;
    }

    .agent, .hunter {
        border-radius: 50%;
    }

    .agent {
        border: 1px solid red;
    }

    .hunter {
        background-color: blue;
        display: flex;
        justify-content: center;
        align-items: center;
        color: var(--white);
        font-size: .7rem;
    }

    .wall {
        background-color: var(--dark-grey);
    }

    .exit {
        background-color: hsla(47, 80%, 75%, .5);
    }

    .valid-move {
        background-color: hsla(119, 64%, 40%, .5);
    }

    .line-of-sight {
        background-color: hsla(0, 100%, 50%, .5);
    }

    .compass {
        position: absolute;
        inset: 0;
        width: calc(200% + 1px);
        z-index: 10;
    }
</style>