<script lang="ts">
	import { slide } from 'svelte/transition';

	export let header: string;
	export let description: string;
	export let isExpanded: boolean;
	export let onClick: () => void;
</script>

<section
	class="flex cursor-pointer flex-col items-start gap-2 overflow-hidden rounded-2xl border p-4 shadow-lg transition-all duration-300 hover:bg-zinc-200 hover:shadow-xl"
	role="button"
	tabindex="0"
	on:click={onClick}
	on:keydown={(e) => (e.key === 'Enter' || e.key === ' ') && onClick()}
>
	<span class="text-2xl font-semibold">{header}</span>

	{#if isExpanded}
		<span transition:slide class="text-lg transition-opacity duration-300">
			{description}
		</span>
	{/if}
</section>
