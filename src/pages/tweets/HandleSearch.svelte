<script>

    import { createEventDispatcher } from "svelte"
    import { Field,Input,Button } from 'svelte-chota';
    import { mdiMagnify } from '@mdi/js';

    const dispatch = createEventDispatcher()
    var handleInput = "";
    function handleKeydown(event) {
		if (event.key === 'Enter') {
			const text = event.target.value;
            if (!text) return;

            const testObj = {
                handle: text
            }

            console.log(testObj)

            dispatch("updatehandle", testObj)
		}
    }
    
    let handleSubmitFunction = function(event) {
		if (event.key === 'Enter') {
			const text = event.target.value;
            if (!text) return;

            const testObj = {
                handle: text
            }

            console.log(testObj)

            dispatch("updatehandle", testObj)
		}
    }

    function handleClick(event) {
        const testObj = {
                handle: handleInput
            }
        console.log(testObj)
        dispatch("updatehandle", testObj)
    }

</script>


<Field gapless >
    <Input on:keydown={handleSubmitFunction} placeholder="QTRResearch" bind:value={handleInput}/>
    <Button on:click={handleClick} icon={mdiMagnify} primary/>
</Field>

<!-- <input on:keydown={handleKeydown} bind:value={handleInput} placeholder = "QTRResearch"/>
<button class="btn-primary" on:click={handleClick}>Submit</button> -->