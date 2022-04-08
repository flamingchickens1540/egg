<script>
    import Tree from "./components/Tree.svelte";
    import {expandAll} from "./stores";
    import Number from "./components/widgets/Number.svelte";
    import {onMount} from "svelte";

    let ls = [
        "SmartDashboard/shooter/tuning/frontRPM",
        "SmartDashboard/shooter/tuning/rearRPM",
        "SmartDashboard/indexer/sensors/front/beamBreak",
        "SmartDashboard/indexer/sensors/rear/beamBreak",
        "SmartDashboard/indexer/sensors/stagedForShooting",
        "SmartDashboard/intake/sensors/frontRPM",
        "SmartDashboard/intake/sensors/rearRPM",
    ];

    let tree = {}
    for (let item of ls) {
        let t = tree
        for (let part of item.split("/")) {
            t = t[part] !== undefined ? t[part] : (t[part] = {})
        }
    }

    onMount(refreshDashboard)

    let path;
    function refreshDashboard() {
        path = window.location.hash.slice(1);
    }
</script>

<svelte:window on:hashchange={refreshDashboard}/>

<main>
    <div class="tree">
        <div class="centered">
            <button on:click={() => {$expandAll = !$expandAll}}>{$expandAll ? "Collapse All" : "Expand All"}</button>
        </div>
        <Tree label="SmartDashboard" path="SmartDashboard" values={tree["SmartDashboard"]}/>
    </div>
    <div class="view">
        <h1>{path}</h1>
        <Number label="foo" value="10"/>
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
        width: 75%;
        padding-left: 1.2rem;
    }

    .centered {
        text-align: center;
    }
</style>
