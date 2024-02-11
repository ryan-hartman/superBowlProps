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
      const response = await fetch('http://localhost:7299/api/GetPicks');
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
  
  <div class="bg-gradient-to-br from-gray-100 to-gray-200 shadow-lg rounded-lg p-4 space-y-4 max-w-4xl mx-auto">
    <form on:submit|preventDefault="{handleSubmit}">
      {#each submittedProps as prop (prop.id)}
        <div class="flex flex-col mb-2 rounded-lg hover:bg-gray-300 transition-colors" style="min-width: 320px; width: 100%;">
          <div class="p-3">
            <div class="flex justify-between items-center">
              <div class="flex-1">
                <p class="font-bold text-gray-800 text-lg">{prop.Name}</p>
              </div>
              <div class="flex-initial mx-4">
                <p class="font-bold text-gray-800 text-lg">{prop.Score}</p>
              </div>
              <div class="flex gap-2">
                <button 
                  class="px-4 py-2 rounded-lg text-sm font-medium bg-purple-700 text-white hover:bg-purple-800 transition-colors" 
                  on:click={() => toggleDetails(prop.id)}
                >
                  Picks
                </button>
              </div>
            </div>
          </div>
          {#if prop.id === selectedId}
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
          {/if}
        </div>
      {/each}
    </form>
  </div>
  
  
  