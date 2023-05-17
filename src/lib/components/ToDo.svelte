<script>
    import { createEventDispatcher } from "svelte";

    export let todoData = undefined;
    export let index = undefined;
    export const testProp = 'test';

    const dispatch = createEventDispatcher();
    function triggerDelete() {
        dispatch('removeEvent', index);
    }
</script>

<div class="todo-entry" class:done={todoData.done}>

    <!-- <p>index: {index}</p> -->
    <!-- <div>{todoData.text}</div> -->

    <!-- checkboxen -->
    <div class="todo-checkbox-container">
      {#if todoData.done}
        <input type="checkbox" bind:checked={todoData.done} id="todo-{index}" class="todo-checkbox" />
      {:else}
        <input type="checkbox" bind:checked={todoData.done} id="todo-{index}" class="todo-label" style="border: none; width: 13px; height: 13px; border-radius: 50%; margin-right: 0.5em; margin-top: 0.4em;color:lightgrey;"/>
      {/if}
        <label for="todo-{index}" class="todo-label">
        {#if todoData.done}
          <span class="material-symbols-outlined" style="color: blue;">check</span>
        {/if}
      </label>
    </div>
    
    <!-- text -->
    <div 
      style="font-family: SF Pro Text, sans-serif; font-size: 1em; margin-top: 0.2em"
      class:done={todoData.done}
    >{todoData.text}</div>

    <!-- tag -->
    <!-- {#if todo.tag}
    <div class="todo-tag" style="background-color: {todo.tagColor}">{todo.tag}</div>
    {/if} -->
    
    <!-- löschen -->
    <button
      class="delete"
      on:click={() => {
        triggerDelete(index);
      }}
      ><span class="material-symbols-outlined" style="border:none; color: lightgrey; background-color: white;">cancel</span>
    </button>
    </div>
    <slot/>

    <style>
        .done {
            color: lightgrey;
        }

        .todo-entry {
            display: flex;
            margin-bottom: 1em;
        }

        .todo-entry:not(.done) div {
            color: black;      
        }

        .todo-checkbox {
            display: none;
            position: relative;
            width: 1.2em;
            height: 1.2em;
            border: 1px solid lightgray; /* 1px Outline-Stärke */
            border-radius: 50%; /* Kreisförmige Darstellung */
        }

        .todo-checkbox-container{
            display: flex;
            margin-right: 1em;
        }

        .todo-label:before {
            position: relative;
            width: 1.2em;
            height: 1.2em;
            border: 1px solid lightgray; /* 1px Outline-Stärke */
            border-radius: 50%; /* Kreisförmige Darstellung */
        }

        .material-symbols-outlined {
            color: white;
            border: none;
            font-variation-settings:
                'FILL' 0,
                'wght' 400,
                'GRAD' 0,
                'opsz' 48
        }
    </style>