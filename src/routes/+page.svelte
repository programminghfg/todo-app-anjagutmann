<script>
        import { onMount } from 'svelte';
        import { browser } from '$app/environment';
      
        let todoText = '';
        let todos = [];
        let hasError = false;
      
        onMount(() => {
          if (browser) {
            const storedTodos = JSON.parse(localStorage.getItem('todos'));
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
          if (todoText.trim() === '') {
            hasError = true;
            return;
          }
          todos.unshift({ text: todoText, done: false }); // add new todo to the beginning of the array
          todos = todos;
          todoText = '';
          hasError = false;
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
      
      <h1>Today</h1>
      
      <div class="input-container">
        <!-- textfeld -->
        <input type="text" class="todo-input" bind:value={todoText} class:error={hasError} />
      
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
        }
      
        p {
          font-family: 'SF Pro Text', sans-serif;
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
      </style>
      