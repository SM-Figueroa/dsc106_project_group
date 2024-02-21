<script>
    import * as d3 from 'd3';
    import { createEventDispatcher } from 'svelte';

    export let data;
    const dispatch = createEventDispatcher();

    function choosePlayer(player) {
        console.log("player change!");
        dispatch('change-variable', { newValue: player });
    }

</script>

<div class="button-container">

    {#each data as d, i}
        <button on:click={() => choosePlayer(i)}>
            <img src={'https://www.basketball-reference.com/req/202106291/images/headshots/' + d.id + '.jpg'} alt="no pic">
            <span>{d.Player}</span>
        </button>
    {/each}
</div>

<style>
    .button-container {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
        gap: 10px;
        justify-items: center;
    }

    button {
        width: 120px;
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    button img {
        width: 90px;
        margin-right: 5px;
    }
</style>