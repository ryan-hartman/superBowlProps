<script>
	import { onMount } from 'svelte';

	let props = null;
	let showForm = false;
	let results = null;
	onMount(async () => {
		const response = await fetch('https://ryan-hartman.azurewebsites.net/api/GetProps');
		if (response.ok) {
			results = await response.json();
			props = results.Props
			console.log(results)
		} else {
			console.error('Failed to fetch results');
		}
	});

    // Function to add a new prop row
    function addNewProp() {
    const newProp = {
      prop: '', // Start empty to allow user input
      sideOne: '', // Start empty to allow user input
      sideTwo: '', // Start empty to allow user input
      points: 0, // Default starting points, adjust as necessary
      winningSide: "0" // Indicates no winner selected yet
    };
    props = [...props, newProp];
  }

    // Function to update the winning side
    function setWinningSide(index, value) {
        props[index].winningSide = parseInt(value, 10);
    }

    // Function to submit changes
    async function handleSubmit() {
        console.log(props)
        const payload = {
            id: results.id.toString(),
            Props: props
        };
        const response = await fetch('https://ryan-hartman.azurewebsites.net/api/SetProps', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify(payload),
        });

        if (response.ok) {
        // Handle successful submission
        console.log('Props updated successfully');
        } else {
        // Handle error
        console.error('Failed to update props');
        }
    }

</script>

{#if props != null}
<div class="max-w-4xl mx-auto my-8">
    <table class="w-full text-sm text-left text-gray-700">
      <thead class="text-xs text-gray-700 uppercase bg-gray-200">
        <tr>
          <th class="py-3 px-6">Prop</th>
          <th class="py-3 px-6">Side One</th>
          <th class="py-3 px-6">Side Two</th>
          <th class="py-3 px-6">Winning Side</th>
          <th class="py-3 px-6 text-right">Points</th>
        </tr>
      </thead>
      <tbody>
        {#each props as prop, index}
          <tr class="{prop.winningSide === "1" ? 'bg-green-100' : prop.winningSide === "2" ? 'bg-red-100' : 'bg-white'} hover:bg-gray-200">
            <td class="py-4 px-6 border-b border-gray-200">
              <input type="text" bind:value={prop.prop} class="w-full text-sm px-2 py-1 rounded border-gray-300" />
            </td>
            <td class="py-4 px-6 border-b border-gray-200">
              <input type="text" bind:value={prop.sideOne} class="w-full text-sm px-2 py-1 rounded border-gray-300" />
            </td>
            <td class="py-4 px-6 border-b border-gray-200">
              <input type="text" bind:value={prop.sideTwo} class="w-full text-sm px-2 py-1 rounded border-gray-300" />
            </td>
            <td class="py-4 px-6 border-b border-gray-200">
              <select bind:value={prop.winningSide} class="text-sm px-2 py-1 rounded border-gray-300">
                <option value="0">None</option>
                <option value="1">Side One</option>
                <option value="2">Side Two</option>
              </select>
            </td>
            <td class="py-4 px-6 border-b border-gray-200 text-right">
              <input type="number" bind:value={prop.points} class="w-full text-sm px-2 py-1 rounded border-gray-300" />
            </td>
          </tr>
        {/each}
      </tbody>
    </table>
    <div class="mt-4">
      <button class="px-4 py-2 rounded-lg bg-blue-500 text-white hover:bg-blue-600" on:click="{addNewProp}">Add Prop</button>
      <button class="ml-2 px-4 py-2 rounded-lg bg-green-500 text-white hover:bg-green-600" on:click="{handleSubmit}">Submit Changes</button>
    </div>
  </div>
{/if}