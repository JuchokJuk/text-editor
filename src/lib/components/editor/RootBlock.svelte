<script lang="ts">
	import type { Editor } from '@tiptap/core';
	import { GripVertical, Plus } from 'lucide-svelte';
	import { NodeViewContent } from 'svelte-tiptap';
	import NodeViewWrapper from './NodeViewWrapper.svelte';
	import Button from '../ui/button/button.svelte';
	import * as Tooltip from '../ui/tooltip';
	import { fade } from 'svelte/transition';

	export let node: any;
	export let getPos: () => number;
	export let editor: Editor;

	function createNodeAfter() {
		// Calculate the position right after the current node
		const pos = getPos() + node.nodeSize;

		// Use the editor's command to insert a new "rootblock" node at the calculated position
		editor
			.chain()
			.insertContentAt(pos, {
				type: 'rootblock',
				content: [
					{
						type: 'paragraph'
					}
				]
			})
			.focus(pos + 3) // Focus on the new block (you might need to adjust the position based on your exact requirements)
			.run();
	}

	let showBlockMenu = false;
	let hoverMenu = false;
	let hoverBlock = false;

	$: {
		showBlockMenu = hoverMenu || hoverBlock;
	}
</script>

<NodeViewWrapper
	{...$$restProps}
	as="div"
	draggable
	class="relative"
	on:pointerover={() => {
		hoverBlock = true;
	}}
	on:pointerleave={() => {
		hoverBlock = false;
	}}
>
	{#if showBlockMenu}
		<div
			class="absolute -left-24 flex w-24 gap-1"
			transition:fade={{ duration: 150 }}
			on:pointerover={() => {
				hoverMenu = true;
			}}
			on:pointerleave={() => {
				hoverMenu = false;
			}}
		>
			<Tooltip.Root portal={null}>
				<Tooltip.Trigger asChild let:builder>
					<Button builders={[builder]} size="icon" variant="ghost" on:click={createNodeAfter}>
						<Plus class="h-4 w-4" />
					</Button>
				</Tooltip.Trigger>
				<Tooltip.Content>
					Добавить блок снизу
				</Tooltip.Content>
			</Tooltip.Root>
			<Button size="icon" variant="ghost" data-drag-handle class="cursor-grab">
				<GripVertical class="h-4 w-4" />
			</Button>
		</div>
	{/if}
	<NodeViewContent />
</NodeViewWrapper>
