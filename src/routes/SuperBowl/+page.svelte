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
<section class="min-h-screen bg-slate-50 text-slate-900">
	<div class="mx-auto flex max-w-6xl flex-col gap-6 px-4 py-10">
		<div class="flex flex-col gap-4 rounded-3xl border border-slate-200 bg-white p-6 shadow-lg shadow-slate-200/60 md:flex-row md:items-center md:justify-between">
			<div>
				<h1 class="text-3xl font-semibold tracking-tight text-slate-900 sm:text-4xl">Super Bowl Props</h1>
				<p class="mt-2 text-sm text-slate-600">Submit picks, see the leaderboard, and review results.</p>
			</div>
			<div class="flex flex-wrap gap-3">
				<button 
					class="rounded-full bg-slate-900 px-6 py-3 text-sm font-semibold text-white shadow-lg transition hover:bg-slate-800"
					on:click={() => showForm = !showForm}
				>
					{showForm ? 'Hide Picks Form' : 'Add Your Picks'}
				</button>
				<a href="#results" class="rounded-full border border-slate-300 px-6 py-3 text-sm font-semibold text-slate-700 transition hover:border-slate-400">
					Jump to Results
				</a>
			</div>
		</div>

		<Submissions PropPicks={props}/>
		{#if showForm}
			<div class="rounded-2xl border border-slate-200 bg-white/90 p-6 shadow-xl shadow-slate-200/60">
				<SubmitForm visible={showForm} Props={props}/>
			</div>
		{/if}
		<div class="flex justify-center">
			<button 
				class="rounded-full bg-slate-900 px-8 py-3 text-sm font-semibold text-white shadow-lg transition hover:bg-slate-800"
				on:click={() => showForm = !showForm}
			>
				{showForm ? 'Hide Picks Form' : 'Add Your Picks'}
			</button>
		</div>

		<div class="text-center">
			<h2 id="results" class="text-3xl font-semibold tracking-tight text-slate-900">Prop Results</h2>
			<p class="mt-2 text-sm text-slate-600">Tap a prop to see the latest winning side.</p>
		</div>
		<div class="hidden overflow-hidden rounded-2xl border border-slate-200 bg-white shadow-lg md:block">
			<table class="w-full text-sm text-left text-slate-700">
				<thead class="bg-slate-100 text-xs uppercase text-slate-600">
					<tr>
						<th scope="col" class="py-3 px-6">Prop</th>
						<th scope="col" class="py-3 px-6">Side One</th>
						<th scope="col" class="py-3 px-6">Side Two</th>
						<th scope="col" class="py-3 px-6 text-right">Points</th>
					</tr>
				</thead>
				<tbody>
					{#each props as prop}
						<tr class={`border-b border-slate-100 ${prop.winningSide === "1" || prop.winningSide === "2" ? 'bg-emerald-50/60' : 'bg-white'}`}>
							<td class="py-4 px-6 font-medium text-slate-900">{prop.prop}</td>
							<td class={`py-4 px-6 ${prop.winningSide === "1" ? 'text-emerald-700 font-semibold' : 'text-slate-700'}`}>{prop.sideOne}</td>
							<td class={`py-4 px-6 ${prop.winningSide === "2" ? 'text-emerald-700 font-semibold' : 'text-slate-700'}`}>{prop.sideTwo}</td>
							<td class="py-4 px-6 text-right font-semibold text-slate-900">{prop.points}</td>
						</tr>
					{/each}
				</tbody>
			</table>
		</div>
		<div class="grid gap-4 md:hidden">
			{#each props as prop}
				<div class={`rounded-2xl border p-4 shadow-sm ${prop.winningSide === "1" || prop.winningSide === "2" ? 'border-emerald-200 bg-emerald-50' : 'border-slate-200 bg-white'}`}>
					<div class="text-sm font-semibold text-slate-900">{prop.prop}</div>
					<div class="mt-4 grid gap-3 text-sm">
						<div class={`flex items-center justify-between rounded-lg px-3 py-2 ${prop.winningSide === "1" ? 'bg-emerald-100 text-emerald-800' : 'bg-slate-100 text-slate-700'}`}>
							<span class="font-medium">Side One</span>
							<span>{prop.sideOne}</span>
						</div>
						<div class={`flex items-center justify-between rounded-lg px-3 py-2 ${prop.winningSide === "2" ? 'bg-emerald-100 text-emerald-800' : 'bg-slate-100 text-slate-700'}`}>
							<span class="font-medium">Side Two</span>
							<span>{prop.sideTwo}</span>
						</div>
						<div class="flex items-center justify-between text-xs font-semibold uppercase tracking-wide text-slate-500">
							<span>Points</span>
							<span class="text-sm text-slate-900">{prop.points}</span>
						</div>
					</div>
				</div>
			{/each}
		</div>
	</div>
</section>
{/if}
