<script>
    import Wrapper from "./Wrapper.svelte";

    export let label;
</script>

<Wrapper>
    <span>{label}</span>
    <select name="" id="">
        <option value="">2 ball</option>
        <option value="">3 ball</option>
    </select>
</Wrapper>

<style>
    option, select {
        width: 100%;
        background-color: var(--background);
    }
</style>
