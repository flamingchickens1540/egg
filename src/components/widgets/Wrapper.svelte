<script>
    export let large = false;
</script>

<main class:large>
    <slot></slot>
</main>

<style>
    main {
        width: 250px;
        padding: 10px;
        margin-right: 10px;
        margin-bottom: 10px;
        border: white solid 1px;
        border-radius: 5px;
    }

    .large {
        width: 1000px;
    }
</style>
