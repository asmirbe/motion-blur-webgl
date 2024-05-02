<script>
	export let diameter;
	export let color;
	export let position;
	export let handleSelect;
</script>

<div
	id="thumb"
	style="
    position: absolute;
    left: {position.x}px;
    top: {position.y}px;
		box-sizing: border-box;
  "
	draggable={false}
	on:touchstart={handleSelect}
	on:mousedown={handleSelect}
>
	<div
		id="thumb-image"
		style="
	width: {diameter}px;
	height: {diameter}px;
	background-color: {color};
	border-radius: 100%;
	transform: translate(-50%, -50%);
	position: absolute;
	cursor: pointer;
"
	/>
</div>
