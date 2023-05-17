<script>
  import { onMount, onDestroy } from 'svelte';
  import { browser } from '$app/environment';

  let todoText = '';
  let todos = [];
  let hasError = false;
  let numberOfTodos = 0;
  let deletedTodos = [];
  let showDeletedTodos = false;

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
    // todos.unshift({ text: todoText, done: false }); // add new todo to the beginning of the array
    // todos = todos;
    todos = [{ text: todoText, done: false }, ...todos]; // create a new array reference
    todoText = '';
    hasError = false;
    numberOfTodos++; // increase numberOfTodos when a new todo is added
    
    // Save todos to local storage
    saveTodos();
  }

  function remove(index) {
    // delete entry
    const deletedTodo = todos.splice(index, 1)[0];
    deletedTodos.unshift(deletedTodo); 
    // todos = todos;
    todos = [...todos]; // create a new array reference
    numberOfTodos--; // decrease numberOfTodos when a todo is removed
    saveTodos();
  }
  
  function restoreDeleted(index) {
  if (index >= 0 && index < deletedTodos.length) {
    const restoredTodo = deletedTodos.splice(index, 1)[0];
    if (!restoredTodo.hasOwnProperty('done')) {
      restoredTodo.done = false;
    }
    todos = [restoredTodo, ...todos];
    numberOfTodos++;
    saveTodos();

    if (deletedTodos.length === 0) {
      showDeletedTodos = false;
    }
  }
  showDeletedTodos = false; // Schließe den "Deleted Todos"-Bereich nach der Wiederherstellung
}




  function toggleDeletedTodos() {
  showDeletedTodos = !showDeletedTodos;
  saveTodos();
  }

  function removeAll() {
  deletedTodos = []; // Leere die deletedTodos-Liste
  saveTodos();
}



</script>

<div class='todo-list'>
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

<!-- button show deleted todos -->
<button on:click={toggleDeletedTodos} class="restore-button" style="border: 1px solid blue; border-radius: 30px;">
  <span class="material-symbols-outlined" style="color: blue;">delete</span>
</button>


</div>

<!-- error message -->
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
  ><span class="material-symbols-outlined" style="color: lightgrey;">cancel</span>
</button>
</div>
{/each}


{#if showDeletedTodos}
<div class="deleted-todos">
  <div class="remove-all-deleted-todos">
<h3>Deleted Todos</h3>
  <!-- button remote all -->
  <button on:click={removeAll} class="remote-all-button">
    Remote All
</button>
</div>

  {#if deletedTodos.length > 0}
    {#each deletedTodos as todo, index}
      <div class="todo-entry" class:done={todo.done}>
        <!-- text -->
        <div style="font-family: SF Pro Text, sans-serif; font-size: 1em; margin-top: 0.2em; color: black;" class:done={todo.done}>{todo.text}</div>

        <!-- wiederherstellen -->
        <button class="restore" on:click={() => restoreDeleted(index)}>
          <span class="material-symbols-outlined" style="color: blue;">undo</span>
        </button>

      </div>
    {/each}
  {:else}
    <p>No deleted todos.</p>
  {/if}
</div>
{/if}
</div>

<style>
h1 {
font-family: 'SF Pro Text', sans-serif;
font-weight: 600;
font-size: 2em;
color: black;
margin-bottom: 0.5em;
}

h3 {
    font-family: 'SF Pro Text', sans-serif;
    font-weight: 600;
    font-size: 1em;
    color: black;
    margin-bottom: 2em;
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
  margin-right: 0.5em;
}

.remove-all-deleted-todos{
  display: flex;
   align-items: center;
}
.remove-all-deleted-todos h3{
  margin-right: 4em;
}

.remote-all-button{
  color: blue;
  background-color: white;
  border: none;
  margin-bottom: 2em;
  margin-top: 1em;
}

.remote-all-button:hover{
  color: blue;
  text-decoration: underline;
  background-color: white;
  border: none;
}

.restore {
  background-color: white;
  border: none;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.restore-button {
  background-color: white;
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.restore-button:hover {
  color: blue;
  font-weight: 700;
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
  display: flex;
  justify-content: center;
  align-items: center;
}

.delete:hover {
color: blue;
font-weight: 700;
}

.deleted-todos{
  position: absolute;
  border: 1px solid lightgrey;
  border-radius: 0 15px 15px 15px;
  display: inline-block;
  width: auto;
  min-width: 250px;
  padding: 1em;
  left: 19.3em;
  top: 12em
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

.todo-list{
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

</style>
