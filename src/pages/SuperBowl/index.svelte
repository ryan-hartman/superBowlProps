<script>
    import { Field,Input,Button, Details, Modal, Radio } from 'svelte-chota'
    import VirtualList from '@sveltejs/svelte-virtual-list';

    let modalOpen = false;

    let props = [
        {
            bet: "Length of the National Anthem",
            points: 3,
            sideOne: "Over 203.5",
            sideTwo: "Under 203.5"
        },
        {
            bet: "Will the coin toss be heads or tails?",
            points: 3,
            sideOne: "Heads",
            sideTwo: "Tails"
        },
        {
            bet: "Which team will win the coin toss?",
            points: 5,
            sideOne: "Kansas City",
            sideTwo: "Tampa Bay"
        },
        {
            bet: "Which team will score first?",
            points: 5,
            sideOne: "Kansas City",
            sideTwo: "Tampa Bay"
        },
        {
            bet: "Jersey Number of the first player to score",
            points: 5,
            sideOne: "Odd",
            sideTwo: "Even"
        },
        {
            bet: "Which half will have more points scored?",
            points: 5,
            sideOne: "First Half",
            sideTwo: "Second Half"
        },
        {
            bet: "Will there be a successful fieldgoal over 49.5 yards?",
            points: 7,
            sideOne: "Yes",
            sideTwo: "No"
        },
        {
            bet: "Which team will score last?",
            points: 7,
            sideOne: "Kansas City",
            sideTwo: "Tampa Bay"
        },
        {
            bet: "Which team will win the game?",
            points: 10,
            sideOne: "Kansas City",
            sideTwo: "Tampa Bay"
        },
        {
            bet: "Total Points Scored in the Game",
            points: 10,
            sideOne: "Over 54.5",
            sideTwo: "Under 54.5"
        },
        
    ];

    let propSubmission = {
        propPicks: {

        },
        name: "",
        tb1: 0,
        tb2: 0
    }

    let submitProps = () => {
        fetch(
            "https://tweetletter.azurewebsites.net/api/SubmitPropPicks",
            {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(propSubmission)
            }
        ).then(resp => {
            console.log(resp)
            modalOpen = false;
            // dispatch("addnewsletter")
        })
    }

    const fetchScores = async () => {
        return await fetch("https://tweetletter.azurewebsites.net/api/GetPropResults")
        .then(r => r.json())
    };

    let resultsPromise = fetchScores()
</script>


<div>

    <Button on:click={fetchScores}>Get Score</Button>

    <div class="results">
        <h3>Results</h3>
        {#await resultsPromise}
            Loading Scores
        {:then results} 
            <table>
                <tr>
                    <th>Name</th>
                    <th>Points</th>
                </tr>
                {#each results as result}
                    <tr>
                        <td>{result.Name}</td>
                        <td>{result.points}</td>
                    </tr>
                {/each}
            </table>
        {/await}
    </div>
    <br>
    <br>
    <Details>
        <span slot="summary">Props</span>
        <table>
            <tr>
                <th>Prop</th>
                <th>Side One</th>
                <th>Side Two</th>
                <th>Points</th>
            </tr>
            {#each props as prop}
            <tr>
                <td>{prop.bet}</td>
                <td>{prop.sideOne}</td>
                <td>{prop.sideTwo}</td>
                <td>{prop.points}</td>
            </tr>
            {/each}
        </table>
    </Details>

    <Button on:click={event => modalOpen = true}>Submit Picks</Button>

    <Modal bind:open={modalOpen} style="width:60%;background-color: #243447;padding:15px">
        <Field label="Your Name" gapless>
            <Input bind:value={propSubmission["name"]}/>
        </Field>
        <VirtualList height="35vh" items={props} let:item>
            <Field label={item.bet +" Points: " + item.points}>
                <Radio value="A" bind:group={propSubmission.propPicks[item.bet]}> {item.sideOne} </Radio>
                <Radio value="B" bind:group={propSubmission.propPicks[item.bet]}> {item.sideTwo} </Radio>
            </Field>
        </VirtualList>
        <Field label="Tie Break 1: Total Points Scored">
            <Input bind:value={propSubmission["tb1"]}/>
        </Field>
        <Field label="Tie Break 2: Total Cobined Yards">
            <Input bind:value={propSubmission["tb2"]}/>
        </Field>
        <Button on:click={submitProps}>Submit</Button>
    </Modal>
    
</div>