<script lang="ts">
	export let header: string;
    export let direction: string;
</script>

<div class={`flex flex-col w-full items-${direction}`}>
	<span class="text-6xl font-bold uppercase">{header}</span>
	<div class="bg-black w-[15%] h-1 rounded-2xl" ></div>
</div>
