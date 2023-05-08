<script>

import { onMount } from 'svelte';
import { browser } from '$app/environment';

let todoText = '';
let todos = [];

onMount(() => {
        if (browser) {
                const storedTodos = JSON.parse(
                        localStorage.getItem('todos')
                );
                if (storedTodos) {
                        todos = storedTodos;
                }
        }
});

function saveTodos() {
        // Save todos to local storage
        if (browser) {
                localStorage.setItem('todos', JSON.stringify(todos));
        }
}

function addTodo() {
        todos.push({ text: todoText, done: false });
        todos = todos;
        todoText = '';
        // Save todos to local storage
        saveTodos();
}

function remove(index) {
        //delete entry
        todos.splice(index, 1);
        todos = todos;
        saveTodos();
}
</script>

<h1>TODO APP</h1>

<!-- textfeld -->
<input type="text" class="todo-input" bind:value={todoText} />
<!-- button add -->
<button on:click={addTodo}>ADD</button>

{#each todos as todo, index}
<!-- todo -->
<div class="todo-entry" class:done={todo.done}>
        <!-- text -->
        <div>{todo.text}</div>
        <!-- checkboxen -->
        <input type="checkbox" bind:value={todo.done} />
        <button
                class="delete"
                on:click={() => {
                        remove(index);
                }}>X</button
        >
</div>
{/each}

<style>
.delete {
        background-color: white;
        border: none;
}
.delete:hover {
        background-color: grey;
        font-weight: 700;
}
.done {
        color: grey;
}
.todo-entry {
        display: flex;
}
</style>