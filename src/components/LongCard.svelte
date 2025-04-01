<script lang="ts">
	export let header: string;
	export let direction: string;
	export let description: string;
	export let image: string;
</script>

<section class="flex h-44 flex-row items-center gap-6 rounded-2xl border-[1px] p-2 shadow-lg hover:scale-[101%] transition-transform duration-300">
	{#if direction === 'start'}
		<img src={`/svgs/${image}`} alt="Long card" class="w-32" />
	{/if}
	<div
		class={`flex flex-col gap-2 items-${direction} text-${direction === 'start' ? 'left' : 'right'}`}
	>
		<span class="text-3xl">{header}</span>
		<span class="text-xl">{description}</span>
	</div>
	{#if direction === 'end'}
		<img src={`/svgs/${image}`} alt="Long card" class="w-32" />
	{/if}
</section>
