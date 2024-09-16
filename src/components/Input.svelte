<script>
    export let labelText;
    export let id;
    export let tooltipText = "";
    export let inputValue = "";
    export let onKeypress;

    let showTooltip = false;
</script>

<div class={`input-row${showTooltip ? "" : "-with-margin"}`}>
    <label
        class="input-label"
        for={id}
        on:mouseenter={() => {
            showTooltip = tooltipText.length > 0;
        }}
        on:mouseleave={() => {
            showTooltip = false;
        }}
    >
        {labelText}
    </label>
    <input
        type="text"
        id={id}
        class="input-boxes"
        bind:value={inputValue}
        on:keypress={onKeypress}
    />
</div>

{#if showTooltip}
    <div class={`tooltip`}>
        {tooltipText}
    </div>
{/if}

<style>
    /* INPUT STYLES */
    .input-row, .input-row-with-margin {
        display: flex;
        flex-direction: row;
    }

    .input-row-with-margin {
        margin-bottom: 5px;
    }

    .input-label {
        width: 150px;
    }

    .input-boxes {
        font-size: 12px;
    }

    .tooltip {
        font-size: small;
        color: #ff7400;
        margin-bottom: 5px;
    }

</style>
