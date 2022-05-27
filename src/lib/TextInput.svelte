<script lang='ts'>
    import { onDestroy, onMount } from "svelte";
    
        export let id = "text";
        export let label = "[label]";
        export let placeholder = "";
        export let value = "";
    
        export let width = "100%";
        export let height = "fit-content";
    
        onMount(() => {});
        onDestroy(() => {});
    
    </script>
    
    <div class="wrapper" style:width style:height>
        <label 
        for={id}>
            {label}
        </label>
        <input
            type="text" 
            {id} 
            {placeholder}
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
            font-family: monospace;
            width: 100%;
        }
    
    </style>