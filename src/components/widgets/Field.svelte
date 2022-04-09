<script>
    import {onMount} from "svelte";

    const canvasMaxX = 300;
    const canvasMaxY = 150;
    const fieldMaxX = 16.445;
    const fieldMaxY = 8.225;

    let heading = 0;
    let poseString = "";

    function updatePose() {
        let pose = NetworkTables.getValue("/Shuffleboard/Autonomous/Field/Robot");
        if (pose === undefined) {
            return
        }

        // let x = pose[0]
        // let y = pose[1]
        // let absoluteAngle = Math.round(pose[2])
        let x = 10;
        let y = 10;
        let absoluteAngle = testAngle;
        poseString = "(" + Math.round(x) + ", " + Math.round(y) + ") @ " + Math.round(absoluteAngle) + "°"

        let ctx = canvas.getContext("2d");

        // Top vertex of the triangle
        let topX = Math.round(x * canvasMaxX / fieldMaxX);
        let topY = Math.abs(Math.round(y * canvasMaxY / fieldMaxY) - canvasMaxY);

        ctx.clearRect(0, 0, canvas.width, canvas.height);
        let path = new Path2D();
        path.moveTo(topX, topY);
        path.lineTo(topX - 5, topY + 15);
        path.lineTo(topX + 5, topY + 15);
        ctx.fillStyle = "red";

        if (absoluteAngle !== heading) {
            let angleOffset = heading + absoluteAngle * (heading < absoluteAngle ? 1 : -1);
            console.log("Target absolute angle " + absoluteAngle + " doesn't equal heading " + heading + ", rotating " + angleOffset + " degrees")
            ctx.translate(topX, topY);
            ctx.rotate((Math.PI / 180) * angleOffset);
            ctx.translate(-topX, -topY);
            heading = absoluteAngle;
            console.log("Heading is now " + heading);
        }

        ctx.fill(path);
    }

    let testAngle = 0;

    let canvas;
    setInterval(updatePose, 1000)
</script>

<main>
    <canvas bind:this={canvas}></canvas>
    <span>{poseString}</span>
    <input type="number" bind:value={testAngle}>
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
