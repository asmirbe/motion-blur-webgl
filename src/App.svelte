<script>
	import Slider from "./component/Slider.svelte";
	import Canvas, { canvasState } from "./component/Canvas.svelte";
	import { onMount } from "svelte";
	let strengthProgress = 0;

	export let options = {
		min: 0,
		max: 1,
		step: 0.01,
	};

	function generateBackground(value, min, max) {
		if (value === min) {
			return;
		}
		let percentage = ((value - min) / (max - min)) * 100;
		return `background: linear-gradient(to right, #0b5ee3 ${percentage}%, #222222 ${percentage}%)`;
	}

	$: strengthProgressFormatted = strengthProgress === options.max ? (1.0).toFixed(2) : (Math.floor(strengthProgress * 10) / 100).toFixed(2);

	function updateStrength(value) {
		canvasState.update((state) => ({
			...state,
			strength: (value * 40).toFixed(2),
		}));
	}
	function updateAngle(value) {
		canvasState.update((state) => ({
			...state,
			angle: value,
		}));
	}
</script>

<div class="debug">
	<div class="title">Debug panel [BETA]</div>
	<pre>
		{JSON.stringify($canvasState, null, 2)}
	</pre>
</div>
<div class="container">
	<div class="rotate-container">
		<div class="content">
			<div class="canvas">
				<Canvas strength={$canvasState.strength} angle={$canvasState.angle} radius={$canvasState.radius} />
			</div>
			<div class="control-wrapper">
				<div class="control">
					<div>
						<div class="title">
							Motion blur <span>{strengthProgressFormatted}</span>
						</div>
						<input type="range" bind:value={strengthProgress} min={options.min} max={options.max} step={options.step} style={generateBackground(strengthProgress, options.min, options.max)} on:input={() => updateStrength(strengthProgress)} />
					</div>
				</div>
				<div class="control">
					<Slider r={22} trackWidth={2} thumbWidth={20} value={$canvasState.angle} initialAngle={90} removeAngleLimit={true} onChange={updateAngle} arcColor="#0b5ee3" max={360} trackColor="#222222" />
				</div>
			</div>
		</div>
	</div>
</div>

<style>
	@import url("https://cdn.jsdelivr.net/gh/mailtoharshit/San-Francisco-Font-/sanfrancisco.css");
	.debug {
		position: absolute;
		display: none;
		bottom: 10px;
		right: 10px;
		background: rgba(0, 0, 0, 0.5);
		border: #222 solid 1px;
		color: white;
		padding: 8px;
	}
	.debug .title {
		padding: 0.25rem;
		font-size: 12px;
	}
	.debug pre {
		tab-size: 4;
		white-space: pre-line;
		font-size: 10px;
		color: #fffc;
		width: 100%;
		margin: 0;
		height: 100%;
	}

	.container {
		display: flex;
		justify-content: space-around;
		align-items: center;
		height: 100vh;
		background-color: black;
	}

	.control-wrapper {
		display: flex;
		gap: 8px;
	}

	.control {
		border-radius: 18px;
		background: #141414;
		padding: 16px;
		display: flex;
	}

	.control .title {
		color: rgba(255, 255, 255, 0.2);
		display: flex;
		margin-bottom: 0.5rem;
		justify-content: space-between;
	}

	.control .title span {
		color: #fff;
	}

	.content {
		height: 600px;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: flex-end;
	}
	.canvas {
		display: flex;
		height: 100%;
		width: 100%;
		align-items: center;
		justify-content: center;
	}
	.rotate-container {
		position: relative;
		display: flex;
		flex-direction: column;
		align-items: center;
		background-size: 100% 100%;
		background-position: center;
		background-repeat: no-repeat;
	}

	input[type="range"] {
		width: 180px;
		-webkit-appearance: none;
		height: 2px;
		border-radius: 5px;
		background: #222222;
		outline: none;
		padding: 0;
		margin: 0;
	}

	input[type="range"]::-webkit-slider-thumb {
		-webkit-appearance: none;
		width: 20px;
		height: 20px;
		border-radius: 50%;
		background: #fff;
		transition: all 0.15s ease-in-out;
		box-shadow: rgba(0, 0, 0, 0.16) 0px 1px 4px;
		cursor: pointer;
	}

	* {
		box-sizing: border-box;
		user-select: none;
	}
</style>
