<script>

    /**
        * Assignment name: Inclass Svelte + D3
        * First name: 
        * Last name:
        * Student ID:
    */

    import * as d3 from 'd3';
    import { onMount } from 'svelte';
    import Barchart from '$lib/visualizations/barchart.svelte';
    import Scatterplot from '$lib/visualizations/scatterplot.svelte';
    import Tooltip from '$lib/components/tooltip.svelte';
  
    let data = $state([]);
    let difficultyFilter = $state([]);
    let filteredData = $derived.by(() => {
    if (difficultyFilter.length === 0) {
        return data;
    } else {
        return data.filter(d => difficultyFilter.includes(d.difficulty));
    }
    });

            function filterData(_difficulty) {
        const isActive = difficultyFilter.includes(_difficulty);
        if (isActive) {
            difficultyFilter = difficultyFilter.filter(d => d !== _difficulty);
        } else {
            difficultyFilter.push(_difficulty);
        }
        }
  
    onMount(async () => {
      const _data = await d3.csv('/data/vancouver_trails.csv');
      _data.forEach(d => {
        d.time = +d.time;
        d.distance = +d.distance;
      });
      data = _data;
    });
  
    const colorScale = $state(
      d3.scaleOrdinal()
        .range(['#d3eecd', '#7bc77e', '#2a8d46'])
        .domain(['Easy', 'Intermediate', 'Difficult'])
    );
  </script>
  
  <div class="flex h-screen w-full p-6 font-display rounded-xl">
    <div class="bg-white grid grid-cols-6 mx-auto px-4 py-8 gap-4">
      
      <div class="col-span-4 row-span-3 rounded-xl shadow-md p-8">
        {#if filteredData.length > 0 }
        <Scatterplot data={filteredData}
        xLabel="Distance"
        yLabel="Hours"
        {colorScale}
        />
        {/if}
    </div>

    <div class="col-span-2 row-span-3 rounded-xl shadow-md p-8">
        {#if data.length > 0}
          <Barchart {data} yLabel="Trails" {colorScale} {difficultyFilter} {filterData} />
        {/if}
      </div>
       
  
      <div class="source">
        Source: Data from <a href="https://vancouvertrails.com/trails/">vancouvertrails.com</a>
      </div>
    </div>
    <Tooltip/>
  </div>
  