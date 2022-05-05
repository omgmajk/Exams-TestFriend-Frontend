<script>
  import { onMount } from "svelte";
  import { slide, fade } from "svelte/transition";

  let cases = [];
  onMount(async () => {
    const res = await fetch("http://localhost:3000/api/test_suite/");
    cases = await res.json();
  });

  let plan_id;
  let suite_name;
  let suite_goal;
  let suite_description;

  let hm_id;
  let edit_plan_id;
  let edit_suite_name;
  let edit_suite_goal;
  let edit_suite_description;

  let edited_id;
  let errorMessage;

  async function refreshData() {
    const res = await fetch("http://localhost:3000/api/test_suite/");
    cases = await res.json();
  }

  const addTestCase = async () => {
    try {
      const response = await fetch(`http://localhost:3000/api/test_suite/`, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          plan_id: plan_id,
          suite_name: suite_name,
          suite_goal: suite_goal,
          suite_description: suite_description,
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
        `http://localhost:3000/api/test_suite/${id}`,
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
      const response = await fetch(
        `http://localhost:3000/api/test_suite/${id}`
      );
      const data = await response.json();
      hm_id = id;
      edit_plan_id = data.plan_id;
      edit_suite_name = data.suite_name;
      edit_suite_goal = data.suite_goal;
      edit_suite_description = data.suite_description;
      editing = true;
    } catch (error) {
      console.log(error);
    }
  };

  const editedTestCase = async () => {
    try {
      const response = await fetch(`http://localhost:3000/api/test_suite/${hm_id}`, {
        method: "PUT",
        headers: {
        "Content-Type": "application/json"
        },
        body: JSON.stringify({
          id: hm_id,
          plan_id: edit_plan_id,
          suite_name: edit_suite_name,
          suite_goal: edit_suite_goal,
          suite_description: edit_suite_description
        }),
      });
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
          <th>Test Plan ID</th>
          <th>Suite Name</th>
          <th>Suite Goals</th>
          <th>Suite Description</th>
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
              <td>{test.id}</td>
              <td>{test.plan_id}</td>
              <td>{test.suite_name}</td>
              <td>{test.suite_goal}</td>
              <td>{test.suite_description}</td>
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
        <h2 class="text-center text-xl">Add new Test Suite</h2>
      {/if}
      <form
        class="shadow-md rounded px-1 pt-6 pb-8 mb-4"
        on:submit|preventDefault={addTestCase}
      >
        <div class="mb-2">
          <input
            bind:value={plan_id}
            class="input input-bordered w-full max-w-xs"
            id="plan_id"
            type="text"
            placeholder="Test Plan ID"
          />
        </div>
        <div class="mb-2">
          <input
            bind:value={suite_name}
            class="input input-bordered w-full max-w-xs"
            id="suite_id"
            type="text"
            placeholder="Test Suite Name"
          />
        </div>
        <div class="mb-2">
          <input
            bind:value={suite_goal}
            class="input input-bordered w-full max-w-xs"
            id="case_name"
            type="text"
            placeholder="Test Suite Goals"
          />
        </div>
        <div>
          <textarea
            bind:value={suite_description}
            id="case_description"
            class="textarea textarea-bordered mt-2 w-full max-w-xs"
            rows="6"
            placeholder="Test Suite Description"
          />
        </div>
        <div class="flex items-center justify-between">
          <button class="btn btn-primary mt-2 w-full max-w-xs" type="submit">
            Add Test Suite
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
          Edited test suite with id: {edited_id}
        </h2>
      {:else}
        <h2 class="text-center text-xl">Edit Test Suite</h2>
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
            placeholder="Test ID"
          />
        </div>
        <div class="mb-2">
          <input
            bind:value={edit_plan_id}
            class="input input-bordered w-full max-w-xs"
            id="plan_id"
            type="text"
            placeholder="Test Plan ID"
          />
        </div>
        <div class="mb-2">
          <input
            bind:value={edit_suite_name}
            class="input input-bordered w-full max-w-xs"
            id="suite_id"
            type="text"
            placeholder="Test Suite ID"
          />
        </div>
        <div class="mb-2">
          <input
            bind:value={edit_suite_goal}
            class="input input-bordered w-full max-w-xs"
            id="case_name"
            type="text"
            placeholder="Test Case Name"
          />
        </div>
        <div>
          <textarea
            bind:value={edit_suite_description}
            id="case_description"
            class="textarea textarea-bordered mt-2 w-full max-w-xs"
            rows="6"
            placeholder="Test Case Description"
          />
        </div>
        <div class="flex items-center justify-between">
          <button class="btn btn-primary mt-2 w-full max-w-xs" type="submit">
            Edit Test Case
          </button>
        </div>
      </form>
    </div>
  </div>
{/if}
