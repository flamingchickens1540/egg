<script>
    import {onMount} from "svelte";

    let canvas;
    onMount(() => {
        canvas.addEventListener("mousedown", function (e) {
            const rect = canvas.getBoundingClientRect()
            const x = e.clientX - rect.left
            const y = e.clientY - rect.top
            console.log(x, ",", y)
        })

        let ctx = canvas.getContext("2d");
        let topX = 175;
        let topY = 40;
        let path = new Path2D();
        path.moveTo(topX, topY);
        path.lineTo(topX - 5, topY + 10);
        path.lineTo(topX + 5, topY + 10);
        ctx.fillStyle = "red";
        ctx.fill(path);
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
