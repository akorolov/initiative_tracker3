<script lang="ts">
import { TagsInput } from '@skeletonlabs/skeleton-svelte';
import { createEventDispatcher } from 'svelte';

const dispatch = createEventDispatcher();

export let name = "Character"
export let health = "0"
export let conditions: string[] = []

function handleDelete() {
    dispatch('delete');
}

function dispatchUpdate() {
    dispatch('update');
}

let edit_name = false

function handleKeydown(e: KeyboardEvent) {
    if (e.code == "Enter") {
        edit_name = false
        dispatchUpdate();
    }
}

function calculateHealth(e: KeyboardEvent) {
    if (e.code == "Enter") {
        let new_health = 0
        if (health.includes("+")) {
            health.split("+").forEach(element => {
                new_health += parseInt(element)
            })
        } else if (health.includes("-")) {
            health.split("-").forEach(element => {
                (new_health == 0) ? new_health = parseInt(element) : new_health -= parseInt(element)
            })
        } else {
            return;
        }
        health = new_health.toString()
        dispatchUpdate();
    }
}

</script>

<div class="flex flex-row gap-2 justify-between mb-2">
    <a on:click={() => edit_name = true} title="Click to rename character">
        {#if edit_name}
            <input class="input" type="text" bind:value={name} on:keydown={handleKeydown} />
        {:else}
        {name}
        {/if}
    </a>
    <div class="input-group grid-cols-[auto_1fr_auto]" style="width: 160px">
        <div class="ig-cell preset-tonal">
            <span class="material-symbols-outlined text-primary-700">
                favorite
                </span>
        </div>
        <input class="ig-input" type="text" placeholder=0 on:keydown={calculateHealth} bind:value={health} />
        
        </div>
        <button type="button" class="btn-icon bg-surface-600-400 text-surface-contrast-600-400" on:click={handleDelete}>
            <span class="material-symbols-outlined">skull</span>
        </button>
</div>

<TagsInput 
    value={conditions} 
    onValueChange={(e) => {
        conditions = e.value;
        dispatchUpdate();
    }} 
    tagBackground="preset-filled-warning-500" 
    name="chips" 
    placeholder="Add conditions..." 
/>
