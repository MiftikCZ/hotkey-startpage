
<script lang="ts">
    export let key: string;
    export let title: string;
    export let add_key;
    export let fun;
    export let key_title:string = "";
    export let th
    add_key(key,fun)


</script>

<div class="keybind" bind:this={th}>
    <span class="gray">[{key_title || key}]</span>
    <span class="title">{title}</span>
</div>


<style>
    :global(.gray) {
        color: #c6a0f6;
    }
</style>
