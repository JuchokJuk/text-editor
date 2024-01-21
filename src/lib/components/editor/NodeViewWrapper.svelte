<script lang="ts">
	import { getContext, onMount, tick } from 'svelte';
	const { onDragStart } = getContext('TipTapNodeView');
	let element;
	export let as = 'div';
	onMount(async () => {
		await tick();
		element.style.whiteSpace = 'normal';
	});
</script>

<svelte:element
	this={as}
	bind:this={element}
	data-node-view-wrapper=""
	on:dragstart={onDragStart}
	on:pointerover
	on:pointerleave
	role="none"
	{...$$restProps}
>
	<slot />
</svelte:element>
