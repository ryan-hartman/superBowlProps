<script>
    import { Field,Input,Button } from 'svelte-chota';
    import { createEventDispatcher } from "svelte"

    const dispatch = createEventDispatcher()
    let handlesInput = ""
    let success = false
    let handleSubmitFunction = function(event) {
        success = false
        let handlesString = handlesInput
        let handles = handlesString.split(",")
        handles.forEach((element, index, array) => {
            array[index] = element.trim()
        })

        fetch(
            "https://tweetletter.azurewebsites.net/api/AddHandles",
            {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(handles)
            }
        ).then(success=true)
    }

</script>

<h2>Add Handles</h2>
<div>
    <Field grouped >
        <Input placeholder="RampCapitalLLC, QTRResearch, NortherlionLP..." bind:value={handlesInput}/>
        {#if success}
            <Button on:click={handleSubmitFunction} {success}>
                Success
            </Button>
        {:else}
            <Button on:click={handleSubmitFunction} >
                Add Handles
            </Button>
        {/if}
    </Field>
</div>