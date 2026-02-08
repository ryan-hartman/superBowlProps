<script>
	import { onMount } from 'svelte';
	import Modal from './Modal.svelte';
	import SubmitForm from './SubmitForm.svelte'
	import Submissions from './Submissions.svelte'

	import "../../app.css"
	let props = null;
	let showForm = false;
	let results = null;
	let submittedProps = [];
	let propStats = [];
	const submissionsOpen = false;
	onMount(async () => {
		const response = await fetch('https://ryan-hartman.azurewebsites.net/api/GetProps');
		if (response.ok) {
			results = await response.json();
			props = results.Props
		} else {
			console.error('Failed to fetch results');
		}

		const picksResponse = await fetch('https://ryan-hartman.azurewebsites.net/api/GetPicks');
		if (picksResponse.ok) {
			submittedProps = await picksResponse.json();
		} else {
			console.error('Failed to fetch submitted props');
		}
	});

	function getPercentage(count, total) {
		if (!total) return 0;
		return Math.round((count / total) * 100);
	}

	$: if (props && props.length) {
		const stats = props.map(() => ({ sideOne: 0, sideTwo: 0, total: 0 }));
		for (const sheet of submittedProps) {
			if (!sheet?.Picks) continue;
			for (const pick of sheet.Picks) {
				const stat = stats[pick.ID];
				if (!stat) continue;
				stat.total += 1;
				if (pick.BetSide === 1) stat.sideOne += 1;
				if (pick.BetSide === 2) stat.sideTwo += 1;
			}
		}
		propStats = stats;
	} else {
		propStats = [];
	}

</script>

<svelte:head>
	<title>Home</title>
	<meta name="description" content="Svelte demo app" />
</svelte:head>

{#if props != null}
<section class="min-h-screen bg-slate-50 text-slate-900">
	<div class="mx-auto flex max-w-6xl flex-col gap-6 px-4 py-10">
		<div class="flex flex-col gap-4 rounded-3xl border border-slate-200 bg-white p-5 shadow-lg shadow-slate-200/60 md:flex-row md:items-center md:justify-between">
			<h1 class="text-3xl font-semibold tracking-tight text-slate-900 sm:text-4xl">Super Bowl Props</h1>
			<div class="flex flex-wrap gap-3">
				<button 
					class={`rounded-full px-6 py-3 text-sm font-semibold shadow-lg transition ${submissionsOpen ? 'bg-slate-900 text-white hover:bg-slate-800' : 'cursor-not-allowed bg-slate-200 text-slate-400'}`}
					on:click={() => {
						if (submissionsOpen) showForm = !showForm;
					}}
					disabled={!submissionsOpen}
				>
					{submissionsOpen ? (showForm ? 'Hide Picks Form' : 'Add Your Picks') : 'Picks Closed'}
				</button>
				<a href="#results" class="rounded-full border border-slate-300 px-6 py-3 text-sm font-semibold text-slate-700 transition hover:border-slate-400">
					Jump to Results
				</a>
			</div>
		</div>

		<div class="grid gap-6 lg:grid-cols-[minmax(0,1fr)_minmax(0,1.1fr)]">
			<div class="flex flex-col gap-6">
				{#if showForm && submissionsOpen}
					<section class="rounded-3xl border border-slate-200 bg-white/90 p-6 shadow-xl shadow-slate-200/60">
						<SubmitForm visible={showForm} Props={props} submissionsOpen={submissionsOpen}/>
					</section>
				{/if}

				<section class="rounded-3xl border border-slate-200 bg-white p-5 shadow-lg shadow-slate-200/60">
					<div class="flex items-center justify-between">
						<h2 class="text-lg font-semibold text-slate-900">Leaderboard</h2>
					</div>
					<div class="mt-4">
						<Submissions PropPicks={props}/>
					</div>
				</section>
			</div>

			<section class="rounded-3xl border border-slate-200 bg-white p-5 shadow-lg shadow-slate-200/60">
				<div class="flex items-center justify-between">
					<h2 id="results" class="text-lg font-semibold text-slate-900">Prop Results</h2>
				</div>
				<div class="mt-4 hidden overflow-hidden rounded-2xl border border-slate-200 bg-white md:block">
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
							{#each props as prop, index}
								<tr class={`border-b border-slate-100 ${prop.winningSide === "1" || prop.winningSide === "2" ? 'bg-slate-100/80' : 'bg-white'}`}>
									<td class="py-4 px-6 font-medium text-slate-900">{prop.prop}</td>
									<td class={`py-4 px-6 ${prop.winningSide === "1" ? 'bg-emerald-100 text-emerald-900 font-semibold ring-1 ring-inset ring-emerald-300/70' : 'text-slate-700'}`}>
										<div>{prop.sideOne}</div>
										{#if propStats.length}
											<div class="mt-1 text-xs font-medium text-slate-500 whitespace-nowrap">
												{getPercentage(propStats[index]?.sideOne, propStats[index]?.total)}% Picked
											</div>
											<div class="mt-1 h-1.5 w-full overflow-hidden rounded-full bg-slate-200">
												<div
													class="h-1.5 rounded-full bg-gradient-to-r from-emerald-300 to-emerald-500"
													style={`width: ${getPercentage(propStats[index]?.sideOne, propStats[index]?.total)}%`}
												></div>
											</div>
										{/if}
									</td>
									<td class={`py-4 px-6 ${prop.winningSide === "2" ? 'bg-emerald-100 text-emerald-900 font-semibold ring-1 ring-inset ring-emerald-300/70' : 'text-slate-700'}`}>
										<div>{prop.sideTwo}</div>
										{#if propStats.length}
											<div class="mt-1 text-xs font-medium text-slate-500 whitespace-nowrap">
												{getPercentage(propStats[index]?.sideTwo, propStats[index]?.total)}% Picked
											</div>
											<div class="mt-1 h-1.5 w-full overflow-hidden rounded-full bg-slate-200">
												<div
													class="h-1.5 rounded-full bg-gradient-to-r from-indigo-300 to-indigo-500"
													style={`width: ${getPercentage(propStats[index]?.sideTwo, propStats[index]?.total)}%`}
												></div>
											</div>
										{/if}
									</td>
									<td class="py-4 px-6 text-right font-semibold text-slate-900">{prop.points}</td>
								</tr>
							{/each}
						</tbody>
					</table>
				</div>
				<div class="mt-4 grid gap-4 md:hidden">
					{#each props as prop, index}
						<div class={`rounded-2xl border p-4 shadow-sm ${prop.winningSide === "1" || prop.winningSide === "2" ? 'border-slate-200 bg-slate-50' : 'border-slate-200 bg-white'}`}>
							<div class="text-sm font-semibold text-slate-900">{prop.prop}</div>
							<div class="mt-4 grid gap-3 text-sm">
								<div class={`rounded-lg px-3 py-2 ${prop.winningSide === "1" ? 'bg-emerald-200 text-emerald-900 ring-1 ring-inset ring-emerald-300/80' : 'bg-slate-100 text-slate-700'}`}>
									<div class="flex items-center justify-between">
										<span class="font-medium">Side One</span>
										<span>{prop.sideOne}</span>
									</div>
									{#if propStats.length}
										<div class="mt-1 text-[11px] font-semibold text-slate-600">
											{getPercentage(propStats[index]?.sideOne, propStats[index]?.total)}% Picked
										</div>
										<div class="mt-1 h-1.5 w-full overflow-hidden rounded-full bg-white/70">
											<div
												class="h-1.5 rounded-full bg-gradient-to-r from-emerald-300 to-emerald-500"
												style={`width: ${getPercentage(propStats[index]?.sideOne, propStats[index]?.total)}%`}
											></div>
										</div>
									{/if}
								</div>
								<div class={`rounded-lg px-3 py-2 ${prop.winningSide === "2" ? 'bg-emerald-200 text-emerald-900 ring-1 ring-inset ring-emerald-300/80' : 'bg-slate-100 text-slate-700'}`}>
									<div class="flex items-center justify-between">
										<span class="font-medium">Side Two</span>
										<span>{prop.sideTwo}</span>
									</div>
									{#if propStats.length}
										<div class="mt-1 text-[11px] font-semibold text-slate-600">
											{getPercentage(propStats[index]?.sideTwo, propStats[index]?.total)}% Picked
										</div>
										<div class="mt-1 h-1.5 w-full overflow-hidden rounded-full bg-white/70">
											<div
												class="h-1.5 rounded-full bg-gradient-to-r from-indigo-300 to-indigo-500"
												style={`width: ${getPercentage(propStats[index]?.sideTwo, propStats[index]?.total)}%`}
											></div>
										</div>
									{/if}
								</div>
								<div class="flex items-center justify-between text-xs font-semibold uppercase tracking-wide text-slate-500">
									<span>Points</span>
									<span class="text-sm text-slate-900">{prop.points}</span>
								</div>
							</div>
						</div>
					{/each}
				</div>
			</section>
		</div>
	</div>
</section>
{/if}
