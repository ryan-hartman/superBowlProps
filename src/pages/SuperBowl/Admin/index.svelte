<script>

    import { Field,Input,Button, Details, Modal, Radio } from 'svelte-chota'
    import VirtualList from '@sveltejs/svelte-virtual-list';
    import {Props} from "../../../data.js"
    let props = Props

    let propSubmission = {
        propPicks: {

        },
        name: "",
        tb1: 0,
        tb2: 0
    }

    let updateProps = () => {
        let updates = []
        props.forEach(prop => {
            if(propSubmission.propPicks[prop.bet] != "") {
                let update = {
                    Prop: prop.bet,
                    Result: propSubmission.propPicks[prop.bet],
                    Points: prop.points
                };
                updates.push(update)
            }
        });
        
        fetch(
            "https://tweetletter.azurewebsites.net/api/UpdatePropResults",
            {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(updates)
            }
        ).then(resp => {
            console.log("Updated")
        })
    }

</script>

<div>
    <VirtualList height="80vh" items={props} let:item>
        <Field label={item.bet +" Points: " + item.points}>
            <Radio value="A" bind:group={propSubmission.propPicks[item.bet]}> {item.sideOne} </Radio>
            <Radio value="B" bind:group={propSubmission.propPicks[item.bet]}> {item.sideTwo} </Radio>
        </Field>
    </VirtualList>
    <Button on:click={updateProps}>Update</Button>
</div>