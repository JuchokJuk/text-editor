<script lang="ts">
	import { Editor } from 'svelte-tiptap';
	import Heading_1 from 'lucide-svelte/icons/heading-1';
	import Heading_2 from 'lucide-svelte/icons/heading-2';
	import Heading_3 from 'lucide-svelte/icons/heading-3';
	import Heading_4 from 'lucide-svelte/icons/heading-4';
	import { Minus, Quote, List, ListOrdered, Code, Image, Video } from 'lucide-svelte';
	import MenuButton from '../instruments/MenuButton.svelte';
	import * as Popover from '$lib/components/ui/popover';
	import { Input } from '$lib/components/ui/input';

	import { Button } from '$lib/components/ui/button';

	import { z } from 'zod';

	export let editor: Editor;

	let imageForm = { url: '' };
	let imageFormValid = false;

	const imageFormSchema = z.object({
		url: z.string().url()
	});

	$: {
		try {
			imageFormSchema.parse(imageForm);
			imageFormValid = true;
		} catch (e) {
			imageFormValid = false;
		}
	}

	let videoForm = { url: '' };
	let videoFormValid = false;

	const videoFormSchema = z.object({
		url: z.string().url()
	});

	$: {
		try {
			videoFormSchema.parse(videoForm);
			videoFormValid = true;
		} catch (e) {
			videoFormValid = false;
		}
	}
</script>

<div class="flex flex-wrap items-center justify-between gap-1">
	<MenuButton
		label="Заголовок 1 уровня"
		on:click={() => {
			editor.chain().focus().toggleHeading({ level: 1 }).run();
		}}
		active={editor.isActive('heading', { level: 1 })}
	>
		<Heading_1 class="h-4 w-4" />
	</MenuButton>
	<MenuButton
		label="Заголовок 2 уровня"
		on:click={() => {
			editor.chain().focus().toggleHeading({ level: 2 }).run();
		}}
		active={editor.isActive('heading', { level: 2 })}
	>
		<Heading_2 class="h-4 w-4" />
	</MenuButton>
	<MenuButton
		label="Заголовок 3 уровня"
		on:click={() => {
			editor.chain().focus().toggleHeading({ level: 3 }).run();
		}}
		active={editor.isActive('heading', { level: 3 })}
	>
		<Heading_3 class="h-4 w-4" />
	</MenuButton>
	<MenuButton
		label="Заголовок 4 уровня"
		on:click={() => {
			editor.chain().focus().toggleHeading({ level: 4 }).run();
		}}
		active={editor.isActive('heading', { level: 4 })}
	>
		<Heading_4 class="h-4 w-4" />
	</MenuButton>
</div>

<div class="flex flex-wrap items-center justify-between gap-1">
	<Popover.Root portal={null}>
		<Popover.Trigger asChild let:builder>
			<MenuButton additionalBuilders={[builder]} label="Картинка">
				<Image class="h-4 w-4" />
			</MenuButton>
		</Popover.Trigger>
		<Popover.Content class="flex w-full max-w-sm items-center space-x-2">
			<Input bind:value={imageForm.url} type="url" placeholder="https://example.com" />
			<Button
				disabled={!imageFormValid}
				on:click={() => {
					editor.commands.setImage({ src: imageForm.url });
				}}
			>
				Вставить
			</Button>
		</Popover.Content>
	</Popover.Root>
	<Popover.Root portal={null}>
		<Popover.Trigger asChild let:builder>
			<MenuButton additionalBuilders={[builder]} label="Ютуб видео">
				<Video class="h-4 w-4" />
			</MenuButton>
		</Popover.Trigger>
		<Popover.Content class="flex w-full max-w-sm items-center space-x-2">
			<Input bind:value={videoForm.url} type="url" placeholder="https://youtube.com" />
			<Button
				disabled={!videoFormValid}
				on:click={() => {
					editor.commands.setYoutubeVideo({ src: videoForm.url });
				}}
			>
				Вставить
			</Button>
		</Popover.Content>
	</Popover.Root>
</div>

<div class="flex flex-wrap items-center justify-between gap-1">
	<MenuButton
		label="Цитата"
		on:click={() => {
			editor.chain().focus().toggleBlockquote().run();
		}}
		active={editor.isActive('blockquote')}
	>
		<Quote class="h-4 w-4" />
	</MenuButton>
</div>

<div class="flex flex-wrap items-center justify-between gap-1">
	<MenuButton
		label="Список"
		on:click={() => {
			editor.chain().focus().toggleBulletList().run();
		}}
		active={editor.isActive('bulletList')}
	>
		<List class="h-4 w-4" />
	</MenuButton>
	<MenuButton
		label="Нумерованный список"
		on:click={() => {
			editor.chain().focus().toggleOrderedList().run();
		}}
		active={editor.isActive('orderedList')}
	>
		<ListOrdered class="h-4 w-4" />
	</MenuButton>
</div>

<div class="flex flex-wrap items-center justify-between gap-1">
	<MenuButton
		label="Разделитель"
		on:click={() => {
			editor.chain().focus().setHorizontalRule().run();
		}}
		active={editor.isActive('horizontalRule')}
	>
		<Minus class="h-4 w-4" />
	</MenuButton>
</div>

<div class="flex flex-wrap items-center justify-between gap-1">
	<MenuButton
		label="Код"
		on:click={() => {
			editor.chain().focus().toggleCodeBlock().run();
		}}
		active={editor.isActive('codeBlock')}
	>
		<Code class="h-4 w-4" />
	</MenuButton>
</div>
