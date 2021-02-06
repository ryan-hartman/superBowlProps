<script>
	import Navigation from './_navigation.svelte'
    import { createAuth } from '../auth';
    import {authInitialized} from "../store"

	if(!$authInitialized) {
		const config = {
			domain: "tweetletter.us.auth0.com",
			client_id: AUTH0
    	};

		const {
			login,
			logout,
		} = createAuth(config);
	}

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
