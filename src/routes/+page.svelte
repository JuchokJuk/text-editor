<script lang="ts">
	import { onMount, onDestroy } from 'svelte';

	import { createEditor, Editor, EditorContent } from 'svelte-tiptap';
	import type { Readable } from 'svelte/store';

	// import Document from '@tiptap/extension-document';
	import { Node } from '@tiptap/core';
	import RootBlock from '$lib/components/editor/RootBlock';
	import Keymap from '$lib/components/editor/Keymap';
	import History from '@tiptap/extension-history';

	import Dropcursor from '@tiptap/extension-dropcursor';
	import Gapcursor from '@tiptap/extension-gapcursor';

	import HardBreak from '@tiptap/extension-hard-break';

	import Link from '@tiptap/extension-link';
	import Bold from '@tiptap/extension-bold';
	import Italic from '@tiptap/extension-italic';
	import Underline from '@tiptap/extension-underline';
	import Strike from '@tiptap/extension-strike';
	import Highlight from '@tiptap/extension-highlight';
	import Superscript from '@tiptap/extension-superscript';
	import Subscript from '@tiptap/extension-subscript';
	import Code from '@tiptap/extension-code';

	import Emoji, { gitHubEmojis } from '@tiptap-pro/extension-emoji';

	import Text from '@tiptap/extension-text';
	import Paragraph from '@tiptap/extension-paragraph';
	import Heading from '@tiptap/extension-heading';

	import Blockquote from '@tiptap/extension-blockquote';
	import HorizontalRule from '@tiptap/extension-horizontal-rule';

	import ListItem from '@tiptap/extension-list-item';
	import BulletList from '@tiptap/extension-bullet-list';
	import OrderedList from '@tiptap/extension-ordered-list';
	import CodeBlockLowlight from '@tiptap/extension-code-block-lowlight';
	import Image from '@tiptap/extension-image';
	import YouTube from '@tiptap/extension-youtube';

	import FileHandler from '@tiptap-pro/extension-file-handler';

	import Typography from '@tiptap/extension-typography';

	import css from 'highlight.js/lib/languages/css';
	import js from 'highlight.js/lib/languages/javascript';
	import ts from 'highlight.js/lib/languages/typescript';
	import html from 'highlight.js/lib/languages/xml';
	import markdown from 'highlight.js/lib/languages/markdown';

	import { common, createLowlight } from 'lowlight';

	import { Button } from '$lib/components/ui/button';
	import Menu from '$lib/components/editor/Menu.svelte';

	import sampleDocument from '../assets/document.json?raw';

	const lowlight = createLowlight(common);

	let editor: Readable<Editor>;

	lowlight.register('html', html);
	lowlight.register('css', css);
	lowlight.register('js', js);
	lowlight.register('ts', ts);
	lowlight.register('markdown', markdown);

	const Document = Node.create({
		name: 'doc',
		topNode: true,
		content: 'rootblock+'
	});

	onMount(() => {
		editor = createEditor({
			extensions: [
				Document,
				RootBlock,
				Keymap,
				History,

				Dropcursor,
				Gapcursor,

				HardBreak,

				// inline start
				Link,
				Bold,
				Italic,
				Underline,
				Strike,
				Highlight,
				Superscript,
				Subscript,
				Code,
				// inline end

				Emoji.configure({
					emojis: gitHubEmojis,
					enableEmoticons: true
				}),

				// blocks start
				Text,
				Paragraph,
				Heading,

				Blockquote,
				HorizontalRule,

				ListItem,
				BulletList,
				OrderedList,
				CodeBlockLowlight.configure({ lowlight }),
				Image,
				YouTube,

				FileHandler.configure({
					allowedMimeTypes: ['image/png', 'image/jpeg', 'image/gif', 'image/webp'],
					onDrop: (currentEditor, files, pos) => {
						files.forEach((file) => {
							const fileReader = new FileReader();

							fileReader.readAsDataURL(file);
							fileReader.onload = () => {
								currentEditor
									.chain()
									.insertContentAt(pos, {
										type: 'image',
										attrs: {
											src: fileReader.result
										}
									})
									.focus()
									.run();
							};
						});
					},
					onPaste: (currentEditor, files, htmlContent) => {
						files.forEach((file) => {
							if (htmlContent) {
								// if there is htmlContent, stop manual insertion & let other extensions handle insertion via inputRule
								// you could extract the pasted file from this url string and upload it to a server for example
								// console.log(htmlContent);
								return false;
							}

							const fileReader = new FileReader();

							fileReader.readAsDataURL(file);
							fileReader.onload = () => {
								currentEditor
									.chain()
									.insertContentAt(currentEditor.state.selection.anchor, {
										type: 'image',
										attrs: {
											src: fileReader.result
										}
									})
									.focus()
									.run();
							};
						});
					}
				}),
				// blocks end

				Typography
			],
			content: JSON.parse(sampleDocument)
		});
	});

	function save() {
		const page = JSON.stringify($editor.getJSON());

		const link = document.createElement('a');
		const file = new Blob([page], { type: 'text/plain' });
		link.href = URL.createObjectURL(file);
		link.download = 'document.json';
		link.click();
	}

	onDestroy(() => {
		if ($editor) {
			$editor.destroy();
		}
	});
</script>

{#if $editor}
	<EditorContent editor={$editor} />
	<Menu editor={$editor} />
{/if}

<Button on:click={save} variant="outline" class="fixed bottom-4 right-4">Сохранить</Button>
