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
<section class="min-h-screen bg-slate-950 text-slate-100">
	<div class="relative overflow-hidden">
		<div class="absolute inset-0">
			<div class="absolute -top-24 left-1/2 h-72 w-72 -translate-x-1/2 rounded-full bg-indigo-500/30 blur-3xl"></div>
			<div class="absolute bottom-0 right-0 h-80 w-80 translate-x-1/3 rounded-full bg-emerald-400/20 blur-3xl"></div>
		</div>
		<div class="relative mx-auto max-w-6xl px-4 pb-12 pt-10">
			<div class="grid gap-8 lg:grid-cols-[1.25fr_0.75fr] lg:items-center">
				<div class="space-y-6">
					<span class="inline-flex items-center rounded-full border border-white/20 bg-white/10 px-4 py-1 text-xs uppercase tracking-[0.2em] text-white/80">
						Super Bowl Prop Central
					</span>
					<h1 class="text-4xl font-semibold tracking-tight sm:text-5xl">
						Patriots vs Seahawks picks, redesigned for game day.
					</h1>
					<p class="text-base text-slate-300 sm:text-lg">
						Lock in your prop picks, compare against the leaderboard, and keep tabs on the latest results with a layout that feels
						at home on every device.
					</p>
					<div class="flex flex-wrap gap-3">
						<button 
							class="rounded-full bg-indigo-500 px-6 py-3 text-sm font-semibold text-white shadow-lg shadow-indigo-500/30 transition hover:bg-indigo-400"
							on:click={() => showForm = !showForm}
						>
							{showForm ? 'Hide Picks Form' : 'Add Your Picks'}
						</button>
						<a href="#results" class="rounded-full border border-white/30 px-6 py-3 text-sm font-semibold text-white/90 transition hover:border-white/60">
							Jump to Results
						</a>
					</div>
				</div>
				<div class="rounded-3xl border border-white/10 bg-white/5 p-6 shadow-2xl shadow-black/40 backdrop-blur">
					<h2 class="text-lg font-semibold text-white">Quick Guide</h2>
					<ul class="mt-4 space-y-3 text-sm text-slate-200">
						<li class="flex items-start gap-3">
							<span class="mt-1 h-2 w-2 rounded-full bg-emerald-400"></span>
							Choose a side for every prop to unlock the submit button.
						</li>
						<li class="flex items-start gap-3">
							<span class="mt-1 h-2 w-2 rounded-full bg-indigo-400"></span>
							Enter Patriots and Seahawks scores for the tie-breaker.
						</li>
						<li class="flex items-start gap-3">
							<span class="mt-1 h-2 w-2 rounded-full bg-sky-400"></span>
							Track the leaderboard and see which picks hit.
						</li>
					</ul>
				</div>
			</div>
		</div>
	</div>
	<div class="bg-slate-50 text-slate-900">
		<div class="mx-auto max-w-6xl px-4 py-10">
			<Submissions PropPicks={props}/>
			{#if showForm}
				<div class="mt-8 rounded-2xl border border-slate-200 bg-white/90 p-6 shadow-xl shadow-slate-200/60">
					<SubmitForm visible={showForm} Props={props}/>
				</div>
			{/if}
			<div class="mt-10 flex justify-center">
				<button 
					class="rounded-full bg-slate-900 px-8 py-3 text-sm font-semibold text-white shadow-lg transition hover:bg-slate-800"
					on:click={() => showForm = !showForm}
				>
					{showForm ? 'Hide Picks Form' : 'Add Your Picks'}
				</button>
			</div>
			<div class="mt-12 text-center">
				<h2 id="results" class="text-3xl font-semibold tracking-tight text-slate-900">Prop Results</h2>
				<p class="mt-2 text-sm text-slate-600">Tap a prop to see the latest winning side.</p>
			</div>
			<div class="mt-6 hidden overflow-hidden rounded-2xl border border-slate-200 bg-white shadow-lg md:block">
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
			<div class="mt-6 grid gap-4 md:hidden">
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
	</div>
</section>
{/if}
