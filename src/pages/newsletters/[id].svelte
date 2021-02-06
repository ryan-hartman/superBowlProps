<script>

    import Tweet from "../tweets/Tweet.svelte"

    export let id;

    const fetchNewsletter = async () => {
        return await fetch(`https://tweetletter.azurewebsites.net/api/GetNewsletter?id=`+id)
        .then(r => r.json())
    };

    const newsletterPromise = fetchNewsletter();

</script>

<style>
    .testing {
        margin:0 auto;
        text-align: center;
        display: flex;
        justify-content: center;
    }
</style>

{#await newsletterPromise}
    Loading
{:then tweets} 
    <div id="tweets" class="testing">
        <ul style="list-style-type:none;">
            {#each tweets as tweet}
                    <li>
                        <Tweet {tweet} />
                    </li>
            {/each}
        </ul>
    </div>
{/await}