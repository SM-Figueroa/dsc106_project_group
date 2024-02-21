<script>
    import * as d3 from 'd3';

    // Bring in data
    export let data;

    // Variable to hold player chosen
    let chosenPlayer;

    // Declare the chart dimensions and margins.
    const width = 800;
    const height = 400;
    const marginTop = 20;
    const marginRight = 20;
    const marginBottom = 30;
    const marginLeft = 40;
    const leftspace = 10;

    // Legend dims
    const legendItemHeight = 20;
    const legendItemWidth = 20;
    const spacing = 30;

    // Declare axes
    let gx;
    let gy;

    // Define avg values and metric
    const metrics = ["PTS", "TRB", "AST", "STL", "BLK"];
    const avgs = {"PTS": 20.596052631578946, "TRB": 8.24342105263158, "AST": 4.563157894736841, "STL":1.315385, "BLK":0.890769};
    const xLabels = ['points', 'rebounds', 'assists', 'steals', 'blocks'];

    // group by metric scale
    $: mg = d3.scaleBand()
        .domain(metrics)
        .range([marginLeft + leftspace, width - marginRight])
        .paddingInner(0.3);

    // player vs. avg scale
    const groups = [chosenPlayer, 'top 75 avg'];

    // x axis scale for each metric
    $: x = d3.scaleBand()
        .domain(groups) // descending frequency
        .rangeRound([0, mg.bandwidth()])
        .padding(0.05);
    
    // Declare the y (vertical position) scale.
    $: y = d3.scaleLinear()
        .domain([0, d3.max(data, (d) => d.PTS)])
        .rangeRound([height - marginBottom, marginTop]);

    $: color = d3.scaleOrdinal(d3.schemeTableau10);

    // Set axes
    $: d3.select(gx).call(d3.axisBottom(mg).tickFormat((d, i) => xLabels[i]));
    $: d3.select(gy).call(d3.axisLeft(y));

    // $: console.log(chosenPlayer);

    // Update choice of player
    function choosePlayer(player) {
        chosenPlayer = player;
    }

</script>

<div class="chart-container">
    <svg {width} {height}>
        <!-- y axis label -->
        <text 
            transform="rotate(-90)"
            y={marginLeft - 30}
            x={-height/2- 50}
            font-size=20px
            >
        career average
        </text>

        <!-- legend colors and text -->
        <g class='legend' transform='translate({width}, {0})'>
            {#if chosenPlayer !== undefined}
                <rect 
                    x=-50
                    y={0}
                    width={legendItemWidth}
                    height={legendItemHeight}
                    fill={color(0)}
                />
                <text
                    x={legendItemWidth - 45}
                    y={13}
                    alignment-baseline='middle'
                    >
                    {data[chosenPlayer].Player}
                </text>
            {/if}
            <rect 
                x=-50
                y={spacing}
                width={legendItemWidth}
                height={legendItemHeight}
                fill={color(1)}
            />
            <text
                x={legendItemWidth - 45}
                y={spacing + 13}
                alignment-baseline='middle'
                >
                Top 75 avg
            </text>
        </g>

        <!-- barplot -->
        <g>
            {#each metrics as m, i}
                <g transform="translate({mg(m)}, 0)">
                    {#if chosenPlayer !== undefined}
                        {console.log('NOT UNDEFINED!')}
                        <rect 
                            x={x(chosenPlayer)}
                            y={y(data[chosenPlayer][m])}
                            width={x.bandwidth()}
                            height={y(0) - y(data[chosenPlayer][m])}
                            fill={color(0)}
                        />
                    {/if}
                    <rect 
                        x={x('top 75 avg')}
                        y={y(avgs[m])}
                        width={x.bandwidth()}
                        height={y(0) - y(avgs[m])}
                        fill={color(1)}
                    />
                </g>
            {/each}
        </g>

        <!-- x-axis -->
        <g bind:this={gx} transform="translate(0,{height - marginBottom})" />
        <!-- y-axis -->
        <g bind:this={gy} transform="translate({marginLeft},0)" />

    </svg>
    
</div>


<!-- buttons -->
<h2> Choose your favorite NBA player and see how they compare to the top 75 of all time!</h2>
<div class="button-container">
    {#each data as d, i}
        <button on:click={() => choosePlayer(i)}>
            <img src={'https://www.basketball-reference.com/req/202106291/images/headshots/' + d.id + '.jpg'} alt="no pic">
            <span>{d.Player}</span>
        </button>
    {/each}
</div>


<style>
    /* Basic Reset */
    * {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
    }

    /* body text */
    body {
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        background-color: #f4f4f4;
        color: #333;
        line-height: 1.6;
        padding: 20px;
    }

    /* chart container */
    .chart-container {
        align-content: center;
        background-color: #fff;
        border: 1px solid #ddd;
        border-radius: 8px;
        padding: 20px;
        margin-bottom: 20px;
    }

    /* svg */
    svg {
        display: block;
        overflow: visible;
    }

    /* text */
    text {
        fill: #333;
        font-size: 16px;
    }

    /* button container */
    .button-container {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
        justify-items: center;
        gap: 10px;
        justify-content: center;
        padding: 20px;
        background-color: #fff;
        border: 1px solid #ddd;
        border-radius: 8px;
    }

    /* button */
    button {
        background-color: white;
        /* border: none; */
        border-color: #333;
        color: white;
        padding: 10px;
        font-size: 14px;
        cursor: pointer;
        display: flex;
        flex-direction: column;
        align-items: center;
        border-radius: 5px;
        overflow: hidden;
        transition: background-color 0.3s ease;

        /* Set uniform size for all buttons */
        width: 110px; /* Adjust width as needed */
        height: 110px; /* Adjust height as needed */
        margin: 5px;
    }

    /* button hover */
    button:hover {
        background-color: #7f8399;
    }

    /* player image in button */
    button img {
        width: 60px;
        height: 60px;
        border-radius: 20%;
        margin-bottom: 5px;
    }

    /* player name on button */
    button span {
        text-align: center;
        font-size: 12px;
        color: black;
    }

    /* Responsive Design */
    @media (max-width: 768px) {
        .button-container {
            flex-direction: row;
            flex-wrap: wrap;
            justify-content: space-around;
        }
    }


</style>