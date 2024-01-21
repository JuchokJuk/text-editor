<script lang="ts">
	import {
		Bold,
		Code,
		Highlighter,
		Italic,
		Link,
		Strikethrough,
		Subscript,
		Superscript,
		Underline
	} from 'lucide-svelte';

	import { Editor } from 'svelte-tiptap';
	import MenuButton from '../instruments/MenuButton.svelte';
	import * as Popover from '$lib/components/ui/popover';
	import { Button } from '$lib/components/ui/button';
	import { Input } from '$lib/components/ui/input';
	import { z } from 'zod';
	import { onMount } from 'svelte';

	export let editor: Editor;

	let linkForm = { url: '' };
	let linkFormValid = false;

	const linkFormSchema = z.object({
		url: z.string().url()
	});

	$: {
		try {
			linkFormSchema.parse(linkForm);
			linkFormValid = true;
		} catch (e) {
			linkFormValid = false;
		}
	}
</script>

<div class="flex flex-wrap items-center justify-between gap-1">
	<Popover.Root portal={null}>
		<Popover.Trigger asChild let:builder>
			<MenuButton additionalBuilders={[builder]} label="Ссылка" active={editor.isActive('link')}>
				<Link class="h-4 w-4" />
			</MenuButton>
		</Popover.Trigger>

		<Popover.Content class="flex w-full max-w-sm items-center space-x-2">
			<Input bind:value={linkForm.url} type="url" placeholder="https://example.com" />
			<Button
				disabled={!linkFormValid}
				on:click={() => {
					editor.commands.toggleLink({ href: linkForm.url, target: '_blank' });
				}}
			>
				Вставить
			</Button>
			<Button
				on:click={() => {
					editor.commands.unsetLink();
				}}
			>
				Убрать
			</Button>
		</Popover.Content>
	</Popover.Root>
</div>

<div class="flex flex-wrap items-center justify-between gap-1">
	<MenuButton
		label="Жирный"
		on:click={() => {
			editor.chain().focus().toggleBold().run();
		}}
		active={editor.isActive('bold')}
	>
		<Bold class="h-4 w-4" />
	</MenuButton>
	<MenuButton
		label="Наклон"
		on:click={() => {
			editor.chain().focus().toggleItalic().run();
		}}
		active={editor.isActive('italic')}
	>
		<Italic class="h-4 w-4" />
	</MenuButton>
	<MenuButton
		label="Подчеркнутый"
		on:click={() => {
			editor.chain().focus().toggleUnderline().run();
		}}
		active={editor.isActive('underline')}
	>
		<Underline class="h-4 w-4" />
	</MenuButton>
	<MenuButton
		label="Зачеркнутый"
		on:click={() => {
			editor.chain().focus().toggleStrike().run();
		}}
		active={editor.isActive('strike')}
	>
		<Strikethrough class="h-4 w-4" />
	</MenuButton>
</div>

<div class="flex flex-wrap items-center justify-between gap-1">
	<MenuButton
		label="Выделенный"
		on:click={() => {
			editor.chain().focus().toggleHighlight().run();
		}}
		active={editor.isActive('highlight')}
	>
		<Highlighter class="h-4 w-4" />
	</MenuButton>
</div>

<div class="flex flex-wrap items-center justify-between gap-1">
	<MenuButton
		label="Верхний текст"
		on:click={() => {
			editor.chain().focus().toggleSuperscript().run();
		}}
		active={editor.isActive('superscript')}
	>
		<Superscript class="h-4 w-4" />
	</MenuButton>
	<MenuButton
		label="Нижний текст"
		on:click={() => {
			editor.chain().focus().toggleSubscript().run();
		}}
		active={editor.isActive('subscript')}
	>
		<Subscript class="h-4 w-4" />
	</MenuButton>
</div>

<div class="flex flex-wrap items-center justify-between gap-1">
	<MenuButton
		label="Код"
		on:click={() => {
			editor.chain().focus().toggleCode().run();
		}}
		active={editor.isActive('code')}
	>
		<Code class="h-4 w-4" />
	</MenuButton>
</div>
