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
  <button on:click={addTodo}>ADD</button>
</div>

<!-- error message -->
{#if hasError}
  <p class="error-message">Please enter a todo item.</p>
{/if}

{#each todos as todo, index}
  <!-- todo -->
  <div class="todo-entry" class:done={todo.done}>
    <!-- checkboxen -->
    <input type="checkbox" bind:checked={todo.done} />

    <!-- text -->
    <div 
      style="font-family: SF Pro Text, sans-serif"
      class:done={todo.done}
    >{todo.text}</div>

    <!-- löschen -->
    <button
      class="delete"
      on:click={() => {
        remove(index);
      }}
    >X</button>
  </div>
{/each}

<style>
  h1 {
    font-family: 'SF Pro Text', sans-serif;
    font-weight: 600;
    color: black;
    margin-bottom: 0.1em;
  }

  p {
    font-family: 'SF Pro Text', sans-serif;
  }

  .task {
    font-family: 'SF Pro Text', sans-serif;
    font-weight: 600;
    color: blue;
    margin-top: 0;
    margin-bottom: 1.5em;
  }

  .input-container {
        margin-bottom: 1.5em;
  }

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

  .todo-entry:not(.done) div {
    color: black;      
  }

  .error-message {
    color: red;
    margin-top: 0;
    margin-bottom: 10px;
  }

  .error {
    outline: 2px solid red;
  }

  .todo-input::placeholder {
    color: grey;
  }
</style>
      