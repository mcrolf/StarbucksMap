<script>
    /* tutorial wants me to use "let" as the variable definition 
    for dimensions, but that gives an error saying that I have to 
    use "const" instead...
    */
    export const dimensions = {
        margin: {
            top: 0,
            bottom: 0,
            right: 0,
            left: 0
        }
    };

    export let colors = [];
    export let categories = [];

    // labels gives an invalid array length for some reason
    let labels = [categories[0], ...new Array(colors.length - 2), categories[1]];
</script>

<g transform={'translate(${dimensions.width - 120}, $dimensions.height - 200})'}>
    {#each colors as color, i}
        <g transform={'translate(0, ${i * 24})'}>
            <rect height="20" width="20" fill={color}/>
            {#if labels[i]}
                <text 
                dominant-baseline="middle" 
                y="14" 
                x="28" 
                font-size="12" 
                font-weight="bold">
                {labels[i]}
                </text>
            {/if}
        </g>
    {/each}
</g>