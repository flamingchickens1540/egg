<script>
    import Tree from "./components/Tree.svelte";
    import {expandAll} from "./stores";
    import Number from "./components/widgets/Number.svelte";
    import {onMount} from "svelte";
    import Select from "./components/widgets/Select.svelte";

    let exampleKeys = [
        "SmartDashboard/shooter/tuning/frontRPM",
        "SmartDashboard/shooter/tuning/rearRPM",
        "SmartDashboard/indexer/sensors/front/beamBreak",
        "SmartDashboard/indexer/sensors/rear/beamBreak",
        "SmartDashboard/indexer/sensors/stagedForShooting",
        "SmartDashboard/intake/sensors/frontRPM",
        "SmartDashboard/intake/sensors/rearRPM",
    ];

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

    function refreshDashboard() {
        path = window.location.hash.slice(1);

        keys = []
        for (const key of NetworkTables.getKeys()) {
            if (key.startsWith("/SmartDashboard")) {
                keys.push(key.slice(1))
            }
        }

        console.log(keys)
        selectedKeys = keys.filter(e => e.startsWith(path))
        tree = keysToTree(keys)
    }

    let isWsConnected = false;
    let robotConnection = "Waiting..."
    onMount(() => {
        NetworkTables.addWsConnectionListener(function (connected) {
            isWsConnected = connected
        }, true);
        NetworkTables.addWsConnectionListener(function (connected) {
            if (connected) {
                robotConnection = NetworkTables.getRobotAddress()
            } else {
                robotConnection = "Disconnected"
            }
        }, true);
    })
</script>

<svelte:window on:hashchange={refreshDashboard}/>

<main>
    <div class="tree">
        <div class="centered">
            <button on:click={() => refreshDashboard()}>Refresh</button>
            <button on:click={() => {$expandAll = !$expandAll}}>{$expandAll ? "Collapse All" : "Expand All"}</button>
        </div>
        <Tree label="SmartDashboard" path="SmartDashboard"
              values={tree["SmartDashboard"] !== undefined ? tree["SmartDashboard"] : {}}/>
        <p>
            WS: {isWsConnected ? "Connected" : "Disconnected"}
            <br>
            Robot: {robotConnection}
        </p>
    </div>

    <div class="view">
        <h1>{path}</h1>
        <div class="widgets">
            {#each selectedKeys as key}
                <Number label={key.replace(path + '/', "")} value="10"/>
            {/each}
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
        padding-right: 1.2rem;
        height: 100vh;
        border-right: white 1px solid;
    }

    .view {
        width: 100%;
        padding-left: 1.2rem;
    }


    .centered {
        text-align: center;
    }

    .widgets {
        width: 100%;
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
    }
</style>
