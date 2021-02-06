<script>
	const links = [ ]
</script>

<nav>
	<div />
	<div>
		{#each links as [path, name]}
			<a href={path}>{name}</a>
		{/each}
	</div>

	<!-- <div>
		{#if $user}
			<img src={$user.picture} alt="profile - {$user.nickname}" />
			<a href="#signout" on:click={signout}>signout</a>
		{:else}
			<a href="/login">login</a>
		{/if}
	</div> -->
</nav>
