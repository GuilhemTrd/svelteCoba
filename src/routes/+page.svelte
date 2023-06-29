<script>
    let myDices = [];
    let teamOne = [];
    let teamTwo = [];

    //Drag and drop
    let draggingDice = null;

    // Game
    let showResult = null
    let playIsClicked = false;

    // Score
    // Score
    $: scoreTeamOneShow = teamOne.reduce((a, b) => {
        let min = Math.min(...teamOne.map(die => die.value));
        let isPurpleExists = teamOne.some(die => die.value === 6);
        if (b.value === 1 && !(isPurpleExists && min === 1)) { // Gris
            return a + 1;
        } else if (b.value === 2 && !(isPurpleExists && min === 2)) { // Vert
            return a + 2;
        } else if (b.value === 3 && !(isPurpleExists && min === 3)) { // Orange
            return a + (teamOne.length % 2 === 0 ? 2 : 1);
        } else if (b.value === 4 && !(isPurpleExists && min === 4)) { // Jaune
            return a - 1;
        } else if (b.value === 5) { // Bleu clair
            return a + teamTwo.length;
        } else if (b.value === 6) { // Violet
            return a + 3;
        } else {
            return a;
        }
    }, 0);

    $: scoreTeamTwoShow = teamTwo.reduce((a, b) => {
        let min = Math.min(...teamTwo.map(die => die.value));
        let isPurpleExists = teamTwo.some(die => die.value === 6);
        if (b.value === 1 && !(isPurpleExists && min === 1)) { // Gris
            return a + 1;
        } else if (b.value === 2 && !(isPurpleExists && min === 2)) { // Vert
            return a + 2;
        } else if (b.value === 3 && !(isPurpleExists && min === 3)) { // Orange
            return a + (teamTwo.length % 2 === 0 ? 2 : 1);
        } else if (b.value === 4 && !(isPurpleExists && min === 4)) { // Jaune
            return a - 1;
        } else if (b.value === 5) { // Bleu clair
            return a + teamOne.length;
        } else if (b.value === 6) { // Violet
            return a + 3;
        } else {
            return a;
        }
    }, 0);
    function canFinishGame(dices) {
        let totalSum = dices.reduce((a, b) => a + b.value, 0);

        if (totalSum % 2 !== 0) {
            return false;
        }

        let targetSum = totalSum / 2;
        let verif = Array(targetSum + 1).fill(false);
        verif[0] = true;

        for (let indexDices = 0; indexDices < dices.length; indexDices++) {
            for (let indexTarget = targetSum; indexTarget >= dices[indexDices].value; indexTarget--) {
                verif[indexTarget] = verif[indexTarget] || verif[indexTarget - dices[indexDices].value];
            }
        }

        return verif[targetSum];
    }
    function createDices() {
        myDices = [];
        teamOne = [];
        teamTwo = [];
        for (let i = 0; i < 7; i++) {
            myDices.push({value: Math.floor(Math.random() * 6) + 1, index: i, team: 'myDices'});
        }

        if (canFinishGame(myDices)) {
            playIsClicked = true;
            showResult = null;
        } else {
            createDices();
        }
    }

    function handleDragStart(e, dice) {
        draggingDice = dice;
        e.dataTransfer.setData('text/plain', JSON.stringify(dice));
    }

    function handleDrop(e, location) {
        e.preventDefault();
        if (draggingDice) {
            const diceIndex = draggingDice.index;
            if (draggingDice.team === 'myDices') {
                myDices[diceIndex] = null;
            } else if (draggingDice.team === 'teamOne') {
                teamOne = teamOne.filter(dice => dice.index !== diceIndex);
            } else if (draggingDice.team === 'teamTwo') {
                teamTwo = teamTwo.filter(dice => dice.index !== diceIndex);
            }

            draggingDice.team = location;
            if (location === 'teamOne') {
                teamOne = [...teamOne, draggingDice];
            } else if (location === 'teamTwo') {
                teamTwo = [...teamTwo, draggingDice];
            } else if (location === 'myDices') {
                myDices[diceIndex] = draggingDice;
            }
            draggingDice = null;
        }
    }

    function calculateScore() {
        // Faire les calculs
        let scoreTeamOne = teamOne.reduce((a, b) => {
            let min = Math.min(...teamOne.map(die => die.value));
            let isPurpleExists = teamOne.some(die => die.value === 6);
            if (b.value === 1 && !(isPurpleExists && min === 1)) { // Gris
                return a + 1;
            } else if (b.value === 2 && !(isPurpleExists && min === 2)) { // Vert
                return a + 2;
            } else if (b.value === 3 && !(isPurpleExists && min === 3)) { // Orange
                return a + (teamOne.length % 2 === 0 ? 2 : 1);
            } else if (b.value === 4 && !(isPurpleExists && min === 4)) { // Jaune
                return a - 1;
            } else if (b.value === 5) { // Bleu clair
                return a + teamTwo.length;
            } else if (b.value === 6) { // Violet
                return a + 3;
            } else {
                return a;
            }
        }, 0);

        let scoreTeamTwo = teamTwo.reduce((a, b) => {
            let min = Math.min(...teamTwo.map(die => die.value));
            let isPurpleExists = teamTwo.some(die => die.value === 6);
            if (b.value === 1 && !(isPurpleExists && min === 1)) { // Gris
                return a + 1;
            } else if (b.value === 2 && !(isPurpleExists && min === 2)) { // Vert
                return a + 2;
            } else if (b.value === 3 && !(isPurpleExists && min === 3)) { // Orange
                return a + (teamTwo.length % 2 === 0 ? 2 : 1);
            } else if (b.value === 4 && !(isPurpleExists && min === 4)) { // Jaune
                return a - 1;
            } else if (b.value === 5) { // Bleu clair
                return a + teamOne.length;
            } else if (b.value === 6) { // Violet
                return a + 3;
            } else {
                return a;
            }
        }, 0);

        if (scoreTeamOne === scoreTeamTwo) {
            showResult = 'Bravo !' + scoreTeamOne + ' points chacun !';
        } else {
            showResult = 'Dommage !' + scoreTeamOne + ' points pour l\'équipe 1 et ' + scoreTeamTwo + ' points pour l\'équipe 2 !';
        }
    }
</script>
<body>
<h1 class="centered-text">Jeu de Coba - V1</h1>

<div class="btn-container">
    {#if playIsClicked === false}
        <button on:click={() => createDices()}>Lancer les dés</button>
    {/if}
    {#if playIsClicked === true}
        <button on:click={() => createDices()}>Rejouer</button>
    {/if}
</div>

{#if playIsClicked === true}
    <h2 class="centered-text">Mes dés</h2>

    <div class="dice-container" on:dragover={(e) => e.preventDefault()} on:drop={(e) => handleDrop(e, 'myDices')}>
        {#each myDices as dice, i (i)}
            {#if dice}
                <div draggable="true" on:dragstart={(e) => handleDragStart(e, dice)}
                     class="dice {dice && 'dice-value-' + dice.value} {dice === null && 'dice-null'}"></div>
            {/if}
        {/each}
    </div>
{/if}

<div class="teams-wrapper">
    <div class="team-container" on:dragover={(e) => e.preventDefault()} on:drop={(e) => handleDrop(e, 'teamOne')}>
        <h2>Équipe 1 / Score {scoreTeamOneShow}</h2>
        {#each teamOne as dice, i (i)}
            <div draggable="true" on:dragstart={(e) => handleDragStart(e, dice)}
                 class="team-dice {dice && 'dice-value-' + dice.value} {dice === null && 'dice-null'}"></div>
        {/each}
    </div>

    <div class="btn-container">
        <button on:click={() => calculateScore()}
                disabled={myDices.filter(dice => dice !== null).length !== 0 || showResult !== null || playIsClicked !== true}>
            Calculer le score
        </button>

        {#if showResult && myDices.filter(dice => dice !== null).length === 0}
            <p class="result">{showResult}</p>
        {/if}
    </div>

    <div class="team-container" on:dragover={(e) => e.preventDefault()} on:drop={(e) => handleDrop(e, 'teamTwo')}>
        <h2>Équipe 2 / Score {scoreTeamTwoShow}</h2>
        {#each teamTwo as dice, i (i)}
            <div draggable="true" on:dragstart={(e) => handleDragStart(e, dice)}
                 class="team-dice {dice && 'dice-value-' + dice.value} {dice === null && 'dice-null'}"></div>
        {/each}
    </div>
</div>

<div class="rules-table-container">
    <table class="rules-table">
        <thead>
        <tr>
            <th>Couleur</th>
            <th>Règle</th>
        </tr>
        </thead>
        <tbody>
        <tr>
            <td style="background-color: grey;">Gris</td>
            <td>Ajoute 1</td>
        </tr>
        <tr>
            <td style="background-color: green;">Vert</td>
            <td>Ajoute 2</td>
        </tr>
        <tr>
            <td style="background-color: orange;">Orange</td>
            <td>Ajoute 2 si nombre de dés dans l'équipe est pair, sinon 1</td>
        </tr>
        <tr>
            <td style="background-color: yellow;">Jaune</td>
            <td>Soustrait 1 au score total de l'équipe</td>
        </tr>
        <tr>
            <td style="background-color: lightskyblue;">Bleu clair</td>
            <td>Ajoute le nombre total de dés de l'équipe adverse au score</td>
        </tr>
        <tr>
            <td style="background-color: mediumpurple;">Violet</td>
            <td>Ajoute 3 et réduis à 0 tous les dés de la valeur la plus faible de son groupe hormis elle-même </td>
        </tr>
        </tbody>
    </table>
</div>

</body>

<style>
    body {
        background-color: #EDF0F4;
        font-family: 'Helvetica', sans-serif;
        margin: 0;
        padding: 0;
        box-sizing: border-box;
        width: 100%;
        height: 100%;
        min-height: 100vh;
    }

    .centered-text {
        text-align: center;
        color: #334257;
    }

    .btn-container {
        margin: 50px auto;
        width: 120px;
    }

    button {
        background-color: #EEF0F4;
        color: #334257;
        border: none;
        border-radius: 15px;
        padding: 10px 20px;
        box-shadow: inset 9.91px 9.91px 15px #D9DADE, inset -9.91px -9.91px 15px #FFFFFF;
        transition: background-color 0.2s;
    }

    button:hover {
        box-shadow: inset 1px 1px 2px #b8b9be, inset -1px -1px 2px #ffffff;
    }

    .dice-container {
        background: #EEF0F4;
        min-height: 70px;
        margin: auto;
        width: 520px;
        border-radius: 15px;
        padding: 10px;
        display: flex;
        justify-content: space-around;
        flex-wrap: wrap;
        box-shadow: inset 9.91px 9.91px 15px #D9DADE, inset -9.91px -9.91px 15px #FFFFFF;
    }

    .dice {
        display: inline-flex;
        justify-content: center;
        align-items: center;
        margin: 10px;
        width: 50px;
        height: 50px;
        border-radius: 50%;
        box-shadow: 7px 7px 15px #b8b9be, -4px -4px 13px #ffffff;
        transition: transform 0.2s;
    }

    .dice:hover {
        box-shadow: inset 1px 1px 2px #b8b9be, inset -1px -1px 2px #ffffff;
    }

    .dice-value-0 {
        background-color: red;
    }

    .dice-value-1 {
        background-color: grey;
    }

    .dice-value-2 {
        background-color: green;
    }

    .dice-value-3 {
        background-color: orange;
    }

    .dice-value-4 {
        background-color: yellow;
    }

    .dice-value-5 {
        background-color: lightskyblue;
    }

    .dice-value-6 {
        background-color: mediumpurple;
    }

    .dice-value-null {
        background-color: red;
    }

    .team-container {
        width: 300px;
        min-height: 214px;
        background: #EEF0F4;
        margin: 20px auto;
        padding: 10px;
        border-radius: 15px;
        box-shadow: inset 9.91px 9.91px 15px #D9DADE, inset -9.91px -9.91px 15px #FFFFFF;
    }

    h2 {
        color: #334257;
        text-align: center;
    }

    .team-dice {
        display: inline-block;
        margin: 10px;
        width: 50px;
        height: 50px;
        border-radius: 50%;
        box-shadow: 7px 7px 15px #b8b9be, -4px -4px 13px #ffffff;
        transition: box-shadow 0.3s ease, transform 0.3s ease;
    }

    .team-dice:hover {
        box-shadow: -6px -6px 12px #fff,
        6px 6px 12px rgba(0, 0, 0, 0.1);
        transform: translateY(-5px);
    }

    .result {
        font-size: 24px;
        font-weight: 900;
        color: #5d6d7e;
    }

    .teams-wrapper {
        display: flex;
        justify-content: space-between;
    }

    .rules-table-container {
        width: 100%;
        margin: 50px auto;
    }

    .rules-table {
        width: 100%;
        text-align: left;
        border-collapse: collapse;
    }

    .rules-table th, .rules-table td {
        padding: 10px;
        border: 1px solid #ddd;
    }

    .rules-table th {
        background-color: #f2f2f2;
    }

    .rules-table td {
        text-align: center;
    }
</style>



