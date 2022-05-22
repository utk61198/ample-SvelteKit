<script>
  // @ts-nocheck

  import { Deta } from "deta";
  import { onMount } from "svelte/internal";
  const deta = Deta(import.meta.env.VITE_DETA_KEY);
  const notes_db = deta.Base("amplenotes");

  /**
   * @type {any[]}
   */
  let all_notes = [];
  let input = "";
  const getAllNotes = async () => {
    const result = await notes_db.fetch();
    const data = await result.items;
    all_notes = await data;
  };

  const addNote = async () => {
    const id = 99999999999999 - Date.now();
    const res = await notes_db.insert({ note: input }, id.toString());
    getAllNotes();
  };

  const deleteNote = async (key) => {
    const res = await notes_db.delete(key);
    getAllNotes();
  };

  onMount(async () => {
    // @ts-ignore
    getAllNotes();
    console.log(all_notes);
  });
</script>

<svelte:head><title>Notes</title></svelte:head>
<div class="grid grid-flow-row justify-items-center">
  <textarea class="border-4 border-sky-500" bind:value={input} />
  <br />
  <button on:click={addNote}>➕</button>
  <br />

  {#each all_notes as note}
    <div class="flex justify-center self-start">
      {note["note"]} &nbsp;<button on:click={() => deleteNote(note["key"])}
        >❌</button
      >
    </div>

    <br />
  {/each}
</div>
