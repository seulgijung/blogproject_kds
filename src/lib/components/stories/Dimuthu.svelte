<script>
  import * as d3 from 'd3'

  // DATASET
  const data = [
    { season: "2005-06", wins: 23 },
    { season: "2006-07", wins: 33 },
    { season: "2007-08", wins: 23 },
    { season: "2008-09", wins: 32 },
    { season: "2009-10", wins: 29 },
    { season: "2010-11", wins: 42 },
    { season: "2011-12", wins: 36 },
    { season: "2012-13", wins: 54 },
    { season: "2013-14", wins: 37 },
    { season: "2014-15", wins: 17 },
    { season: "2015-16", wins: 32 },
    { season: "2016-17", wins: 31 },
    { season: "2017-18", wins: 29 },
    { season: "2018-19", wins: 17 },
    { season: "2019-20", wins: 21 },
    { season: "2020-21", wins: 41 },
    { season: "2021-22", wins: 37 },
    { season: "2022-23", wins: 47 },
    { season: "2023-24", wins: 50 },
    { season: "2024-25", wins: 51 },
    { season: "2025-26", wins: 53 }
  ]

  // DIMENSIONS
  const width = 680
  const height = 400
  const margin = { top: 40, right: 20, bottom: 60, left: 50 }

  // SCALES
  const xScale = d3.scaleBand()
    .domain(data.map(d => d.season))
    .range([margin.left, width - margin.right])

  const yScale = d3.scaleLinear()
    .domain([0, d3.max(data, d => d.wins)])
    .range([height - margin.bottom, margin.top])

  // LINE GENERATOR
  const lineGenerator = d3.line()
    .x(d => xScale(d.season) + xScale.bandwidth() / 2)
    .y(d => yScale(d.wins))

  const linePath = lineGenerator(data)
</script>

<article class="story-card">
  <h2 class="headline"> Last week's historic win also marked Knicks's best season over a decade </h2>
  <p>After hitting a franchise low of 17 twice in five years, the Knicks climbed to 53 wins and a championship in 2026</p>

    <div class="chart-workspace">

      <svg viewBox="0 0 {width} {height}" {width} {height}
        font-family="'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif">

        <!-- Y AXIS LABELS AND GRIDLINES -->
        {#each [0, 10, 20, 30, 40, 50] as tick}
          <text
            x={margin.left - 10}
            y={yScale(tick) + 4}
            text-anchor="end"
            font-size="11px"
            fill="#999"
          >
            {tick}
          </text>
          <line
            x1={margin.left}
            y1={yScale(tick)}
            x2={width - margin.right}
            y2={yScale(tick)}
            stroke="#eee"
          />
        {/each}

        <!-- LINE -->
        <path
          d={linePath}
          fill="none"
          stroke="#006BB6"
          stroke-width="2.5"
        />

        <!-- DOTS -->
        {#each data as d}
          {#if d.wins === 54 || d.wins === 53}
            <circle
              cx={xScale(d.season) + xScale.bandwidth() / 2}
              cy={yScale(d.wins)}
              r={d.wins === 54 ? 8 : 6}
              fill="#F58426"
            />
            <text
              x={xScale(d.season) + xScale.bandwidth() / 2}
              y={yScale(d.wins) - 12}
              text-anchor="middle"
              font-size="11px"
              fill="#F58426"
            >
              {d.wins}
            </text>
          {/if}
        {/each}

		<!-- X AXIS LABELS -->
        {#each data as d, i}
          {#if i % 2 === 0}
            <text
              x={xScale(d.season) + xScale.bandwidth() / 2}
              y={height - margin.bottom + 20}
              text-anchor="middle"
              font-size="11px"
              fill="#999"
            >
              {d.season}
            </text>
          {/if}
        {/each}


        <!-- X AXIS LINE -->
        <line
          x1={margin.left}
          y1={height - margin.bottom}
          x2={width - margin.right}
          y2={height - margin.bottom}
          stroke="#111"
        />

      </svg>

    </div>
    <figcaption class="chart-footer">
      Source: NBA.com; Chart by: Dimuthu Attanayake
    </figcaption>

</article>

<style>
  
  svg {
    display: block;
    width: 100%;
}
  .story-card {
    margin-bottom: 3rem;
    font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
  }
  .divider {
    border: none;
    border-top: 1px solid #222;
    margin: 0 0 2rem;
  }
  .headline {
    font-size: 1.75rem;
    font-weight: 700;
    line-height: 1.2;
    margin: 0 0 0.5rem;
    font-family: Georgia, serif;
  }
  .byline {
    font-size: 0.875rem;
    color: #666;
    margin: 0 0 1.5rem;
  }
  p {
    font-size: 1.0625rem;
    line-height: 1.7;
    margin: 0 0 1rem;
    color: #999;
  }
  .chart {
    margin: 2rem 0 0;
  }
  .chart-title {
    font-size: 0.875rem;
    font-weight: 600;
    margin-bottom: 0.75rem;
  }
  
  .chart-footer {
    margin-top: 0.75rem;
    font-size: 0.8125rem;
    color: #999;
  }
</style>