<script>
    import { Card, CardBody, CardHeader, Input, Table } from '@sveltestrap/sveltestrap';
    import InPlaceEdit from '$lib/common/InPlaceEdit.svelte'
    import { format } from '$lib/helpers/datetime';
	import { onMount } from 'svelte';
	import { getAgentTools } from '$lib/services/agent-service';

    /** @type {import('$types').AgentModel} */
    export let agent;

    /** @type {string[]} */
    export let profiles = [];

    /** @type {string[]} */
    export let tools = [];

    /** @type {string[]} */
    let toolOptions = [];

    const profileLimit = 10;
    const toolLimit = 10;

    onMount(() => {
        getAgentTools().then(data => {
            const tools = data?.filter(x => x?.trim()?.length > 0) || [];
            toolOptions = ["", ...tools];
        });
    });

    function addProfile() {
        if (!!!agent) return;

        profiles = [...profiles, ''];
        agent.profiles = profiles;
    }

    /**
	 * @param {number} index
	 */
    function removeProfile(index) {
        profiles = profiles.filter((x, idx) => idx !== index);
        agent.profiles = profiles;
    }

    function addTool() {
        if (!!!agent) return;

        tools = [...tools, ''];
        agent.tools = tools;
    }

    /**
	 * @param {number} index
	 */
    function removeTool(index) {
        tools = tools.filter((x, idx) => idx !== index);
        agent.tools = tools;
    }

    function chatWithAgent() {
        if (!!!agent?.id) return;
        
        window.open(`/chat/${agent?.id}`, '_blank');
    }
</script>

<Card>
    <CardHeader>
        <div class="text-center">
            <img
                src="images/users/bot.png"
                alt=""
                height="50"
                class="mx-auto d-block"
                style="cursor: pointer;"
                tabindex="-1"
                role=''
                on:keydown={() => {}}
                on:click={() => chatWithAgent()}
            />
            <h5 class="mt-1 mb-1"><InPlaceEdit bind:value={agent.name}/></h5>
            <p class="text-muted mb-0">Updated at {format(agent.updated_datetime, 'time')}</p>
        </div>
    </CardHeader>
    <CardBody>
        <div class="table-responsive">
            <Table >
                <tbody>
                    <tr>
                        <th class="agent-prop-key">Type</th>
                        <td>
                            {#if agent.is_router}
                                Routing Agent
                            {:else}
                                Task Agent
                            {/if}
                        </td>
                    </tr>
                    <tr>
                        <th class="agent-prop-key">Visibility</th>
                        <td>
                            <div class="form-check mb-3">
                                <input class="form-check-input" type="checkbox" bind:checked={agent.is_public} id="is_public" />
                                <label class="form-check-label" for="is_public"> Public </label>
                            </div>
                        </td>
                    </tr>
                    <tr>
                        <th class="agent-prop-key">Routable</th>
                        <td>
                            <div class="form-check mb-3">
                                <input class="form-check-input" type="checkbox" bind:checked={agent.allow_routing} id="allow_routing" />
                                <label class="form-check-label" for="allow_routing">Allow</label>
                            </div>
                        </td>
                    </tr>
                    <tr>
                        <th class="agent-prop-key">Profiles</th>
                        <td>
                            <div class="agent-prop-list-container">
                                {#each profiles as profile, index}
                                <div class="edit-wrapper">
                                    <input
                                        class="form-control edit-text-box"
                                        type="text"
                                        placeholder="Typing here..."
                                        maxlength={30}
                                        bind:value={profile}
                                    />
                                    <div class="delete-icon">
                                        <i
                                            class="bx bxs-no-entry"
                                            role="link"
                                            tabindex="0"
                                            on:keydown={() => {}}
                                            on:click={() => removeProfile(index)}
                                        />
                                    </div>
                                </div>
                                {/each}
                                {#if profiles?.length < profileLimit}
                                <div class="list-add">
                                    <i
                                        class="bx bx bx-list-plus"
                                        role="link"
                                        tabindex="0"
                                        on:keydown={() => {}}
                                        on:click={() => addProfile()}
                                    />
                                </div>
                                {/if}
                            </div>
                        </td>
                    </tr>
                    <tr>
                        <th class="agent-prop-key">Tools</th>
                        <td>
                            <div class="agent-prop-list-container">
                                {#each tools as tool, index}
                                <div class="edit-wrapper">
                                    <Input type="select" class="edit-text-box" bind:value={tool}>
                                        {#each toolOptions as option}
                                            <option selected={tool === option}>{option}</option>
                                        {/each}
                                    </Input>
                                    <div class="delete-icon">
                                        <i
                                            class="bx bxs-no-entry"
                                            role="link"
                                            tabindex="0"
                                            on:keydown={() => {}}
                                            on:click={() => removeTool(index)}
                                        />
                                    </div>
                                </div>
                                {/each}
                                {#if tools?.length < toolLimit}
                                <div class="list-add">
                                    <i
                                        class="bx bx bx-list-plus"
                                        role="link"
                                        tabindex="0"
                                        on:keydown={() => {}}
                                        on:click={() => addTool()}
                                    />
                                </div>
                                {/if}
                            </div>
                        </td>
                    </tr>
                    <tr>
                        <th class="agent-prop-key">Status</th>
                        <td>							
                            <div class="form-check mb-3">
                                <input class="form-check-input" type="checkbox" bind:checked={agent.disabled} id="disabled" />
                                <label class="form-check-label" for="disabled">Disabled</label>
                            </div>
                        </td>
                    </tr>
                    <tr>
                        <th class="agent-prop-key">Created Date</th>
                        <td>{format(agent.created_datetime, 'time')}</td>
                    </tr>
                </tbody>
            </Table>
        </div>
    </CardBody>
</Card>