<!-- Modal.svelte -->
<script>
	export let propSheet;
  
	export function closeModal() {
	  // Emit an event to notify the parent component to close the modal
	  const event = new CustomEvent('close');
	  dispatchEvent(event);
	}
  </script>
  
  <div class="modal">
	<div class="modal-content">
	  <span class="close" on:click={closeModal}>&times;</span>
	  <h2>{propSheet.Name}</h2>
	  <ul>
		{#each propSheet.Picks as pick}
		  <li>Pick ID: {pick.ID}, BetSide: {pick.BetSide === 1 ? 'sideOne' : 'sideTwo'}</li>
		{/each}
	  </ul>
	</div>
  </div>
  
  <style>
	/* Styles for the modal */
  </style>
  