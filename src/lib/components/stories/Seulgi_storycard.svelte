<!--
  Copy this file into the stories/ folder, rename it (e.g. JaneStory.svelte),
  then import your new component in Blog.svelte.
-->
<script>
	import * as d3 from 'd3';

  	const data = [
		{ season: "2021-22", wins: 37 },
    	{ season: "2022-23", wins: 47 },
    	{ season: "2023-24", wins: 50 },
    	{ season: "2024-25", wins: 51 },
    	{ season: "2025-26", wins: 53 },
  	];

	const width = 640;
	const height = 320;
	const margin = { top: 20, right: 40, bottom: 40, left: 40 };
	const innerWidth = width - margin.left - margin.right;
	const innerHeight = height - margin.top - margin.bottom;

	const xScale = d3.scalePoint()
		.domain(data.map(d=>d.season))
		.range([0, innerWidth]);
	const yScale = d3.scaleLinear()
		.domain([0, d3.max(data, d => d.wins)])
		.range([innerHeight, 0]);

	const lineGenerator = d3.line()
		.x(d => xScale(d.season))
		.y(d => yScale(d.wins))
	const linePath = lineGenerator(data);
</script>
<article class="story-card">
	<hr class="divider" />

	<h2 class="headline">How the Knicks Built Toward a Title, One Season at a Timee</h2>
	<p class="byline">By Seulgi Jung</p>

	<p>The New York Knicks did not arrive at the 2025-26 championship overnight. Their regular-season win total climbed every year for five straight seasons — from 37 wins in 2021-22 to 53 in 2025-26 — a steady rise rather than a sudden leap.</p>
	<p>That final season ended a 52-year championship drought, the franchise's first title since 1973. The chart below tracks only regular-season wins, so it can't explain why the team broke through — but it does show a team that improved consistently in the years leading up to it.</p>

	<figure class="chart">
		<figcaption class="chart-title">New York Knicks regular-season wins, 2021-22 to 2025-26</figcaption>

		<div class="chart-workspace">
			<svg viewBox="0 0 {width} {height}">
				<g transform="translate({margin.left},{margin.top})">
					<line x1="0" x2={innerWidth} y1={innerHeight} y2={innerHeight} stroke="currentColor"/>
					<line x1="0" x2="0" y1={innerHeight} y2="0" stroke="currentColor"/>
					<path d={linePath} fill="none" stroke="currentColor" stroke-width="2"/>
					{#each data as d}
						<circle
							cx={xScale(d.season)}
							cy={yScale(d.wins)}
							r="4"
							fill="currentColor"
						/>
						<text
							x={xScale(d.season)}
							y={innerHeight+20}
							text-anchor="middle">
							{d.season}
						</text>
					{/each}
					{#each yScale.ticks(5) as tick}
						<text
							x="-8"
							y={yScale(tick)}
							text-anchor="end" 
							fill="currentColor">
							{tick}
						</text>
					{/each}
				</g>
			</svg>
		</div>

		<figcaption class="chart-footer">Source: Basketball-Reference; Credit: Seulgi Jung</figcaption>
	</figure>
</article>

<style>
	.story-card {
		margin-bottom: 3rem;
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
	}

	.chart {
		margin: 2rem 0 0;
	}

	.chart-title {
		font-size: 0.875rem;
		font-weight: 600;
		margin-bottom: 0.75rem;
	}

	.chart-workspace {
		display: flex;
		align-items: center;
		justify-content: center;
		min-height: 320px;
		border: 1px solid #ddd;
		background: #fafafa;
	}

	.chart-placeholder {
		margin: 0;
		font-size: 0.875rem;
		color: #999;
	}

	.chart-footer {
		margin-top: 0.75rem;
		font-size: 0.8125rem;
		color: #666;
	}
</style>
