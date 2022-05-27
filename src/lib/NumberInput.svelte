<script lang='ts'>
import { onDestroy, onMount } from "svelte";

    export let id = "slider";
    export let label = "[label]";
    export let placeholder = "";
    export let min = 0;
    export let max = 10;
    export let step = 1;
    export let value = 1;

    export let width = "100%";
    export let height = "fit-content";

    export let threshold = 4;
    export let speed = 1;
    let cdx = 0;

    const clamp = (min: number, a: number, max: number) => Math.max(min, Math.min(max, a));

    onMount(() => {});
    onDestroy(() => {});

    function captureDrag(event: PointerEvent) {
        const target = event.currentTarget as HTMLElement;
        target.addEventListener('pointermove', drag);
        target.setPointerCapture(event.pointerId);
    }

    function drag(event: PointerEvent) {
        cdx += Math.floor(Math.abs(event.movementX) / 2);
        if (cdx > threshold) {
            value = clamp(min, value+(step * Math.sign(event.movementX) * speed), max);
            cdx = 0;
        }
    } 

    function releaseDrag(event: PointerEvent) {
        const target = event.currentTarget as HTMLElement;
        target.removeEventListener('pointermove', drag);
        target.releasePointerCapture(event.pointerId);
    }
</script>

<div class="wrapper" style:width style:height>
    <label 
    for={id}
    on:pointerdown={captureDrag}
    on:pointerup={releaseDrag}>
        {label}
    </label>
    <input
        type="number" 
        {id} 
        placeholder={placeholder || `[${min}, ${max}]`}
        bind:value>
    
</div>

<style lang='scss'>
    .wrapper {
        // padding: var(--padding);
        border: var(--border);
        display: flex;
        font-family: 'Founders Grotesk Condensed', sans-serif;
        align-items: center;
        justify-content: stretch;
        text-transform: uppercase;
    }

    label {
        width: fit-content;
        padding: var(--padding);
        background: rgb(224, 224, 224);
        cursor:ew-resize;
        user-select: none;
        border-right: var(--border);
    }

    input {
        border: none;
        background: none;
        outline: none;
        padding: var(--padding);
        -moz-appearance: textfield;
        font-family: monospace;
        width: 100%;
    }

    input[type=number]::-webkit-inner-spin-button, 
    input[type=number]::-webkit-outer-spin-button { 
        -webkit-appearance: none; 
        margin: 0; 
    }

</style>