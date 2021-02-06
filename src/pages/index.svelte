<script>
	import { metatags } from '@roxi/routify'
	metatags.title = 'TweetLetter'
	metatags.description = 'The tweets you want to see delivered to your inbox'

    import { createAuth } from '../auth';
    import {isLoading, isAuthenticated, authToken, userInfo, authError} from "../store"

    const config = {
        domain: "tweetletter.us.auth0.com",
        client_id: AUTH0
    };

    const {
        login,
		logout,
    } = createAuth(config);
    
    $: disabled = !$isAuthenticated;
</script>

<div class="center-all">
	<div>
		{#if $isLoading}
		  	<p>Loading ...</p>
		{:else if $authError}
		  	<p>Got error: {$authError.message}</p>
		{:else if !$isAuthenticated}
		  	<button on:click={() => login()}>Login</button>
		{:else}
			<h1>Hi {$userInfo.name}</h1>	
		  	<button on:click={() => logout()}>Logout</button>
		{/if}
	  </div>
</div>
