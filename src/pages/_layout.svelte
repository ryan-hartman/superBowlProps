<script>
	import Navigation from './_navigation.svelte'

</script>

<style>
	.containerStyle {
		max-width:850px;
		margin: auto;
		margin-top: 2%;
		padding-left: 2%;
		padding-right: 2%;
		border: 3px solid #96aad6;
	}
</style>

<div class="app">
	<header>
		<Navigation />
	</header>

	<main>
		<div class="containerStyle">
			<slot />

		</div>
	</main>

	<!-- <footer>
		TweetLetter a Ryan Hartman Production
	</footer> -->
</div>
