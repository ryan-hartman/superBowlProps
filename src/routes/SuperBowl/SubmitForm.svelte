<script>
    export let Props
    export let visible = false;
    let forms = Props
    let selections = {};
    let name = ''; // To store the name entered by the user
    let chiefsPoints;
    let eaglesPoints;
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
            EaglesPoints: eaglesPoints,
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
<form on:submit|preventDefault={handleSubmit} class="space-y-6">
    <div class="rounded-2xl border border-slate-200 bg-slate-50/60 p-4 shadow-sm">
        <h3 class="text-lg font-semibold text-slate-800">Make your picks</h3>
        <p class="text-sm text-slate-500">Choose a side for every prop to enable the submit button.</p>
    </div>
    <div class="space-y-4">
        <!-- Enhanced Form Headers -->
        <div class="hidden md:flex md:items-center md:justify-between rounded-xl bg-slate-100 py-2 text-sm font-semibold text-slate-600">
            <div class="flex-1 text-left pl-3">
                <p>Prop</p>
            </div>
            <div class="flex flex-1 justify-center gap-2">
                <p class="min-w-[140px] px-4 py-2 text-center">Side One</p>
                <p class="min-w-[140px] px-4 py-2 text-center">Side Two</p>
            </div>
            <div class="flex-none text-center pr-2" style="min-width: 100px;"> <!-- Adjust the min-width as needed to align with your points input width -->
                <p>Points</p>
            </div>
        </div>
        <!-- Form Body -->
        {#each forms as form, index (form.prop)}
            <div class="flex flex-col gap-3 rounded-2xl border border-slate-200 bg-white p-4 md:flex-row md:items-center md:justify-between">
                <div class="flex-1">
                    <p class="font-semibold text-slate-800">{form.prop}</p>
                </div>
                <div class="flex flex-1 flex-col gap-2 sm:flex-row sm:justify-center">
                    <label class={`block min-w-[140px] cursor-pointer select-none rounded-full px-4 py-2 text-center text-sm ${selections[index] === 0 ? 'bg-indigo-500 text-white shadow-lg shadow-indigo-200' : 'bg-slate-100 text-slate-700'} `}>
                        <input type="radio" class="sr-only" bind:group={selections[index]} value={0}>
                        {form.sideOne}
                    </label>
                    <label class={`block min-w-[140px] cursor-pointer select-none rounded-full px-4 py-2 text-center text-sm ${selections[index] === 1 ? 'bg-indigo-500 text-white shadow-lg shadow-indigo-200' : 'bg-slate-100 text-slate-700'} `}>
                        <input type="radio" class="sr-only" bind:group={selections[index]} value={1}>
                        {form.sideTwo}
                    </label>
                </div>
                <div class="flex-none text-center text-sm" style="min-width: 100px;">
                    <span class="font-semibold text-slate-600 md:hidden">Points: </span>
                    <span class="font-semibold text-slate-800">{form.points}</span>
                </div>
            </div>
        {/each}
    
    </div>
    <div class="flex flex-wrap gap-3 items-center rounded-2xl border border-slate-200 bg-white p-4">
        <input 
            type="text" 
            bind:value={name} 
            placeholder="Your Name" 
            class="flex-grow rounded-full border border-slate-200 bg-slate-100 px-4 py-2 text-sm text-slate-800 focus:border-indigo-500 focus:ring focus:ring-indigo-500 focus:ring-opacity-50" 
        />
        <input 
            type="number" 
            bind:value={chiefsPoints} 
            placeholder="Patriots Points Scored" 
            class="flex-grow rounded-full border border-slate-200 bg-slate-100 px-4 py-2 text-sm text-slate-800 focus:border-indigo-500 focus:ring focus:ring-indigo-500 focus:ring-opacity-50" 
        />
        <input 
            type="number" 
            bind:value={eaglesPoints} 
            placeholder="Seahawks Points Scored" 
            class="flex-grow rounded-full border border-slate-200 bg-slate-100 px-4 py-2 text-sm text-slate-800 focus:border-indigo-500 focus:ring focus:ring-indigo-500 focus:ring-opacity-50" 
        />
        <button 
            type="submit" 
            class="rounded-full bg-emerald-500 px-6 py-2 text-sm font-semibold text-white shadow-lg shadow-emerald-200 transition hover:bg-emerald-600 focus:ring-4 focus:ring-emerald-500 disabled:cursor-not-allowed disabled:bg-slate-200 disabled:text-slate-400"
            disabled={!allAnswered}
        >
        Submit
        </button>
    </div>
</form>


{/if}
