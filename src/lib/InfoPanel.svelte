<script>
    export let moveHistory;
    export let undoLastMove;
    export let agent;
    export let placingHunters;
    export let togglePlacingHunters;
    export let numPlayers;

    let panelCollapsed = false;
    let maxLife = 4;
    if (agent === 'orangutan') { maxLife += 2 };
    if (numPlayers === 4) { maxLife += 2 };
    let currentLife = maxLife;

    const updateLife = (i) => {
        if (i === currentLife - 1) {
            currentLife--;
        } else if (i === currentLife) {
            currentLife++;
        }
    }
</script>

<div class="panel-wrapper" class:collapsed={panelCollapsed}>
    <button class="toggle-panel" on:click={() => panelCollapsed = !panelCollapsed}>
        <svg width="20" height="20" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M2.2742 4.70021C1.76796 4.19397 0.947191 4.19397 0.440955 4.70021C-0.0652792 5.20644 -0.0652793 6.02721 0.440955 6.53345L9.08337 15.1759C9.50149 15.594 10.1342 15.6668 10.627 15.3942C10.7538 15.3327 10.8726 15.2493 10.9779 15.1441L19.6203 6.50167C20.1265 5.99544 20.1265 5.17467 19.6203 4.66843C19.1141 4.1622 18.2933 4.1622 17.7871 4.66843L10.0147 12.4408L2.2742 4.70021Z" fill="white"/>
        </svg>  
        toggle panel
    </button>

    <div class="actions">
        <button class="button" on:click={undoLastMove}>
            <svg width="15" height="15" viewBox="0 0 15 15" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M6.45114 9.03592L5.2273 10.2339L0 5.11696L5.2273 0L6.45114 1.19801L3.31298 4.26975L8.07692 4.26985C11.8622 4.26985 14.9379 7.2436 14.9991 10.9347L15 11.0468V15H13.2692V11.0468C13.2692 8.27087 10.9959 6.01471 8.17231 5.96492L8.07692 5.96408L3.31298 5.96399L6.45114 9.03592Z" fill="white"/>
            </svg>
            Undo last move
        </button>
        <button class="button--primary" class:button={!placingHunters} on:click={togglePlacingHunters}>{placingHunters ? 'Finish placing hunters' : 'Place hunters on the board'}</button>
    </div>
    
    <h2 class="title turn-title">Turn {moveHistory.length} / 40 - movement log :</h2>
    <ul class="move-log">
        {#each moveHistory as move, i}
            <li>
                <div class="turn">{i}</div>
                <div class="move">{move}</div>
            </li>
        {/each}
    </ul>

    <div class="health-wrapper">
        <h2 class="title">Health</h2>
        <div class="life">
            {#each Array(maxLife) as _, i}
                <button class="life-point" on:click={() => updateLife(i)}>
                    <svg class:empty={currentLife <= i} width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <path d="M12.1784 0.237644C12.0222 -0.00362108 11.7 -0.0725807 11.4588 0.0836339C11.3971 0.123555 11.3447 0.176002 11.3048 0.237644C11.0155 0.755028 4.23529 12.9465 4.23529 16.4942C4.23529 20.6395 7.59573 24 11.7411 24C15.8865 24 19.2469 20.6395 19.2469 16.4942C19.2469 12.9465 12.4667 0.755028 12.1784 0.237644Z" fill="#D32F2F"/>
                    </svg>
                    health point              
                </button>
            {/each}
        </div>
    </div>
</div>

<style>
    .panel-wrapper {
        position: fixed;
        z-index: 20;
        left: 0;
        bottom: 0;
        width: 100vw;
        padding: 0 1rem 1rem;
        background-color: var(--dark-grey);
        border-top: 1px solid var(--white);
        transition: transform .2s ease-in-out;
    }

    .panel-wrapper.collapsed {
        transform: translateY(calc(100% - 64px))
    }

    .panel-wrapper.collapsed svg {
        transform: rotate(180deg);
    }

    .toggle-panel {
        width: 100%;
        display: flex;
        justify-content: center;
        background-color: transparent;
        border: none;
        margin-bottom: 1rem;
        padding: 1.4rem;
        cursor: pointer;
        font-size: 0;
    }

    .toggle-panel svg {
        margin: 0;
    }

    .actions {
        margin-bottom: 2rem;
        display: flex;
        justify-content: space-between;
        gap: 1rem;
    }

    .actions button {
        max-width: 50%;
    }

    .title {
        margin: 0;
        font-size: 1.5rem;
    }

    .turn-title {
        margin-bottom: .5rem;
    }
    

    .move-log {
        list-style: none;
        margin: 0 0 2rem;
        padding: 0;
        display: flex;
        overflow-x: auto;
    }

    .turn, .move {
        padding: 4px;
        font-size: .8rem;
        text-align: center;
        border: 1px solid var(--white);
    }

    .turn {
        background-color: var(--dark-grey);
    }

    .move {
        background-color: var(--white);
        color: var(--dark-grey);
    }

    .health-wrapper {
        display: flex;
        align-items: center;
        flex-wrap: wrap;
        gap: 1rem;
    }

    .life {
        display: flex;
        gap: .5rem;
    }

    .life-point {
        padding: 6px;
        border: none;
        background-color: var(--white);
        border-radius: 50%;
        font-size: 0;
        cursor: pointer;
    }

    .life-point svg.empty path {
        fill: var(--dark-grey);
    }
</style>