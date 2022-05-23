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
  let date = "";

  async function addTodo() {
    const id = 99999999999999 - Date.now();
    const res = await todos_db.insert(
      { todo: input, date: date },
      id.toString()
    );
    getAllTodos();
  }

  /**
   * @param {any} key
   */
  async function completeTodo(key) {
    const res = await todos_db.delete(key);
    getAllTodos();
    date = "";
    input = "";
  }

  async function getAllTodos() {
    const result = await todos_db.fetch();
    const data = await result.items;
    const temp = await data;
    // @ts-ignore
    temp.sort(function (a, b) {
      // @ts-ignore
      if (a.date > b.data) return 1;
      else return -1;
    });
    all_todos = temp;
  }

  onMount(async () => {
    getAllTodos();
  });
</script>

<svelte:head><title>Todos</title></svelte:head>
<div class="flex flex-col">
  <textarea class="border-4 border-sky-500" bind:value={input} />
  <br />
  <input
    class="border-2 border-sky-500"
    type="date"
    placeholder="Due Date"
    bind:value={date}
  />
  <br/>
  <button on:click={addTodo}>➕</button>
  <br />
  <table>
    {#each all_todos as todo}
      <tr>
        <td class="text-2xl">{todo["todo"]}</td>
        <td class="text-red-600	text-2xl">{todo["date"]}</td>
        <td class="text-2xl"
          ><button on:click={() => completeTodo(todo["key"])}>✔</button></td
        >
      </tr>
      <br />
    {/each}
  </table>
</div>
