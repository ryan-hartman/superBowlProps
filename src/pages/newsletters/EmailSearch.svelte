<script>
    let emailInput = ""
    import { createEventDispatcher } from "svelte"
    const dispatch = createEventDispatcher()

    function handleKeydown(event) {
		if (event.key === 'Enter') {
			const text = event.target.value;
            if (!text) return;

            const testObj = {
                email: text
            }

            console.log(testObj)

            dispatch("updateemail", testObj)
		}
    }
    
    function handleClick(event) {
        const testObj = {
                email: emailInput
            }
        dispatch("updateemail", testObj)
    }

</script>

<input on:keydown={handleKeydown} bind:value={emailInput} placeholder="example@example.com"/>
<button class="btn-primary" on:click={handleClick}>Search</button>
