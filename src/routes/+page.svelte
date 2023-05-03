<script>

    import {onMount} from 'svelte';

    let todoText = '';
    let todos = [];

    onMount(getTodoList(todoList));

    function saveTodoList(todoList) {
        localStorage.setItem('todoList', JSON.stringify(todoList));
    }

    function getTodoList() {
        const todoList = localStorage.getItem('todoList');
        return todoList ? JSON.parse(todoList) : [];
    }

    function addTodo() {
        todos.push({text: todoText, done: false});
        todos = todos;
        todoText = '';
        console.log(todos);
    }

    function remove(index) {
        //delete entry
        todos.splice(index,1);
        todosText = '';
        console.log('hallo');
    }

</script>

<h1>TODO APP</h1>

<!-- textfeld -->
<input type="text" class="todo-input" bind:value={todoText}/>

<!-- button add -->
<button on:click={addTodo}>ADD</button>

<!-- todo -->
{#each todos as todo, index}
    <div class="todo-entry" class:done={todo.done}>

        <!-- checkbox -->
        <input type="checkbox" bind:value={todo.done}/>
        <button
            class="delete" 
            on:click={(index) => {
                remove(index);
            }}>X</button>

        <!-- text -->
        <div>{todo.text}</div>
    </div>    
{/each}

<style>
    .delete{
        background-color: white;
        border:none;
    }

    /* .delete:hover{

    } */
    .done{
        color:grey;
    }
    .todo-entry {
        display: flex;
    }
</style>