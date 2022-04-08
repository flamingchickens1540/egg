<script context="module">
    // retain module scoped expansion state for each tree node
    const _expansionState = {
        /* treeNodeId: expanded <boolean> */
    }
</script>

<script>
    import {expandAll} from "../stores";

    export let label, values, path;

    let expanded = _expansionState[label] || false
    const toggleExpansion = () => {
        expanded = _expansionState[label] = !expanded
    }
    $: expanded = $expandAll
    $: arrowDown = expanded
</script>

<ul>
    <!-- transition:slide-->
    <li>
        <!-- If self has children -->
        {#if (Object.keys(values).length !== 0)}
            <span on:click={toggleExpansion} class="arrow" class:arrowDown>&#x25b6</span>
            <span on:click={() => {window.location.hash = path}}>
                {label}
            </span>
            {#if expanded}
                {#each Object.entries(values) as [k, v]}
                    <svelte:self path={path + "/" + k} label={k} values={v}/>
                {/each}
            {/if}
        {:else}
            <span class="leaf">
                {label}
            </span>
        {/if}
    </li>
</ul>

<style>
    ul {
        list-style: none;
        padding-left: 1.2rem;
        user-select: none;
    }

    .arrow {
        cursor: pointer;
        display: inline-block;
    }

    .arrowDown {
        transform: rotate(90deg);
    }

    .leaf {
        color: #464646;
    }
</style>
