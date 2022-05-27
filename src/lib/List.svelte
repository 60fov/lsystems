<script lang='ts'>

    export let items = ["aaaa"];
    

</script>

<ul class="list">
    {#each items as item}
        <li class="item"><span>{item}</span><span>remove</span></li>
    {/each}
</ul>

<style lang='scss'>

.list {
    list-style-type: none;
    width: 100%;
    border: var(--border);
}

.item {
    display: flex;
    justify-content: space-between;
}

</style>