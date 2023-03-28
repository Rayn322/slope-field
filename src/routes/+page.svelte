<script lang="ts">
	import { parse } from 'mathjs';
	let width: number = 0;
	let height: number = 0;
	let equation: string = 'x + y';

	let equationFunction: (scope?: any) => any;
	$: try {
		equationFunction = parse(equation).compile().evaluate;
	} catch (e) {
		// ignore
	}

	const scale = 50;

	const xTicks = [-10, -9, -8, -7, -6, -5, -4, -3, -2, -1, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
	const yTicks = [-10, -9, -8, -7, -6, -5, -4, -3, -2, -1, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

	function getCoordinates(x: number, y: number, func: (scope?: any) => any) {
		let slope: number;
		let emptyLine = { x1: 0, y1: 0, x2: 0, y2: 0 };

		try {
			slope = func({ x, y });
		} catch {
			return emptyLine;
		}

		if (isNaN(slope)) {
			return emptyLine;
		}

		const hypotenuse = Math.sqrt(slope ** 2 + 1);
		const length = 0.25;

		// make hypotenuse a length of 1
		const x1 = x - length / hypotenuse;
		const y1 = y - (length * slope) / hypotenuse;
		const x2 = x + length / hypotenuse;
		const y2 = y + (length * slope) / hypotenuse;

		return { x1, y1, x2, y2 };
	}
</script>

<svelte:window bind:innerWidth={width} bind:innerHeight={height} />

<div class="m-8">
	<label for="equation">Equation</label>
	<input type="text" id="equation" bind:value={equation} />
</div>

<svg class="absolute left-0 top-0 -z-10" {width} {height} xmlns="http://www.w3.org/2000/svg">
	<line x1={0} y1={height / 2} x2={width} y2={height / 2} stroke="black" />
	<line x1={width / 2} y1={0} x2={width / 2} y2={height} stroke="black" />

	{#each xTicks as tick}
		<line
			x1={width / 2 + tick * scale}
			y1={height / 2 - 5}
			x2={width / 2 + tick * scale}
			y2={height / 2 + 5}
			stroke="black"
		/>
		<text x={width / 2 + tick * scale + 5} y={height / 2 - 15} text-anchor="end">{tick}</text>
	{/each}

	{#each yTicks as tick}
		<line
			x1={width / 2 - 5}
			y1={height / 2 + tick * scale}
			x2={width / 2 + 5}
			y2={height / 2 + tick * scale}
			stroke="black"
		/>
		<text x={width / 2 + 15} y={height / 2 + tick * scale + 5}>{tick}</text>
	{/each}

	{#each xTicks as x}
		{#each yTicks as y}
			{@const { x1, y1, x2, y2 } = getCoordinates(x, y, equationFunction)}
			<circle
				cx={width / 2 + x * scale}
				cy={height / 2 - y * scale}
				r={2}
				fill="black"
				opacity="0.2"
			/>
			<line
				x1={width / 2 + x1 * scale}
				y1={height / 2 - y1 * scale}
				x2={width / 2 + x2 * scale}
				y2={height / 2 - y2 * scale}
				stroke="black"
				opacity="0.5"
			/>
		{/each}
	{/each}
</svg>
