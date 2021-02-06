<script>
    import { createEventDispatcher } from "svelte"
    import { Field,Input,Button, Details, Modal } from 'svelte-chota';
    import VirtualList from '@sveltejs/svelte-virtual-list';
    import {Rainbow} from 'svelte-loading-spinners'
    import {isLoading, userInfo} from "../../store"
    const dispatch = createEventDispatcher()

    let titleInput = ""
    let handleInput = ""

    let handles = []

    let end;
    let start;

    let modalOpen = false;

    let submitForm = async function(event) {
        let email = event.target.email.value
        let handlesString = event.target.handles.value
        let handles = handlesString.split(",")
        handles.forEach((element, index, array) => {
            array[index] = element.trim()
        })

        const newsletterObject = {
            Email:email,
            handles:handles
        }
        const testObj = {
                email: email
            }
        fetch(
            "https://tweetletter.azurewebsites.net/api/AddNewsletter",
            {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(newsletterObject)
            }
        ).then(dispatch("updateemail",testObj))

    }

    let addHandle = () => {
        let val = handleInput
        handles = [val, ...handles]
        handleInput = ""
    }

    let addNewsletter = () => {
        let email = $userInfo.email;
        console.log(email)
        let title = titleInput;

        const newsletterObject = {
            Email: email,
            Title: title,
            Handles: handles
        }

        fetch(
            "https://tweetletter.azurewebsites.net/api/AddNewsletter",
            // "http://localhost:7071/api/AddNewsletter",
            {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(newsletterObject)
            }
        ).then(resp => {
            titleInput = "";
            handles = [];
            handleInput = "";
            modalOpen = false;
            dispatch("addnewsletter")
        })
    }

    let onKeyDown = (event) => {
        if (event.key === 'Enter') {
            addHandle()
		}
    }
</script>

<div>

    {#if $isLoading}
        <Rainbow />
    {:else}
        <Button on:click={event => modalOpen = true}>
            <h2>
                Add Newsletter
            </h2>
        </Button>
        <Modal bind:open={modalOpen} style="width:60%;background-color: #243447;padding:15px">
            <span slot="summary">Add Newsletter</span>
            <h2>Add Newsletter</h2>
            <Field label = "Newsletter Name">
                <Input placeholder={$userInfo.given_name + "'s Newsletter"} bind:value={titleInput}/>
            </Field>
            <h3> Handles Included</h3>
            <VirtualList height="250px" items={handles} let:item bind:start bind:end>
                <!-- this will be rendered for each currently visible item -->
                <p>{item}</p>
            </VirtualList>
            <Field label = "Handles" gapless> 
                <Input on:keydown={onKeyDown} placeholder="twitterHandle" bind:value={handleInput}/>
                <Button on:click={addHandle}> Add Handle</Button>
            </Field>
            <Button on:click={addNewsletter} > Add Newsletter</Button>
        </Modal>
    {/if}

</div>