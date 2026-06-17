<script>
	import { onMount } from 'svelte';
	import * as d3 from 'd3';

	// Panel dimensions
	const PW = 190, PH = 130;
	const m = { top: 6, right: 8, bottom: 16, left: 28 };
	const iW = PW - m.left - m.right;
	const iH = PH - m.top - m.bottom;

	// Shared scales (fixed domain so all panels are comparable)
	const x = d3.scaleLinear([0, 48], [0, iW]);
	const y = d3.scaleLinear([-37, 8], [iH, 0]);

	// D3 line and area generators
	const lineFn = d3.line().x((d) => x(d.t)).y((d) => y(d.d)).curve(d3.curveMonotoneX);

	const quarterLines = [12, 24, 36];
	const yTicks = [-30, -20, -10, 0];
	const quarterMids = [6, 18, 30, 42];
	const quarterLabels = ['Q1', 'Q2', 'Q3', 'Q4'];

	// This will hold our parsed game data once loaded
	let games = $state([]);

	onMount(async () => {
		const res = await fetch('/nba_comebacks_points.csv');
		const text = await res.text();

		const rows = d3.csvParse(text, (d) => ({
			game_id: +d.game_id,
			title: d.title,
			sub: d.sub,
			highlight: d.highlight === 'true',
			t: +d.t,
			d: +d.d
		}));

		games = Array.from(d3.group(rows, (r) => r.game_id).values());
	});
</script>

<article class="story-card">
	<hr class="divider" />

	<h2 class="headline">The Largest Comebacks in NBA Playoff History</h2>
	<p class="subtitle">How nine teams erased double-digit deficits to win — and how differently each one got there</p>
	<p class="byline">By Kylie Clifton</p>

	<p>
		Each panel traces the point differential of one of the largest single-game comebacks in NBA
		playoff history, from tip-off to the final buzzer. The x-axis is game time divided into four
		quarters; the y-axis shows how many points the comeback team was down (below zero) or up (above
		zero). Every line starts tied, plunges into deficit, and claws back to a win. The <span
			class="knicks-label">Knicks' 2026 NBA Finals game</span
		> — highlighted in red — is the largest comeback in Finals history at 29 points down, notable for
		its double-dip in the fourth quarter before OG Anunoby's tip-in with 1.2 seconds left sealed it.
		The Clippers' 2019 game against the Warriors remains the all-time playoff record at 31 points.
	</p>

	<figure class="chart">
		<figcaption class="chart-title">
			Point differential over game time — largest NBA playoff comebacks
		</figcaption>

		<div class="grid">
			{#each games as game}
				{@const info = game[0]}
				{@const color = info.highlight ? '#F58426' : '#444'}
				{@const strokeW = info.highlight ? 2 : 1.5}

				<div class="panel" class:highlight={info.highlight}>
					<div class="panel-title">{info.title}</div>
					<div class="panel-sub">{info.sub}</div>
					<svg width={PW} height={PH}>
						<g transform="translate({m.left},{m.top})">
							<!-- Quarter dividers -->
							{#each quarterLines as q}
								<line x1={x(q)} x2={x(q)} y1={0} y2={iH} stroke="#e0e0e0" stroke-width="1" />
							{/each}

							<!-- Y-axis gridlines and labels -->
							{#each yTicks as tick}
								<line
									x1={0}
									x2={iW}
									y1={y(tick)}
									y2={y(tick)}
									stroke={tick === 0 ? '#aaa' : '#ebebeb'}
									stroke-width={tick === 0 ? 1.5 : 1}
								/>
								<text x={-4} y={y(tick)} text-anchor="end" dominant-baseline="middle" class="y-label"
									>{tick}</text
								>
							{/each}

							<!-- The line -->
							<path d={lineFn(game)} fill="none" stroke={color} stroke-width={strokeW} />

							<!-- Quarter labels along the bottom -->
							{#each quarterMids as mid, i}
								<text x={x(mid)} y={iH + 12} text-anchor="middle" class="x-label">
									{quarterLabels[i]}
								</text>
							{/each}
						</g>
					</svg>
				</div>
			{/each}
		</div>

		<figcaption class="chart-footer">
			<span class="note-label">Note:</span> Point differentials are approximate, reconstructed from
			play-by-play data, and reflect the comeback team's score minus their opponent's at key moments.
			Games shown represent the largest deficits overcome to win in a single playoff game.
			<br />
			<span class="note-label">Source:</span> NBA.com
		</figcaption>
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

	.subtitle {
		font-size: 1.0625rem;
		color: #444;
		margin: 0.25rem 0 0.75rem;
		line-height: 1.4;
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

	.knicks-label {
		color: #F58426;
		font-weight: 600;
	}

	.chart {
		margin: 2rem 0 0;
	}

	.chart-title {
		font-size: 0.875rem;
		font-weight: 600;
		margin-bottom: 1rem;
		display: block;
	}

	.grid {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 1rem 0.5rem;
	}

	.panel {
		display: flex;
		flex-direction: column;
	}

	.panel-title {
		font-size: 0.75rem;
		font-weight: 700;
		color: #111;
		line-height: 1.3;
	}

	.panel-sub {
		font-size: 0.6875rem;
		color: #888;
		margin-bottom: 0.25rem;
		line-height: 1.3;
	}

	.highlight .panel-title {
		color: #F58426;
	}

	.highlight .panel-sub {
		color: #F58426;
		opacity: 0.75;
	}

	.chart-footer {
		margin-top: 0.75rem;
		font-size: 0.8125rem;
		color: #666;
		display: block;
		line-height: 1.6;
	}

	.note-label {
		font-weight: 600;
		color: #444;
	}

	.y-label {
		font-size: 9px;
		fill: #aaa;
	}

	.x-label {
		font-size: 8px;
		fill: #bbb;
	}
</style>
