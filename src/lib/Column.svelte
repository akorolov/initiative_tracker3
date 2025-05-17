<script lang="ts">
import { flip } from 'svelte/animate';
import { dndzone } from 'svelte-dnd-action';
import Item from './Item.svelte';

// Define item type
interface Item {
    id: number;
    name: string;
    health: string;
    conditions: string[];
}

const flipDurationMs = 150;
export let name: string;
export let items: Item[];
export let onDrop: (items: Item[]) => void;

function handleItemDelete(itemId: number) {
    // Filter out the deleted item
    const updatedItems = items.filter(item => item.id !== itemId);
    
    // Update the items array and notify the parent component
    items = updatedItems;
    onDrop(updatedItems);
}

function handleNewItem() {
	// Generate a unique ID (using timestamp for simplicity)
	const newId = Date.now();
	
	// Create a new character with default values
	const newItem = {
		id: newId,
		name: "Character",
		health: "0",
		conditions: []
	};

	// Add the new item to the items array
	const updatedItems = [...items, newItem];
	items = updatedItems;
	
	// Notify the parent component
	onDrop(updatedItems);
}

	function handleDndConsiderCards(e: CustomEvent) {
    console.warn("got consider", name); 
		items = e.detail.items;
    }
    function handleDndFinalizeCards(e: CustomEvent) {
        onDrop(e.detail.items);
    }
</script>

<div class='wrapper'>
 	<h4 class="h4 flex flex-row justify-center mb-1">
		{name}
	</h4>
	<div class="column-content flex flex-col items-center" use:dndzone={{items, flipDurationMs, zoneTabIndex: -1}}
         on:consider={handleDndConsiderCards} 
         on:finalize={handleDndFinalizeCards}>
        {#each items as item (item.id)}
            <div class="card preset-filled-surface-50-950 border-[1px] border-surface-100-900 p-2 m-1 w-full rounded-sm" animate:flip="{{duration: flipDurationMs}}">
                <Item 
                    bind:name={item.name} 
                    bind:health={item.health} 
                    bind:conditions={item.conditions}
                    on:delete={() => handleItemDelete(item.id)}
                    on:update={() => onDrop([...items])}
                />
            </div>
        {/each}
    </div>
</div>
<div class="flex flex-row justify-center mt-1">
	<button class="btn preset-filled-primary-500" on:click={handleNewItem}>New Character</button>
</div>


<style>
	.wrapper {
		height: calc(100% - 2.5em);
		width: 100%;
		/*Notice we make sure this container doesn't scroll so that the title stays on top and the dndzone inside is scrollable*/
		overflow-y: hidden;
	}
	.column-content {
        height: calc(100% - 2.5em);
        /* Notice that the scroll container needs to be the dndzone if you want dragging near the edge to trigger scrolling */
        overflow-y: scroll;
    }

</style>
