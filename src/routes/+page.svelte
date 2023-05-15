<script>
  import { onMount, onDestroy } from 'svelte';
  import { browser } from '$app/environment';

  let todoText = '';
  let todos = [];
  let hasError = false;
  let numberOfTodos = 0;

  onMount(() => {
    if (browser) {
      const storedTodos = JSON.parse(localStorage.getItem('todos'));
      if (storedTodos) {
        todos = storedTodos;
        numberOfTodos = todos.length; // initialize numberOfTodos with the current length of the todos array
      }
    }
  });

  onDestroy(() => {
    saveTodos();
  });

  function saveTodos() {
    // Save todos to local storage
    if (browser) {
      localStorage.setItem('todos', JSON.stringify(todos));
    }
  }

  function addTodo() {
    if (todoText.trim() === '') {
      hasError = true;
      return;
    }
    todos.unshift({ text: todoText, done: false }); // add new todo to the beginning of the array
    todos = todos;
    todoText = '';
    hasError = false;
    numberOfTodos++; // increase numberOfTodos when a new todo is added
    
    // Save todos to local storage
    saveTodos();
  }

  function remove(index) {
    // delete entry
    todos.splice(index, 1);
    todos = todos;
    numberOfTodos--; // decrease numberOfTodos when a todo is removed
    saveTodos();
  }
</script>

<h1>Today</h1>

<!-- display the number of todos -->
{#if todos.length > 0}
  <p class="task">{todos.length} {todos.length === 1 ? 'task' : 'tasks'}</p>
{/if}

<div class="input-container">

<!-- textfeld -->
    <input type="text" class="todo-input" bind:value={todoText} class:error={hasError} placeholder="Note a todo..." on:focus={() => todoText = ''}/>


<!-- button add -->
  <button on:click={addTodo} class="add-button">
    <span class="material-symbols-outlined">add</span>
  </button>

  <!-- error message -->

</div>

{#if hasError}
<p class="error-message" style="margin-bottom: 1em;">Please enter a todo item.</p>
{/if}

{#each todos as todo, index}
<!-- todo -->
  <div class="todo-entry" class:done={todo.done}>
<!-- checkboxen -->
<div class="todo-checkbox-container">
  {#if todo.done}
    <input type="checkbox" bind:checked={todo.done} id="todo-{index}" class="todo-checkbox" />
  {:else}
    <input type="checkbox" bind:checked={todo.done} id="todo-{index}" class="todo-label" style="border: none; width: 13px; height: 13px; border-radius: 50%; margin-right: 0.5em; margin-top: 0.4em;color:lightgrey;"/>
  {/if}
    <label for="todo-{index}" class="todo-label">
    {#if todo.done}
      <span class="material-symbols-outlined" style="color: blue;">check</span>
    {/if}
  </label>
</div>

<!-- text -->
<div 
  style="font-family: SF Pro Text, sans-serif; font-size: 1em; margin-top: 0.2em"
  class:done={todo.done}
>{todo.text}</div>

<!-- löschen -->
<button
  class="delete"
  on:click={() => {
    remove(index);
  }}
  ><span class="material-symbols-outlined" style="color: lightgrey;" >delete</span>
</button>
</div>
{/each}

<style>
h1 {
font-family: 'SF Pro Text', sans-serif;
font-weight: 600;
font-size: 2em;
color: black;
margin-bottom: 0.5em;
}

p {
font-family: 'SF Pro Text', sans-serif;
}

.task {
font-family: 'SF Pro Text', sans-serif;
font-weight: 600;
font-size: 1em;
color: blue;
margin-bottom: 2em;
}

.input-container {
  margin-bottom: 1.5em;
  display: flex;
  position: relative;
}

.todo-input {
  font-family: 'SF Pro Text', sans-serif;
  border: 1px solid lightgray;
  border-radius: 15px;
  padding: 0.5em 1em;
  margin-right: 0.5em;
  transition: border-color 0.5s ease-in-out;
  width: 15em;
}

.add-button {
  background-color: blue;
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.material-symbols-outlined {
  color: white;
  font-variation-settings:
  'FILL' 0,
  'wght' 400,
  'GRAD' 0,
  'opsz' 48
}


.delete {
background-color: white;
border: none;
}

.delete:hover {
color: blue;
font-weight: 700;
}

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

.error-message {
color: red;
margin-top: 0;
margin-bottom: 10px;
font-size: 0.8em;
}

.error {
border: 1px solid red;
border-radius: 15px;
}

.todo-input::placeholder {
color: lightgrey;
font-size: 1em;
}

.todo-input:focus {
  border: 1px solid gray;
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

</style>
