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
		const response = await fetch('http://localhost:7299/api/GetProps');
		if (response.ok) {
			results = await response.json();
			props = results.Props
			console.log(loadedProps)
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
	<button on:click={() => (showForm=!showForm)}> Show Pick Sheet</button>
	{#if showForm}
		<SubmitForm visible={showForm}/>
	{/if}
	<Submissions PropPicks={props}/>
	<h1 class="text-3xl font-bold underline">
		Props
	</h1>
	<div class="props">
		<table class="table-auto border-8">
			<tr>
				<th>Prop</th>
				<th>Side One</th>
				<th>Side Two</th>
				<th>Points</th>
			</tr>
			{#each props as prop}
				<tr>
					<td class="text-left pr-4">{prop.prop}</td>
					<td class="text-left pr-4">{prop.sideOne}</td>
					<td class="text-left pr-4">{prop.sideTwo}</td>
					<td class="text-right pr-4">{prop.points}</td>
				</tr>
			{/each}
		</table>
	</div>

</section>
{/if}
<style>
	/* th {
    	text-align: left;
		margin-right: 5%;
		border: 1px solid black;
	}
	table {
        border-collapse: collapse;
        border: 1px solid black;
    }
    td {
        border: 1px solid black;
    } */
	th {
		@apply text-left pr-4;
	}
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 0.6;
	}

	h1 {
		width: 100%;
	}
</style>
