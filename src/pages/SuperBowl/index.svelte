<script>
    import { Field,Input,Button, Details, Modal, Radio } from 'svelte-chota'
    import VirtualList from '@sveltejs/svelte-virtual-list';
    import {Props} from "../../data"
    import {isActive, url} from '@roxi/routify'
    let modalOpen = false;

    let props = Props

    let propSubmission = {
        propPicks: {

        },
        name: "",
        tb1: 0,
        tb2: 0
    }

    const fetchScores = async () => {
        return await fetch("https://ryan-hartman.azurewebsites.net/api/SuperBowl/GetPropResults")
        .then(r => r.json())
    };

    let resultsPromise = fetchScores()

    let isValid = (submission) => {

        let valid = true;

        Object.keys(submission.propPicks).forEach(element => {
            valid = valid && (submission.propPicks[element] != "")
        });

        return valid 
        &&(submission.name!="")
        &&(submission.tb1!=0)
        &&(submission.tb2!=0)
        &&(Object.keys(submission.propPicks).length == props.length)
    }

    let submitProps = () => {

        if(!isValid(propSubmission)) {
            alert("Please make sure everything is filled out. Scroll down on the questions to see more.")
            return;
        }

        fetch(
            "https://ryan-hartman.azurewebsites.net/api/SuperBowl/SubmitPropPicks",
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
            new Promise(resolve => setTimeout(resolve, 150));
            resultsPromise = fetchScores()
            // dispatch("addnewsletter")
        })
    }

</script>

<style>
    .tableStyle {
        border: 1px solid rgb(255, 255, 255);
    }
    .thStyle, .tdStyle {
        border-collapse: collapse;
        border-bottom: 1px solid rgb(255,255,255);
    }
</style>

<div>
    <div class="results">
        <h3>Results</h3>
        {#await resultsPromise}
            Loading Scores
        {:then results} 
            <table class="tableStyle">
                <tr>
                    <th class="thStyle">Name</th>
                    <th class="thStyle">Points</th>
                    <th class="thStyle">Total Points</th>
                    <th class="thStyle">Total Yards</th>
                </tr>
                {#each results as result}
                    <tr>
                        <td class="tdStyle">
                            <a href={"/SuperBowl/"+$url(result.Name)}>
                                {result.Name}
                            </a>
                        </td>
                        <td class="tdStyle">{result.points}</td>
                        <td class="tdStyle">{result.tb1}</td>
                        <td class="tdStyle">{result.tb2}</td>
                    </tr>
                {/each}
            </table>
        {/await}
    </div>
    <br>
    <br>
    <Details>
        <span slot="summary">Props</span>
        <table class="tableStyle">
            <tr>
                <th class="thStyle">Prop</th>
                <th class="thStyle">Side One</th>
                <th class="thStyle">Side Two</th>
                <th class="thStyle">Points</th>
            </tr>
            {#each props as prop}
            <tr>
                <td class="tdStyle">{prop.bet}</td>
                <td class="tdStyle">{prop.sideOne}</td>
                <td class="tdStyle">{prop.sideTwo}</td>
                <td class="tdStyle">{prop.points}</td>
            </tr>
            {/each}
        </table>
    </Details>

    <Button on:click={event => modalOpen = true}>Add Your Picks</Button>

    <Modal bind:open={modalOpen} style="width:60%;background-color: #243447;padding:15px">
        <Field label="Your Name" gapless>
            <Input required bind:value={propSubmission["name"]}/>
        </Field>
        <VirtualList height="35vh" items={props} let:item>
            <Field label={item.bet +" Points: " + item.points}>
                <Radio value="A" bind:group={propSubmission.propPicks[item.bet]}> {item.sideOne} </Radio>
                <Radio value="B" bind:group={propSubmission.propPicks[item.bet]}> {item.sideTwo} </Radio>
            </Field>
        </VirtualList>
        <Field label="Tie Break 1: Total Points Scored">
            <Input required bind:value={propSubmission["tb1"]}/>
        </Field>
        <Field label="Tie Break 2: Total Cobined Yards">
            <Input bind:value={propSubmission["tb2"]}/>
        </Field>
        <Button error on:click={submitProps}>Submit</Button>
    </Modal>
</div>