<script>
    import Tweet from "./Tweet.svelte";
    import HandleSearch from "./HandleSearch.svelte";  
    import AddHandle from "./AddHandle.svelte";
    import {userInfo} from "../../store";


    $: tweetsPromise = fetchTweets();
    export let handle = "QTRResearch"
    
    const fetchTweets = async () => {
        return await fetch(`https://tweetletter.azurewebsites.net/api/GetTweets?handle=`+handle)
        .then(r => r.json())
    };
    
	const updateHandle = async (event) => {
        handle = event.detail.handle
        updateTweets()
	}

    const updateTweets = async() => {
        tweetsPromise = await fetchTweets()
    }

    fetchTweets();

    // onMount(fetchTweets);

</script>

<style>
    .testing {
        margin:0 auto;
        text-align: center;
        display: flex;
        justify-content: center;
    }
</style>

<h1 >Tweets</h1>
<br>
<HandleSearch on:updatehandle={updateHandle}/>
<br>
{#await tweetsPromise}
    LoadingTweets
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
<AddHandle />