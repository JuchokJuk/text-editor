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
</script>

<NodeViewWrapper {...$$restProps} as="div" draggable class="group relative">
	<div
		class="absolute -left-24 flex h-full w-24 gap-1 opacity-0 transition-opacity group-hover:opacity-100 hover:opacity-100"
	>
		<Tooltip.Root portal={null}>
			<Tooltip.Trigger asChild let:builder>
				<Button builders={[builder]} size="icon" variant="ghost" on:click={createNodeAfter}>
					<Plus class="h-4 w-4" />
				</Button>
			</Tooltip.Trigger>
			<Tooltip.Content>Добавить блок снизу</Tooltip.Content>
		</Tooltip.Root>
		<Button size="icon" variant="ghost" data-drag-handle class="cursor-grab">
			<GripVertical class="h-4 w-4" />
		</Button>
	</div>
	<NodeViewContent />
</NodeViewWrapper>
