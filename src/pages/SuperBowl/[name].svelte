<script>
    import { Field,Input,Button, Details, Modal, Radio } from 'svelte-chota'
    import VirtualList from '@sveltejs/svelte-virtual-list';
    import {Props} from "../../data.js"
    import {isActive, url} from '@roxi/routify'

    export let name;

    let propSubmission = {
        propPicks: {

        },
        name: "",
        tb1: 0,
        tb2: 0
    }

    let getPicks = async () => {
        return fetch(
            "https://ryan-hartman.azurewebsites.net/api/SuperBowl/GetPropPicks?name="+encodeURIComponent(name)
            )
        .then(result => result.json())
        .then(data => propSubmission=data)
    }

    let picksPromise = getPicks()

</script>

<div>
    <a href={$url("/SuperBowl")}>Back</a>
    {#await picksPromise}
        Loading...
    {:then picks} 

        <Field label="Your Name" gapless>
            <Input readonly bind:value={propSubmission["name"]} />
        </Field>
        <VirtualList height="35vh" items={Props} let:item>
            <Field label={item.bet +" Points: " + item.points}>
                {#if propSubmission.propPicks[item.bet] == "A"}
                    <input type="radio" onclick="return false" checked> {item.sideOne}
                    <input type="radio" onclick="return false" > {item.sideTwo}
                {:else if propSubmission.propPicks[item.bet] == "B"}
                    <input type="radio" onclick="return false" unchecked> {item.sideOne}
                    <input type="radio" onclick="return false" checked> {item.sideTwo}
                {:else}
                    <input type="radio" onclick="return false" unchecked> {item.sideOne}
                    <input type="radio" onclick="return false" unchecked> {item.sideTwo}
                {/if}
                <!-- <Radio on:click={event => false} value="A" bind:group={propSubmission.propPicks[item.bet]}> {item.sideOne} </Radio>
                <Radio on:click={event => false} value="B" bind:group={propSubmission.propPicks[item.bet]}> {item.sideTwo} </Radio> -->
            </Field>
        </VirtualList>
        <Field label="Tie Break 1: Total Points Scored">
            <Input readonly bind:value={propSubmission["tb1"]}/>
        </Field>
        <Field label="Tie Break 2: Total Cobined Yards">
            <Input readonly bind:value={propSubmission["tb2"]}/>
        </Field>

    {/await}
</div>