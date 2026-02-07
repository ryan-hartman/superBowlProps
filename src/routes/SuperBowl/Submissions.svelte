<script>
    import { onMount } from 'svelte';
    import Modal from './Modal.svelte'
    
    export let PropPicks;
    
    let forms = PropPicks;
    let submittedProps = [];
    let selectedId = null;
    let selectedPropSheet = null;

    onMount(async () => {
      const response = await fetch('https://ryan-hartman.azurewebsites.net/api/GetPicks');
      if (response.ok) {
        submittedProps = await response.json();
      } else {
        console.error('Failed to fetch submitted props');
      }
    });

  
    function toggleModal(propSheet) {
        selectedPropSheet = propSheet;
    }

    function closeModal() {
        selectedPropSheet = null;
    }

    function getSelectedValue(pick) {
        const form = forms[pick.ID];
        if (form) {
            return pick.BetSide === 1 ? form.sideOne : form.sideTwo;
        }
        return null;
    }

    function getSelectedProp(id) {
        const form = forms[id];
        if (form) {
            return form.prop;
        }
        return null;
    }

    function getTeamPoints(propSheet, teamIndex) {
      if (!propSheet) {
        return null;
      }

      const directPoints = [propSheet.ChiefsPoints, propSheet.EaglesPoints].filter(
        (value) => value !== undefined && value !== null
      );
      if (directPoints.length === 2) {
        return directPoints[teamIndex];
      }

      const pointKeys = Object.keys(propSheet).filter(
        (key) => key.endsWith('Points') && key !== 'Points'
      );
      return pointKeys[teamIndex] ? propSheet[pointKeys[teamIndex]] : null;
    }

    function handleSubmit() {

    }

    function toggleDetails(id) {
      selectedId = selectedId === id ? null : id;
      selectedPropSheet = submittedProps[id];
    }
  </script>
  

  <div class="max-w-5xl mx-auto my-8 p-6 rounded-3xl border border-white/20 bg-white/80 shadow-xl shadow-slate-200/80 backdrop-blur">
    <div class="flex flex-col gap-2 text-center">
      <h2 class="text-2xl font-semibold text-slate-800 sm:text-3xl">Picks & Scores</h2>
      <p class="text-sm text-slate-500">Tap a player to reveal their prop sheet.</p>
    </div>
    <form on:submit|preventDefault="{handleSubmit}">
      {#each submittedProps as prop (prop.id)}
      <div class="mt-6 flex flex-col rounded-2xl border border-slate-200 bg-white/90 shadow-sm transition hover:-translate-y-0.5 hover:shadow-md">
        <div class="flex flex-col gap-3 p-4 sm:flex-row sm:items-center sm:justify-between">
          <div class="flex-1">
            <p class="text-lg font-semibold text-slate-800">{prop.Name}</p>
          </div>
          <div class="flex items-center justify-center sm:flex-1">
            <div class="rounded-full bg-indigo-50 px-4 py-2 text-sm font-semibold text-indigo-600">
              {prop.Score} Points
            </div>
          </div>
          <div class="flex-end">
            <button 
              class="rounded-full bg-slate-900 px-4 py-2 text-xs font-semibold uppercase tracking-wide text-white transition hover:bg-slate-800" 
              on:click={() => toggleDetails(prop.id)}
            >
              Picks
            </button>
          </div>
        </div>
          {#if prop.id === selectedId}
            <div class="rounded-b-2xl bg-slate-50 px-4 pb-4">
                <div class="pt-2">
                    {#each prop.Picks as pick}
                    <div class="mb-3 rounded-xl border border-slate-200 bg-white p-3 transition-colors 
                        {pick.IsCorrect === 1 ? 'border-emerald-200 bg-emerald-50' : 
                         pick.IsCorrect === 2 ? 'border-rose-200 bg-rose-50' : 'border-slate-200 bg-white'}">
                      <div class="flex flex-col gap-2 sm:flex-row sm:items-center sm:justify-between">
                        <div class="text-sm font-semibold text-slate-800">
                          {getSelectedProp(pick.ID)}
                        </div>
                        <div class="flex items-center gap-2">
                          <span class="text-sm font-medium {pick.IsCorrect === 1 ? 'text-emerald-600' : 
                                        pick.IsCorrect === 2 ? 'text-rose-600' : 'text-slate-700'}">
                            {getSelectedValue(pick)}
                          </span>
                          <span class="text-xs font-semibold uppercase tracking-wide
                              {pick.IsCorrect === 1 ? 'text-emerald-500' : 
                               pick.IsCorrect === 2 ? 'text-rose-500' : 'text-slate-500'}">
                            ({PropPicks[pick.ID].points} pts)
                          </span>
                        </div>
                      </div>
                    </div>
                    {/each}
                  </div>
                  <div class="mt-4 grid gap-3 rounded-2xl border border-slate-200 bg-white p-4">
                    <div class="flex items-center justify-between text-sm font-semibold text-slate-700">
                        <span>Patriots Score</span>
                        <span class="text-slate-900">{getTeamPoints(prop, 0)}</span>
                    </div>
                    <div class="flex items-center justify-between text-sm font-semibold text-slate-700">
                        <span>Seahawks Score</span>
                        <span class="text-slate-900">{getTeamPoints(prop, 1)}</span>
                    </div>
                  </div>
                
            </div>
          {/if}
        </div>
      {/each}
    </form>
  </div>
