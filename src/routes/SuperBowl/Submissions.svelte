<script>
    import { onMount } from 'svelte';
    import Modal from './Modal.svelte'
    
    export let PropPicks;
    
    let forms = PropPicks;
    console.log(forms)
    let submittedProps = [];
    let selectedId = null;
    let selectedPropSheet = null;

    onMount(async () => {
      const response = await fetch('https://ryan-hartman.azurewebsites.net/api/GetPicks');
      if (response.ok) {
        submittedProps = await response.json();
        console.log(submittedProps)
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
            return form.selection === 0 ? form.sideOne : form.sideTwo;
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

    function handleSubmit() {

    }

    function toggleDetails(id) {
      selectedId = selectedId === id ? null : id;
      selectedPropSheet = submittedProps[id];
    }
  </script>
  
  <div class="max-w-4xl mx-auto my-8 p-4 card-bg rounded-lg shadow-md">
    <h2 class="text-2xl font-bold text-center text-gray-700 mb-4">Leaderboard</h2>
    <form on:submit|preventDefault="{handleSubmit}">
      {#each submittedProps as prop (prop.id)}
      <div class="flex flex-col mb-4 rounded-lg hover:bg-gray-100 transition-colors">
        <div class="flex justify-between items-center p-3">
          <div class="flex-1">
            <p class="font-semibold text-gray-800">{prop.Name}</p>
          </div>
          <div class="flex items-center justify-center flex-1">
            <div class="text-lg font-bold text-indigo-600 mr-8">
              {prop.Score} Points
            </div>
          </div>
          <div class="flex-end">
            <button 
              class="px-4 py-2 rounded-lg text-sm font-medium bg-purple-700 text-white hover:bg-purple-800 transition-colors" 
              on:click={() => toggleDetails(prop.id)}
            >
              Picks
            </button>
          </div>
        </div>
          {#if prop.id === selectedId}
            <div class="p-3 bg-gray-50 rounded-b-lg">
                <div class="p-3 bg-gray-50 rounded-b-lg">
                    {#each prop.Picks as pick}
                    <div class="mb-4 p-3 rounded-lg transition-colors 
                        {pick.IsCorrect === 1 ? 'bg-green-100 border-l-4 border-green-600' : 
                         pick.IsCorrect === 2 ? 'bg-red-100 border-l-4 border-red-600' : 'bg-gray-100 border-l-4 border-gray-300'}">
                      <div class="flex justify-between items-center">
                        <div class="text-gray-800 font-semibold">
                          {getSelectedProp(pick.ID)}:
                        </div>
                        <div class="flex items-center">
                          <span class="{pick.IsCorrect === 1 ? 'text-green-600' : 
                                        pick.IsCorrect === 2 ? 'text-red-600' : 'text-gray-800'}">
                            {getSelectedValue(pick)}
                          </span>
                          <span class="ml-2 font-light text-sm
                              {pick.IsCorrect === 1 ? 'text-green-500' : 
                               pick.IsCorrect === 2 ? 'text-red-500' : 'text-gray-500'}">
                            ({PropPicks[pick.ID].points} pts)
                          </span>
                        </div>
                      </div>
                    </div>
                    {/each}
                  </div>
            </div>
          {/if}
        </div>
      {/each}
    </form>
  </div>
  
  <style>
    .card-bg {
        background-color: #e2e8f0; /* A cool light gray-blue */
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05); /* A lighter shadow for subtlety */
        border-radius: 0.5rem; /* Consistent rounded corners */
    }
  </style>