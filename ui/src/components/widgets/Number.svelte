<script>
    import Wrapper from "./Wrapper.svelte";

    export let label, value;
</script>

<Wrapper>
    <span>{label}</span>
    <input type="number" bind:value>
</Wrapper>
