<script>
    import '../app.css';
    import InfoPanel from '../components/InfoPanel.svelte';
    import * as shadowsOfBabelInfo from '../shadowsOfBabel';
    import * as brokenCovenantInfo from '../brokenCovenant';

    let board, roadSegments, exits, extraExits, sectorObjectives, compass, fourHuntersStartingPosition;
    let gameBoardSelected = false;
    let agent = '';
    let numPlayers = 3;
    let numPlayersConfirmed = false;
    let objectivesPlaced = false;
    let validMoves = [];
    let moveHistory = [];
    let hunterLineOfSight = [];
    let placingHunters = false;
    let panelCollapsed = false;

    const blockingObjects = ['.', 'a', 'oi', 'oc', 'ct', 'cl', 'cb', 'cr', 'ctu', 'clu', 'cbu', 'cru'];

    const setupGame = () => {
        // Place objectives
        sectorObjectives.forEach(sector => {
            const selectedObjective = sector[Math.floor(Math.random() * sector.length)];
            board[selectedObjective] = 'oi';
            board[sector] = 'oi';
            board = board;
        });

        // Place hunters starting position
        board[numPlayers === 4 ? fourHuntersStartingPosition : 378] = 'h';
        board = board;

        // Place additional exit
        if (numPlayers === 4 || numPlayers === 5) {
            exits = [...exits, ...extraExits]; 
        }

        // Log initial agent position
        moveHistory = [board.indexOf('a')];

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
        if (!blockingObjects.includes(board[i])) {
            board[i] = 'h';
            board = board;

            getHunterLineOfSight();
        }

        if (board.filter(cell => cell === 'h').length >= numPlayers - 1) {
            togglePlacingHunters();
        }
    }

    const togglePlacingHunters = () => {
        placingHunters = !placingHunters;

        if (placingHunters) {
            board = [...board.map(cell => cell === 'h' ? ' ' : cell)];
            hunterLineOfSight = [];
        } else {
            panelCollapsed = true;
        }

        getValidMoves();
    }

    const calcValidSingleMove = (boardCells) => {
        const obstacles = [...blockingObjects, 'h'];

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
        const hunterPositions = board.map((cell, i) => cell === 'h' ? i : '').filter(String);
        hunterLineOfSight = [...hunterPositions];

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
                if ((hunterPosition - westLineOfSight + 1) % 23 !== 0 && !blockingObjects.includes(board[hunterPosition - westLineOfSight])) {
                    hunterLineOfSight = [...hunterLineOfSight, hunterPosition - westLineOfSight];
                    westLineOfSight++;
                } else {
                    westVisible = false;
                }
            }

            while(eastVisible) {
                if ((hunterPosition + eastLineOfSight) % 23 !== 0 && !blockingObjects.includes(board[hunterPosition + eastLineOfSight])) {
                    hunterLineOfSight = [...hunterLineOfSight, hunterPosition + eastLineOfSight];
                    eastLineOfSight++;
                } else {
                    eastVisible = false;
                }
            }

            while(northVisible) {
                if ((hunterPosition - northLineOfSight * 23) >= 0 && !blockingObjects.includes(board[hunterPosition - northLineOfSight * 23])) {
                    hunterLineOfSight = [...hunterLineOfSight, hunterPosition - northLineOfSight * 23];
                    northLineOfSight++;
                } else {
                    northVisible = false;
                }
            }

            while(southVisible) {
                if ((hunterPosition + southLineOfSight * 23) <= 32 * 23 && !blockingObjects.includes(board[hunterPosition + southLineOfSight * 23])) {
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
            switch (board[i]) {
                case 'oi':
                    board[i] = 'oc';
                    break;
                case 'oc':
                    board[i] = 'oi';
                    break;
                case 'ct':
                    board[i] = 'ctu';
                    break;
                case 'cr':
                    board[i] = 'cru';
                    break;
                case 'cb':
                    board[i] = 'cbu';
                    break;
                case 'cl':
                    board[i] = 'clu';
                    break;
                case 'ctu':
                    board[i] = 'ct';
                    break;
                case 'cru':
                    board[i] = 'cr';
                    break;
                case 'cbu':
                    board[i] = 'cb';
                    break;
                case 'clu':
                    board[i] = 'cl';
                    break;
                default:
                    updateAgentPosition(i);
            }

            board = board;
        }
    }

    const togglePanel = () => panelCollapsed = !panelCollapsed;
</script>

<main>
    <div class="rotate-device">
        <img src="/img/rotate-device.svg" alt="Rotate device">
        <p>It looks like you're on a device with a small screen (maybe a phone ?). We recommend using the app in landscape mode.</p>
    </div>
    {#if !gameBoardSelected}
        <h1>Choose your board</h1>

        <ul class="game-board-wrapper">
            <li>
                <button on:click={() => {
                    ({board, roadSegments, exits, extraExits, sectorObjectives, compass, fourHuntersStartingPosition} = shadowsOfBabelInfo);
                    gameBoardSelected = true;
                }}>
                    <img src="img/shadows-of-babel.jpg" alt="Shadows of Babel" width="300" height="300" />
                </button>

                <p>Shadows of Babel (original game)</p>
            </li>
    
            <li>
                <button on:click={() => {
                    ({board, roadSegments, exits, extraExits, sectorObjectives, compass, fourHuntersStartingPosition} = brokenCovenantInfo);
                    gameBoardSelected = true;
                }}>
                    <img src="img/broken-covenant.jpg" alt="Broken Covenant" width="300" height="300" />
                </button>

                <p>Broken Covenant</p>
            </li>
        </ul>
    {:else if !numPlayersConfirmed}
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
                <li>
                    <button class="select-agent" on:click="{() => {
                        agent = 'fox';
                        setupGame();
                    }}">
                        <img src="img/fox.jpg" alt="Fox" width="100" height="100" />
                        Fox
                    </button>
                </li>
                <li>
                    <button class="select-agent" on:click="{() => {
                        agent = 'mantis';
                        setupGame();
                    }}">
                        <img src="img/mantis.jpg" alt="Mantis" width="100" height="100" />
                        Mantis
                    </button>
                </li>
                <li>
                    <button class="select-agent" on:click="{() => {
                        agent = 'panther';
                        setupGame();
                    }}">
                        <img src="img/panther.jpg" alt="Panther" width="100" height="100" />
                        Panther
                    </button>
                </li>
                <li>
                    <button class="select-agent" on:click="{() => {
                        agent = 'raven';
                        setupGame();
                    }}">
                        <img src="img/raven.jpg" alt="Raven" width="100" height="100" />
                        Raven
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

                    <!-- Moves -->
                    {#if validMoves.includes(i)}
                        <div class="valid-move" />
                    {/if}

                    <!-- Hunter line of sight -->
                    {#if hunterLineOfSight.includes(i)}
                        <div class="line-of-sight" />
                    {/if}

                    <!-- Exits -->
                    {#if exits.includes(i)}
                        <img class="exit" src="img/exit.svg" alt="Exit" />
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
                    {:else if cell === 'cb'}
                        <img class="cache" src="img/cache.svg" alt="Supply cache below this tile" />
                    {:else if cell === 'cl'}
                        <img class="cache cache-left" src="img/cache.svg" alt="Supply cache left of this tile" />
                    {:else if cell === 'cr'}
                        <img class="cache cache-right" src="img/cache.svg" alt="Supply cache right of this tile" />
                    {:else if cell === 'ct'}
                        <img class="cache cache-top" src="img/cache.svg" alt="Supply cache top of this tile" />
                    {:else if cell === 'ctu' || cell === 'cru' || cell === 'cbu' || cell === 'clu'}
                        <img class="cache cache-used" src="img/cache-used.svg" alt="Used supply cached next to this tile" />
                    {/if}

                    <!-- Compass -->
                    {#if i === compass}
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
            panelCollapsed={panelCollapsed}
            togglePanel={togglePanel}
        />
    {/if}
</main>

<style>
    main {
        padding: 1rem;
    }

    .rotate-device {
        display: none;
        position: fixed;
        inset: 0;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        padding: 0 1rem;
        background-color: var(--dark-grey);
    }

    @media screen and (orientation: portrait) and (max-width: 500px) {
        .rotate-device {
            display: flex;
        }
    }

    .container {
        width: 100%;
        max-width: 400px;
        margin: 0 auto;
        padding: 0 1rem;
    }

    .game-board-wrapper {
        list-style: none;
        display: flex;
        gap: 2rem;
        justify-content: center;
        flex-wrap: wrap;
    }

    .game-board-wrapper li {
        text-align: center;
    }

    .game-board-wrapper button {
        padding: 0;
        border: none;
        background: transparent;
        cursor: pointer;
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

    .agent, .hunter, .exit, .objective-incomplete, .objective-complete, .wall, .valid-move, .line-of-sight, .cache {
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

    .cache {
        background-color: var(--dark-grey);
    }

    .cache.cache-top {
        transform: rotate(180deg);
    }

    .cache.cache-left {
        transform: rotate(90deg);
    }

    .cache.cache-right {
        transform: rotate(-90deg);
    }
</style>