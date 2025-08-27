
<script>
    import { onMount } from 'svelte';
    import './todo.css';


  
    let todos;
    let newTodo = "";

    // Load todos from localStorage on mount (browser only) if not browser then we skip this part of the code
    onMount(() => {
        if (typeof window !== "undefined") {
            const saved = localStorage.getItem('todos');
            todos = saved ? JSON.parse(saved) : [];
        }
    });

    // Save todos to localStorage whenever they change (browser only) if not browser skip here also
    $: if (typeof window !== "undefined" && todos) {
        localStorage.setItem('todos', JSON.stringify(todos));
    }

    function addTodo() {
        if (newTodo.trim() !== "") {
            todos = [...todos, { text: newTodo, completed: false }];
            newTodo = "";
        }
    }

    /** @param {number} index */
    function deleteTodo(index) {
        todos = todos.filter((_, i) => i !== index);
    }

    /** @param {number} index */
    function toggleComplete(index) {
        todos = todos.map((todo, i) => i === index ? 
        { ...todo, completed: !todo.completed } 
        : todo);
    }
</script>


<h1 class="head">To-Do List</h1>

<div class="input-container">
  <input type="text" bind:value={newTodo} placeholder="Add a new task..." on:keydown={(e) => e.key === 'Enter' && addTodo()} />
  <button on:click={addTodo}>Add</button>
</div>

{#if todos}
<ul class="todo-list">
  {#each todos as todo, i}
    <li class="todo-item {todo.completed ? 'completed' : ''}">
      <input type="checkbox" checked={todo.completed} on:change={() => toggleComplete(i)} />
      {todo.text}
      <button class="delete-btn" on:click={() => deleteTodo(i)} title="Delete">🗑️</button>
    </li>
  {/each}
</ul>
{/if}