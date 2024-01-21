<script lang="ts">
	import * as Tooltip from '$lib/components/ui/tooltip';
	import { Button } from '$lib/components/ui/button';

	export let label: string;
	export let active = false;
	export let additionalBuilders: any[] | undefined = undefined;
</script>

<Tooltip.Root portal={null}>
	<Tooltip.Trigger asChild let:builder>
		<Button
			builders={additionalBuilders ? [...additionalBuilders, builder] : [builder]}
			variant="ghost"
			size="icon"
			class={active ? 'bg-accent text-accent-foreground' : undefined}
			aria-label={label}
			on:click
		>
			<slot />
		</Button>
	</Tooltip.Trigger>
	<Tooltip.Content>
		<p>{label}</p>
	</Tooltip.Content>
</Tooltip.Root>
