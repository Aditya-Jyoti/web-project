<script lang="ts">
	export let header: string;
	export let description: string;
	export let image: string;
</script>

<section
	class="flex flex-col items-center gap-6 rounded-2xl border-[1px] p-2 shadow-lg transition-transform duration-300 hover:scale-[101%]"
>
<img src={image} alt="" class="grayscale rounded-xl w-full h-72">
	<div class="flex flex-col gap-2">
		<span class="text-3xl font-semibold">{header}</span>
		<span class="text-xl">{description}</span>
	</div>
</section>
