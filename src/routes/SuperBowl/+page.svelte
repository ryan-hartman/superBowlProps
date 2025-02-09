<script>
	import { onMount } from 'svelte';
	import Modal from './Modal.svelte';
	import SubmitForm from './SubmitForm.svelte'
	import Submissions from './Submissions.svelte'

	import "../../app.css"
	let props = null;
	let showForm = false;
	let results = null;
	onMount(async () => {
		const response = await fetch('https://ryan-hartman.azurewebsites.net/api/GetProps');
		if (response.ok) {
			results = await response.json();
			props = results.Props
		} else {
			console.error('Failed to fetch results');
		}
	});

</script>

<svelte:head>
	<title>Home</title>
	<meta name="description" content="Svelte demo app" />
</svelte:head>

{#if props != null}
<section>
	<Submissions PropPicks={props}/>
	<div class="flex justify-center">
		<button 
		  class="my-4 px-10 py-4 rounded-md text-lg bg-indigo-500 text-white hover:bg-indigo-600 transition-colors shadow-lg"
		  on:click={() => showForm = !showForm}
		>
		  {showForm ? 'Hide Form' : 'Add Your Picks'}
		</button>
	  </div>
	{#if showForm}
		<SubmitForm visible={showForm} Props={props}/>
	{/if}
	<h1 class="text-3xl font-bold underline text-center">
		Prop Results
	</h1>
	<div class="props max-w-4xl mx-auto my-8">
		<table class="w-full text-sm text-left text-gray-700">
			<thead class="text-xs text-gray-700 uppercase bg-gray-200">
				<tr>
					<th scope="col" class="py-3 px-6">Prop</th>
					<th scope="col" class="py-3 px-6">Side One</th>
					<th scope="col" class="py-3 px-6">Side Two</th>
					<th scope="col" class="py-3 px-6 text-right">Points</th>
				</tr>
			</thead>
			<tbody>
				{#each props as prop}
					<tr class={`${prop.winningSide === "1" || prop.winningSide === "2" ? 'bg-gray-100' : 'bg-white'}`}>
						<td class="py-4 px-6 border-b border-gray-200">{prop.prop}</td>
						<td class={`py-4 px-6 border-b border-gray-200 ${prop.winningSide === "1" ? 'bg-green-200' : ''}`}>{prop.sideOne}</td>
						<td class={`py-4 px-6 border-b border-gray-200 ${prop.winningSide === "2" ? 'bg-green-200' : ''}`}>{prop.sideTwo}</td>
						<td class="py-4 px-6 border-b border-gray-200 text-right">{prop.points}</td>
					</tr>
				{/each}
			</tbody>
		</table>
	</div>
	

</section>
{/if}
<style>
	h1 {
		width: 100%;
	}
</style>
