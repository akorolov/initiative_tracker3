<script>
	import { flip } from 'svelte/animate';
	import Column from "./Column.svelte";
	const flipDurationMs = 300;
	
  export let columns;
	// will be called any time a card or a column gets dropped to update the parent data
	export let onFinalUpdate;

 	function handleItemFinalize(columnIdx, newItems) {
		columns[columnIdx].items = newItems;
		onFinalUpdate([...columns]);
	}
</script>


<section class="board flex flex-row gap-4">
    {#each columns as column, idx (column.id)}
  		<div class="card {column.primary ? 'bg-surface-200-800' : 'bg-surface-100-900'} border-[1px] border-tertiary-200-800 w-full max-w-xl p-2 px-4" animate:flip="{{duration: flipDurationMs}}" >    
				<Column name={column.name} items={column.items} onDrop={(newItems) => handleItemFinalize(idx, newItems)} />
			</div>
    {/each}
</section>


<style>
    .board {
        height: 100vh;
		width: 100%;
        padding: 0.5em;
    }
</style>