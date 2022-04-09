<script>
    import Wrapper from "./Wrapper.svelte";

    export let label, key;

    let value;
    $: value = NetworkTables.getValue(key)
</script>

<Wrapper>
    <span>{label}</span>
    <br>
    <input bind:value on:change={() => {NetworkTables.putValue(key, value)}} type="checkbox">
</Wrapper>

<style>
    br {
        padding-top: 20px;
        margin-bottom: 10px;
    }

    input {
        height: 20px !important;
        width: 20px !important;
    }
</style>
