<script>
    import {Props} from "./2024Props.js"
    export let visible = false;
    let forms = Props
    let selections = {};
    let name = ''; // To store the name entered by the user
    $: allAnswered = forms.every((form, index) => selections[index] !== undefined) && name.trim() !== '';

    async function handleSubmit() {
        // Construct the payload
        const picks = Object.entries(selections).map(([index, value]) => ({
            ID: parseInt(index),
            BetSide: value + 1
        }));

        const payload = {
            Name: name,
            Picks: picks
        };
        console.log(payload)
        try {
            const response = await fetch('http://localhost:7299/api/SubmitPicks', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(payload)
            });

            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }

            // Handle response data if needed
            const data = await response.json();
            console.log('Success:', data);
        } catch (error) {
            console.error('Error during fetch:', error);
            alert(error.error);
        }
    }

</script>
{#if visible}
    <form on:submit|preventDefault={handleSubmit} class="bg-white shadow-lg rounded-lg p-4 space-y-4">
        <div class="space-y-4">
            {#each forms as form, index (form.prop)}
                <div class="flex flex-col gap-2 md:flex-row md:items-center md:justify-between">
                    <div class="md:flex-1">
                        <p class="text-gray-700 font-semibold">{form.prop}</p>
                    </div>
                    <div class="flex gap-2 md:flex-1 justify-center">
                        <label class={`block text-center px-4 py-2 text-sm rounded-lg cursor-pointer select-none ${selections[index] === 0 ? 'bg-indigo-500 text-white' : 'bg-gray-200 text-gray-800'} min-w-[140px] min-h-[40px]`}>
                            <input type="radio" class="sr-only" bind:group={selections[index]} value={0}>
                            {form.sideOne}
                        </label>
                        <label class={`block text-center px-4 py-2 text-sm rounded-lg cursor-pointer select-none ${selections[index] === 1 ? 'bg-indigo-500 text-white' : 'bg-gray-200 text-gray-800'} min-w-[140px] min-h-[40px]`}>
                            <input type="radio" class="sr-only" bind:group={selections[index]} value={1}>
                            {form.sideTwo}
                        </label>
                    </div>
                    <div class="text-gray-700 font-semibold md:flex-none">
                        <p>Points: {form.points}</p>
                    </div>
                </div>
            {/each}
        </div>
        <div class="flex gap-2 items-center mt-4">
            <input type="text" bind:value={name} placeholder="Your Name" class="flex-grow px-4 py-2 text-sm rounded-lg bg-gray-200 text-gray-800 border border-gray-300 focus:border-indigo-500 focus:ring focus:ring-indigo-500 focus:ring-opacity-50" />
            <button 
                type="submit" 
                class="px-6 py-2 rounded-lg text-sm 
                    {allAnswered ? 'bg-green-500 hover:bg-green-600 text-white focus:ring-4 focus:ring-green-500' : 'bg-gray-300 text-gray-500 cursor-not-allowed'}"
                disabled={!allAnswered}
            >
            Submit
        </button>
        </div>
    </form>
{/if}