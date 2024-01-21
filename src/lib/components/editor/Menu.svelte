<script lang="ts">
	import type { Editor } from 'svelte-tiptap';

	import { posToDOMRect } from '@tiptap/core';
	import { fade } from 'svelte/transition';
	import BlockInstruments from './instruments/BlockInstruments.svelte';
	import InlineInstruments from './instruments/InlineInstruments.svelte';
	export let editor: Editor;

	let showFloatingMenu = false;
	let showBubbleMenu = false;

	let rangeStart = editor.view.state.selection.ranges.reduce(function (prev, curr) {
		return prev.$from < curr.$from ? prev : curr;
	});
	let rangeEnd = editor.view.state.selection.ranges.reduce(function (prev, curr) {
		return prev.$from > curr.$from ? prev : curr;
	});

	let bottom = 0;

	$: {
		rangeStart = editor.view.state.selection.ranges.reduce(function (prev, curr) {
			return prev.$from < curr.$from ? prev : curr;
		});
		rangeEnd = editor.view.state.selection.ranges.reduce(function (prev, curr) {
			return prev.$from > curr.$from ? prev : curr;
		});
	}

	$: {
		showFloatingMenu =
			rangeStart.$from.pos === rangeEnd.$to.pos &&
			rangeStart.$from.depth === 2 &&
			rangeStart.$to.depth === 2 &&
			(editor.isFocused || hoverMenu) &&
			rangeStart.$from.parentOffset === 0 &&
			rangeStart.$to.parentOffset === 0;

		showBubbleMenu = rangeStart.$from.pos !== rangeEnd.$to.pos && (editor.isFocused || hoverMenu);

		if (showBubbleMenu || showFloatingMenu) {
			bottom =
				posToDOMRect(editor.view, rangeStart.$from.pos, rangeEnd.$to.pos).bottom +
				window.scrollY +
				8;
		}
	}

	let hoverMenu = false;
</script>

<div class="absolute top-0 w-full">
	<div class="mx-auto max-w-screen-md">
		<div class="relative mx-8">
			{#if showBubbleMenu || showFloatingMenu}
				<div
					style="top: {bottom}px;"
					class="absolute flex max-w-screen-md flex-wrap items-center gap-x-4 gap-y-1 rounded-lg border bg-card p-1 text-card-foreground shadow-sm"
					on:pointerover={() => {
						hoverMenu = true;
					}}
					on:pointerleave={() => {
						hoverMenu = false;
					}}
					transition:fade={{ duration: 150 }}
				>
					{#if showBubbleMenu}
						<InlineInstruments {editor} />
					{/if}
					{#if showFloatingMenu}
						<BlockInstruments {editor} />
					{/if}
				</div>
			{/if}
		</div>
	</div>
</div>
