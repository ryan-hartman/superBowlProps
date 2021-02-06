<script>
    import AddNewsletter from "./AddNewsletter.svelte";
    import EmailSearch from "./EmailSearch.svelte";
    import Newsletter from "./Newsletter.svelte"
    import {RingLoader} from 'svelte-loading-spinners'
    import VirtualList from '@sveltejs/svelte-virtual-list';
    import {isLoading, userInfo} from "../../store"
    import { Details } from 'svelte-chota';



    let getNewsletters = async function() {
        return await fetch(`https://tweetletter.azurewebsites.net/api/GetNewsletters?email=`+encodeURIComponent(email))
            .then(r => {
                let jsonVal = r.json();
                return jsonVal
            })
    }

    let email;
    let newslettersPromise

    let updateNewsletters = async function(e) {
        console.log(newslettersPromise)
        await new Promise(resolve => setTimeout(resolve, 150));
        newslettersPromise = getNewsletters()
    }



    $: if(!$isLoading) {
        email = $userInfo.email
        newslettersPromise = getNewsletters()
    }



</script>


<div class="newsletter">

    <h1>Newsletters</h1>
    {#if $isLoading}
        <div class="center">
            <RingLoader />
        </div>
    {:else}
    <!-- <EmailSearch on:updateemail = {updateEmail}/> -->
    <br>
    <div class="newsletterTable">
        {#await newslettersPromise}
        <div class="center">
            <RingLoader />
        </div>
        {:then newsletters}
            <div class="tableScroll" style="height:550px;overflow:auto">
                <table>
                    <tr>
                        <th>Title</th>
                        <th>Handles</th>
                    </tr>
                    {#if newsletters.length > 0}
                        {#each newsletters as newsletter}
                            <Newsletter {newsletter}/>
                        {/each}
                    {:else}
                        <tr>
                            <td>N/A</td>
                            <td>N/A</td>
                        </tr>
                    {/if}
                </table>
            </div>
        {/await}
    </div>
    <AddNewsletter on:addnewsletter = {updateNewsletters}/>
    {/if}
</div>