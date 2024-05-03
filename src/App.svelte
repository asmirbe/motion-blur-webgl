<script>
	import Slider from "./component/Slider.svelte";
	import Canvas, { canvasState } from "./component/Canvas.svelte";
	import InputRange from "./component/InputRange.svelte";

	$: strengthProgressFormatted = (Math.round(($canvasState.strength / 40) * 100) / 100).toFixed(1);
	// $: strengthProgressFormatted = (Math.round(($canvasState.strength / 40) * 10)).toFixed(1);

	function updateStrength(value) {
		canvasState.update((state) => ({
			...state,
			strength: value.toFixed(2),
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
				<Canvas width="355" strength={$canvasState.strength} angle={$canvasState.angle} radius={$canvasState.radius} />
			</div>
			<div class="control-wrapper">
				<div class="control">
					<div>
						<div class="title">
							Motion blur <span>{strengthProgressFormatted}</span>
						</div>
						<InputRange max={40} value={$canvasState.strength} onChange={updateStrength}/>
					</div>
				</div>
				<div class="control">
					<Slider r={25} trackWidth={2} thumbWidth={20} value={$canvasState.angle} initialAngle={90} removeAngleLimit={true} onChange={updateAngle} arcColor="#0b5ee3" max={360} trackColor="#222222" />
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
		height: 500px;
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

	* {
		box-sizing: border-box;
		user-select: none;
	}
</style>
