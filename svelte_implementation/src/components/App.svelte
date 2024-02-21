<!-- App.svelte -->
<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';
    import Barplot from './Barplot.svelte';

    // load dataframe here
    let data = [];

    onMount(async () => {

        const res = await fetch('https://raw.githubusercontent.com/SM-Figueroa/dsc106_project_group/main/nba_header.csv'); 

        const csv = await res.text();

        data = d3.csvParse(csv, d3.autoType);

    });

</script>

<main>
    <body>
        <!-- title/header of page -->
        <h1> NBA Top 75 Player Stats Comparison </h1>
        <h3> An interactive comparison of average player career performance from Basketball-Reference.com</h3>
        <Barplot {data}/>
    </body>

</main>

<style>
    /* Basic Reset */
    * {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
    }

    /* text css */
    body {
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        background-color: #f0f2f5;
        color: #333;
        line-height: 1.6;
        padding: 20px;
    }

    /* Typography */
    h1, h2, h3, h4, h5, h6 {
        color: #333;
    }

    h1 {
        font-size: 2.5rem;
        margin-bottom: 0.75rem;
    }

    h2 {
        font-size: 2rem;
        margin-bottom: 0.5rem;
    }

    p {
        margin-bottom: 1rem;
    }


</style>
