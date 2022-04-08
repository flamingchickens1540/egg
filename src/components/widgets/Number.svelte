<script>
    import Wrapper from "./Wrapper.svelte";

    export let label, key;

    let value;
    $: value = NetworkTables.getValue(key)
</script>

<Wrapper>
    <span>{label}</span>
    <input bind:value on:change={() => {NetworkTables.putValue(key, value)}} type="number">
</Wrapper>
