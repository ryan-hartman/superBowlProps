<script>

    import { Field,Input,Button, Details, Modal, Radio } from 'svelte-chota'
    import VirtualList from '@sveltejs/svelte-virtual-list';

    let props = [
        {
            bet: "Length of the National Anthem",
            points: 3,
            sideOne: "Over 203.5 Seconds",
            sideTwo: "Under 203.5 Seconds"
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

    let updateProps = () => {
        let updates = []
        props.forEach(prop => {
            if(propSubmission.propPicks[prop.bet] != "") {
                let update = {
                    Prop: prop.bet,
                    Result: propSubmission.propPicks[prop.bet],
                    Points: prop.points
                };
                updates.push(update)
            }
        });
        
        fetch(
            "https://tweetletter.azurewebsites.net/api/UpdatePropResults",
            {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(updates)
            }
        ).then(resp => {
            console.log("Updated")
        })
    }

</script>

<div>
    <VirtualList height="80vh" items={props} let:item>
        <Field label={item.bet +" Points: " + item.points}>
            <Radio value="A" bind:group={propSubmission.propPicks[item.bet]}> {item.sideOne} </Radio>
            <Radio value="B" bind:group={propSubmission.propPicks[item.bet]}> {item.sideTwo} </Radio>
        </Field>
    </VirtualList>
    <Button on:click={updateProps}>Update</Button>
</div>