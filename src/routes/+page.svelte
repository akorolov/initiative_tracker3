<script lang="ts">
    import Board from "$lib/Board.svelte";
    import { onMount } from 'svelte';

    // Define the type for a column item
    interface ColumnItem {
        id: number;
        name: string;
        health: number | string;
        conditions: string[];
    }

    // Define the type for a column
    interface Column {
        id: string;
        name: string;
        primary: Boolean;
        items: ColumnItem[];
    }

    // Default data to use if no saved data exists
    const defaultColumnsData: Column[] = [
        {
            id: "c1",
            name: "Character Pool",
            primary: false,
            items: [
                {id: 1, name: "Monster 1", health: 0, conditions: []},
                {id: 2, name: "Monster 2", health: 0, conditions: []},
                {id: 3, name: "Monster 3", health: 0, conditions: []}
            ]
        },
        {
            id: "c2",
            name: "Current Combat",
            primary: true,
            items: [
                {id: 10, name: "Elie", health: 0, conditions: []},
                {id: 11, name: "Pelias", health: 0, conditions: []},
                {id: 12, name: "Lou", health: 0, conditions: []},
                {id: 13, name: "Mattias", health: 0, conditions: []}
            ]
        },
    ];

    // Initialize with default data (will be replaced if saved data exists)
    let columnsData: Column[] = [...defaultColumnsData];

    // Load saved data on component mount
    onMount(() => {
        try {
            const savedData = localStorage.getItem('initiativeTrackerData');
            if (savedData) {
                columnsData = JSON.parse(savedData) as Column[];
                console.log('Loaded saved character data');
            }
        } catch (error) {
            console.error('Error loading saved data:', error);
            // If there's an error loading the data, use the default data
            columnsData = [...defaultColumnsData];
        }
    });

    function handleBoardUpdated(newColumnsData: Column[]) {
        // Update the local state
        columnsData = newColumnsData;
        
        // Save to localStorage for persistence
        try {
            localStorage.setItem('initiativeTrackerData', JSON.stringify(columnsData));
            console.log('Saved character data');
        } catch (error) {
            console.error('Error saving data:', error);
        }
    }

    // Function to reset data to defaults
    function resetToDefaults() {
        // Update the columnsData with default values
        const resetData = [...defaultColumnsData];
        
        // Call handleBoardUpdated to update the state and save to localStorage
        handleBoardUpdated(resetData);
        
        console.log('Reset to default data');
    }
</script>

<div class="flex flex-row">
    <Board columns={columnsData} onFinalUpdate={handleBoardUpdated}/>
    
    <div class="sidebar mt-2 mr-2">
        <button class="btn preset-filled-error-500" on:click={resetToDefaults}>
            Reset
        </button>
    </div>
</div>

