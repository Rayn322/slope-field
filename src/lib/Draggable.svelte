<script lang="ts">
	export let left = 100;
	export let top = 100;
	let className: string = '';
	export { className as class };

	let moving = false;

	function onMouseDown() {
		moving = true;
	}

	function onMouseMove(e: MouseEvent) {
		if (moving) {
			left += e.movementX;
			top += e.movementY;
		}
	}

	function onMouseUp() {
		moving = false;
	}
</script>

<section
	on:mousedown={onMouseDown}
	style="left: {left}px; top: {top}px;"
	class={`draggable absolute cursor-move select-none ${className}`}
>
	<slot />
</section>

<svelte:window on:mouseup={onMouseUp} on:mousemove={onMouseMove} />
