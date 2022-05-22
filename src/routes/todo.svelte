<script>
  import { Deta } from "deta";
  import { onMount } from "svelte/internal";
  const deta = Deta(import.meta.env.VITE_DETA_KEY);
  const todos_db = deta.Base("ampletodo");

  /**
   * @type {any[]}
   */
  let all_todos = [];
  let input = "";

  async function addTodo() {}

  /**
   * @param {any} key
   */
  async function deleteTodo(key) {}

  async function getAllTodos() {
    const result = await todos_db.fetch();
    const data = await result.items;
    all_todos = await data;
  }

  onMount(async () => {
    getAllTodos();
  });
</script>

<svelte:head><title>Todos</title></svelte:head>
<div class="grid grid-flow-row justify-items-center">
  <textarea class="border-4 border-sky-500" bind:value={input} />
  <br />
  <button on:click={addTodo}>➕</button>
  <br />
  <table>
    {#each all_todos as todo}
      <tr>
        <td>{todo["todo"]}</td>
        <td class="text-red-600	">{todo["date"]}</td>
        <td><button on:click={() => deleteTodo(todo["key"])}>✔</button></td>

      </tr>
      <br />
    {/each}
  </table>
</div>
