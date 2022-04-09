<script>
    import Tree from "./components/Tree.svelte";
    import {expandAll} from "./stores";
    import Number from "./components/widgets/Number.svelte";
    import {onMount} from "svelte";
    import String from "./components/widgets/String.svelte";
    import Boolean from "./components/widgets/Boolean.svelte";
    import Field from "./components/widgets/Field.svelte";
    import Wrapper from "./components/widgets/Wrapper.svelte";

    function keysToTree(keys) {
        let tree = {}
        for (let item of keys) {
            let t = tree
            for (let part of item.split("/")) {
                t = t[part] !== undefined ? t[part] : (t[part] = {})
            }
        }
        return tree
    }

    let path = ""
    let tree = {}
    let keys = []
    let selectedKeys = []
    let searchQuery = ""

    function refreshDashboard(query) {
        path = window.location.hash.slice(1);

        keys = []
        for (const key of NetworkTables.getKeys()) {
            if (query === "" || key.includes(query)) {
                keys.push(key)
            }
        }

        selectedKeys = keys.filter(e => e.startsWith(path))
        tree = keysToTree(keys)[""]
    }

    $: refreshDashboard(searchQuery)

    let isWsConnected = false;
    let robotConnection = "Waiting..."
    onMount(() => {
        NetworkTables.addWsConnectionListener(function (connected) {
            isWsConnected = connected
        }, true);
        NetworkTables.addWsConnectionListener(function (connected) {
            if (connected) {
                robotConnection = NetworkTables.getRobotAddress()
                setTimeout(() => {
                    refreshDashboard("")
                }, 500)
            } else {
                robotConnection = "Disconnected"
            }
        }, true);
    })
</script>

<svelte:window on:hashchange={() => refreshDashboard("")}/>

<main>
    <div class="tree">
        <input bind:value={searchQuery} placeholder="Search..." type="text">
        <Tree label="/" path="" values={tree}/>
        <p>
            WS: {isWsConnected ? "Connected" : "Disconnected"}
            <br>
            Robot: {robotConnection}
        </p>
    </div>

    <div class="view">
        <div class="top-bar">
            <h1>{path.endsWith("/") ? path : path + "/"}</h1>
            <div>
                <button on:click={() => refreshDashboard("")}>Refresh</button>
                <button on:click={() => {$expandAll = !$expandAll}}>{$expandAll ? "Collapse All" : "Expand All"}</button>
            </div>
        </div>
        <div class="widgets">
            {#each selectedKeys as key}
                {#if (typeof NetworkTables.getValue(key) === 'number')}
                    <Number label={key.replace(path + '/', '')} {key}/>
                {:else if (typeof NetworkTables.getValue(key) === 'string')}
                    <String label={key.replace(path + '/', '')} {key}/>
                {:else if (typeof NetworkTables.getValue(key) === 'boolean')}
                    <Boolean label={key.replace(path + '/', '')} {key}/>
                {:else if (typeof NetworkTables.getValue(key) === 'object')}
                    <Wrapper>
                        <span>{key.replace(path + '/', '')} ({JSON.stringify(NetworkTables.getValue(key))})</span>
                    </Wrapper>
                {:else}
                    <Wrapper>
                        <span>{key.replace(path + '/', '')} ({typeof NetworkTables.getValue(key)} not implemented)</span>
                    </Wrapper>
                {/if}
            {/each}
            <Field/>
        </div>
    </div>
</main>

<style>
    main {
        display: flex;
        flex-direction: row;
    }

    .tree {
        padding-top: 10px;
        padding-right: 10px;
        border-right: white 1px solid;
        max-height: 100vh;
        overflow-y: scroll;
    }

    .tree input {
        width: 100%;
        padding-right: 100px;
    }

    .view {
        width: 100%;
        padding-left: 1.2rem;
    }

    .widgets {
        width: 100%;
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
    }

    .top-bar {
        margin-top: 10px;
        margin-bottom: 10px;
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: space-between;
    }

    .top-bar button {
        margin-bottom: 0;
        margin-left: 3px;
        margin-right: 3px;
    }

    .top-bar h1 {
        margin-top: 0;
    }
</style>
