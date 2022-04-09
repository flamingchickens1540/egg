<script>
    import {onMount} from "svelte";

    let canvas;
    onMount(() => {
        let ctx = canvas.getContext("2d");
    })
</script>

<main>
    <canvas bind:this={canvas}></canvas>
</main>

<style>
    canvas {
        background: url("/field/2022.png");
        background-size: 100% 100%;
        height: 500px;
    }

    main {
        padding: 10px;
        margin-right: 10px;
        margin-bottom: 10px;
        border: white solid 1px;
        border-radius: 5px;
    }
</style>
