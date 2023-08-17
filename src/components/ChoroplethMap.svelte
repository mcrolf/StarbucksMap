<script lang="ts">
    import {extent, rollup} from "d3-array";
    import {csv, json} from "d3-fetch";
    import {geoPath} from "d3-geo";
    import {scaleQuantile, scaleLinear} from "d3-scale";
    //import * as topojson from "topojson-client";
    import { type UsAtlas, mesh, feature } from "topojson";
    import {onMount} from "svelte";
    import Legend from "./Legend.svelte";

    export let datasets = [];
    export let colors = [];

    // define map dimensions
    let dimensions = {
        width: 975,
        height: 720,
        margin: {
        top: 24,
        right: 0,
        left: 0,
        bottom: 6
        }
    };

    let scale = scaleLinear<string, string>()
        .domain([0, 1])
        .range(["#FFFFFF", "#FFFFFF"]);

    let ratios = new Map();

    const path = geoPath();

    let stateMesh;
    let statesFeatures = [];

    let categories = datasets.map(({label}) => label);

    //map datasets to promises that return the parsed csv data
    //run the promises with Promise.all
    onMount(async () => {
      
        const counts = await Promise.all(
            datasets.map(
                async ({url}) =>
                    await csv(url, ({state, count}) => ({
                        state, count: +count,
                }))
            )
        );

        // us is of type: UsAtlas
        const us: UsAtlas = await json("/data/us_topojson.json");

        const data = [...counts[0], ...counts[1]];

        ratios = rollup(data, ([v1, v2]) => v1.count  / v2.count, d => d.state);

        const [, max] = extent(Array.from(ratios.values()), ratio => ratio);

        scale = scaleLinear<string, string>()
                .domain([max, (max-1)/2, 1, 0.5, 0])
                .range(colors);

        stateMesh = mesh(us, us.objects.states, (a, b) => a !== b);
        
        //features property does not exist i guess...
        statesFeatures = feature(us, us.objects.states).features;
    });


</script>


<svg width={dimensions.width} 
height={dimensions.height} 
viewBox={`0 0 ${dimensions.width} ${dimensions.height}`}>
    <g>
        <path fill="none" stroke="white" stroke-linejoin="round" d={path(stateMesh)} />
        {#each statesFeatures as feature}
        <path fill={scale(ratios.get(feature.properties.name))} d={path(feature)}/>
        {/each}
    </g>
    <!-- Legend element on the page -->
    <Legend 
    dimensions={dimensions} 
    colors={colors} 
    categories={categories}/>
</svg>