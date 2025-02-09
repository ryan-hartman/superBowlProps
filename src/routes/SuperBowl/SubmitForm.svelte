<script>
    export let Props
    export let visible = false;
    let forms = Props
    let selections = {};
    let name = ''; // To store the name entered by the user
    let chiefsPoints;
    let ninersPoints;
    $: allAnswered = forms.every((form, index) => selections[index] !== undefined) && name.trim() !== '';

    async function handleSubmit() {
        // Construct the payload
        const picks = Object.entries(selections).map(([index, value]) => ({
            ID: parseInt(index),
            BetSide: value + 1
        }));

        const payload = {
            Name: name,
            ChiefsPoints: chiefsPoints,
            EaglesPoints: ninersPoints,
            Picks: picks
        };
        try {
            const response = await fetch('https://ryan-hartman.azurewebsites.net/api/SubmitPicks', {
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
            window.location.reload();
        } catch (error) {
            console.error('Error during fetch:', error);
            alert(error.error);
        }
    }

</script>
{#if visible}
<form on:submit|preventDefault={handleSubmit} class="bg-white shadow-lg rounded-lg p-4 space-y-4">
    <div class="space-y-4">
        <!-- Enhanced Form Headers -->
        <div class="hidden md:flex md:items-center md:justify-between font-semibold text-gray-700 bg-gray-100 rounded-md py-2">
            <div class="flex-1 text-left pl-2">
                <p>Prop</p>
            </div>
            <div class="flex flex-1 justify-center gap-2">
                <p class="text-center px-4 py-2 min-w-[140px]">Side One</p>
                <p class="text-center px-4 py-2 min-w-[140px]">Side Two</p>
            </div>
            <div class="flex-none text-center pr-2" style="min-width: 100px;"> <!-- Adjust the min-width as needed to align with your points input width -->
                <p>Points</p>
            </div>
        </div>
        <!-- Form Body -->
        {#each forms as form, index (form.prop)}
            <div class="flex flex-col md:flex-row md:items-center md:justify-between gap-2">
                <div class="flex-1">
                    <p class="text-gray-700 font-semibold">{form.prop}</p>
                </div>
                <div class="flex flex-1 justify-center gap-2">
                    <label class={`block text-center px-4 py-2 text-sm rounded-lg cursor-pointer select-none ${selections[index] === 0 ? 'bg-indigo-500 text-white' : 'bg-gray-200 text-gray-800'} min-w-[140px]`}>
                        <input type="radio" class="sr-only" bind:group={selections[index]} value={0}>
                        {form.sideOne}
                    </label>
                    <label class={`block text-center px-4 py-2 text-sm rounded-lg cursor-pointer select-none ${selections[index] === 1 ? 'bg-indigo-500 text-white' : 'bg-gray-200 text-gray-800'} min-w-[140px]`}>
                        <input type="radio" class="sr-only" bind:group={selections[index]} value={1}>
                        {form.sideTwo}
                    </label>
                </div>
                <div class="flex-none text-center" style="min-width: 100px;">
                    <span class="font-semibold text-gray-700 md:hidden">Points: </span>
                    <span class="text-gray-700 font-semibold">{form.points}</span>
                </div>
            </div>
        {/each}
    
    </div>
    <div class="flex flex-wrap gap-2 items-center mt-4">
        <input 
            type="text" 
            bind:value={name} 
            placeholder="Your Name" 
            class="flex-grow px-4 py-2 text-sm rounded-lg bg-gray-200 text-gray-800 border border-gray-300 focus:border-indigo-500 focus:ring focus:ring-indigo-500 focus:ring-opacity-50 my-2" 
        />
        <input 
            type="number" 
            bind:value={chiefsPoints} 
            placeholder="Chiefs Points Scored" 
            class="flex-grow px-2 py-2 text-sm rounded-lg bg-gray-200 text-gray-800 border border-gray-300 focus:border-indigo-500 focus:ring focus:ring-indigo-500 focus:ring-opacity-50 my-2" 
        />
        <input 
            type="number" 
            bind:value={ninersPoints} 
            placeholder="Eagles Points Scored" 
            class="flex-grow px-2 py-2 text-sm rounded-lg bg-gray-200 text-gray-800 border border-gray-300 focus:border-indigo-500 focus:ring focus:ring-indigo-500 focus:ring-opacity-50 my-2" 
        />
        <button 
            type="submit" 
            class="px-6 py-2 rounded-lg text-sm bg-green-500 hover:bg-green-600 text-white focus:ring-4 focus:ring-green-500 disabled:bg-gray-300 disabled:text-gray-500 cursor-pointer my-2"
            disabled={!allAnswered}
        >
        Submit
        </button>
    </div>
</form>


{/if}