<script>
  import { onMount } from "svelte";
  import { slide, fade } from "svelte/transition";

  let cases = [];

  onMount(async () => {
    const res = await fetch("http://localhost:3000/api/test_plan/");
    cases = await res.json();
  });

  let plan_name;
  let plan_description;
  let hm_id;

  let edit_id;
  let edit_plan_name;
  let edit_plan_description;
  let edited_id;
  let errorMessage;

  async function refreshData() {
    const res = await fetch("http://localhost:3000/api/test_plan/");
    cases = await res.json();
  }

  const addTestCase = async () => {
    try {
      const response = await fetch(`http://localhost:3000/api/test_plan/`, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          plan_name: plan_name,
          plan_description: plan_description,
          hm_id: hm_id,
        }),
      });
      const data = await response.json();
      if (data.changes) {
        refreshData();
        errorMessage = "";
      } else {
        errorMessage = data.error;
      }
      //console.log(data);
    } catch (error) {
      console.log(error);
    }
  };

  const deleteTestCase = async (id) => {
    try {
      const response = await fetch(
        `http://localhost:3000/api/test_plan/${id}`,
        {
          method: "DELETE",
        }
      );
      refreshData();
    } catch (error) {
      console.log(error);
    }
  };

  let editing = false;

  const editTestCase = async (id, test) => {
    try {
      const response = await fetch(`http://localhost:3000/api/test_plan/${id}`);
      const data = await response.json();
      hm_id = id;
      edit_plan_name = data.plan_name;
      edit_plan_description = data.plan_description;
      editing = true;
    } catch (error) {
      console.log(error);
    }
  };

  const editedTestCase = async () => {
    try {
      const response = await fetch(
        `http://localhost:3000/api/test_plan/${hm_id}`,
        {
          method: "PUT",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            id: hm_id,
            plan_name: edit_plan_name,
            plan_description: edit_plan_description,
          }),
        }
      );
      const data = await response.json();
      if (data.changes) {
        refreshData();
        errorMessage = "";
        edited_id = hm_id;
        editing = false;
      } else {
        errorMessage = data.error;
      }
      console.log(data);
    } catch (error) {
      console.log(error);
    }
  };
</script>

<div class="md:flex md:justify-center mt-20 mb-20">
  <div class="overflow-x-auto">
    <table class="table w-full">
      <!-- head -->
      <thead>
        <tr>
          <th>ID</th>
          <th>Test Plan Name</th>
          <th>Test Plan Description</th>
          <th>Edit</th>
          <th>Delete</th>
        </tr>
      </thead>
      <tbody>
        {#await cases}
          <p>Waiting for data...</p>
        {:then test_cases}
          {#each test_cases as test}
            <tr>
              <th>{test.id}</th>
              <th>{test.plan_name}</th>
              <td>{test.plan_description}</td>
              <td>
                <button
                  class="btn-sm btn-primary w-full max-w-xs"
                  on:click={() => editTestCase(test.id, test)}>Edit</button
                >
              </td>
              <td>
                <button
                  class="btn-sm btn-primary w-full max-w-xs"
                  on:click={() => deleteTestCase(test.id)}>Delete</button
                >
              </td>
            </tr>
          {/each}
        {:catch error}
          <p>An error has occured... {error}</p>
        {/await}
      </tbody>
    </table>
  </div>
</div>

{#if !editing}
  <div class="md:flex md:justify-center mt-20">
    <div class="w-full max-w-xs">
      {#if errorMessage}
        <h2 class="text-center text-xl text-red-600">{errorMessage}</h2>
      {:else}
        <h2 class="text-center text-xl">Add new Test Plan</h2>
      {/if}
      <form
        class="shadow-md rounded px-1 pt-6 pb-8 mb-4"
        on:submit|preventDefault={addTestCase}
      >
        <div class="mb-2">
          <input
            bind:value={plan_name}
            class="input input-bordered w-full max-w-xs"
            id="plan_name"
            type="text"
            placeholder="Test Plan Name"
          />
        </div>
        <div class="mb-2">
          <textarea
            bind:value={plan_description}
            id="case_description"
            class="textarea textarea-bordered mt-2 w-full max-w-xs"
            rows="6"
            placeholder="Test Plan Description"
          />
        </div>
        <div class="flex items-center justify-between">
          <button class="btn btn-primary mt-2 w-full max-w-xs" type="submit">
            Add Test Plan
          </button>
        </div>
      </form>
    </div>
  </div>
{:else}
  <div class="md:flex md:justify-center mt-20">
    <div class="w-full max-w-xs">
      {#if errorMessage}
        <h2 transition:fade class="text-center text-xl text-red-600">{errorMessage}</h2>
      {:else if edited_id}
        <h2 transition:fade class="text-center text-xl text-green-600">
          Edited test plan with id: {edited_id}
        </h2>
      {:else}
        <h2 class="text-center text-xl">Edit Test Plan</h2>
      {/if}
      <form
        class="shadow-md rounded px-1 pt-6 pb-8 mb-4"
        on:submit|preventDefault={editedTestCase}
      >
        <!-- <input type="hidden" id="id" bind:value{edited_id} -->
        <div class="mb-2" transition:slide>
          <input
            bind:value={hm_id}
            class="input input-bordered w-full max-w-xs"
            id="hm_id"
            type="text"
            placeholder="Test Plan ID"
          />
        </div>
        <!-- <div class="mb-2">
          <input
            bind:value={edit_id}
            class="input input-bordered w-full max-w-xs"
            id="plan_id"
            type="text"
            placeholder="Test Plan ID"
          />
        </div> -->
        <div class="mb-2">
          <input
            bind:value={edit_plan_name}
            class="input input-bordered w-full max-w-xs"
            id="plan_id"
            type="text"
            placeholder="Test Plan ID"
          />
        </div>
        <div>
          <textarea
            bind:value={edit_plan_description}
            id="case_description"
            class="textarea textarea-bordered mt-2 w-full max-w-xs"
            rows="6"
            placeholder="Test Plan Description"
          />
        </div>
        <div class="flex items-center justify-between">
          <button class="btn btn-primary mt-2 w-full max-w-xs" type="submit">
            Edit Test Plan
          </button>
        </div>
      </form>
    </div>
  </div>
{/if}
