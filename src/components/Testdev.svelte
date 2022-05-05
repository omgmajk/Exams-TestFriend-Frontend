<script>
  import { onMount } from "svelte";
  import { slide } from "svelte/transition";

  let cases = [];

  onMount(async () => {
    const res = await fetch("http://localhost:3000/api/test_case/");
    cases = await res.json();
  });

  let plan_id;
  let suite_id;
  let case_name;
  let case_description;

  let hm_id;
  let edit_plan_id;
  let edit_suite_id;
  let edit_case_name;
  let edit_case_description;

  let edited_id;
  let errorMessage;

  async function refreshData() {
    const res = await fetch("http://localhost:3000/api/test_case/");
    cases = await res.json();
  }

  const addTestCase = async () => {
    try {
      const response = await fetch(`http://localhost:3000/api/test_case/`, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          plan_id: plan_id,
          suite_id: suite_id,
          case_name: case_name,
          case_description: case_description,
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
        `http://localhost:3000/api/test_case/${id}`,
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
      const response = await fetch(`http://localhost:3000/api/test_case/${id}`);
      const data = await response.json();
      hm_id = id;
      edit_plan_id = data.plan_id;
      edit_suite_id = data.suite_id;
      edit_case_name = data.case_name;
      edit_case_description = data.case_description;
      editing = true;
    } catch (error) {
      console.log(error);
    }
  };

  const editedTestCase = async () => {
    try {
      const response = await fetch(
        `http://localhost:3000/api/test_case/${hm_id}`,
        {
          method: "PUT",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            id: hm_id,
            plan_id: edit_plan_id,
            suite_id: edit_suite_id,
            case_name: edit_case_name,
            case_description: edit_case_description,
          }),
        }
      );
      const data = await response.json();
      if (data.changes) {
        refreshData();
        errorMessage = "";
        edited_id = hm_id;
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
          <th>Test Suite ID</th>
          <th>Test Case Name</th>
          <th>Test Case Description</th>
          <th>Outcome</th>
          <th>Edit Test Case</th>
          <th>Delete Test Case</th>
        </tr>
      </thead>
      <tbody>
        {#await cases}
          <p>Waiting for data...</p>
        {:then test_cases}
          {#each test_cases as test}
            <tr>
              <th>{test.id}</th>
              <th>{test.plan_id}</th>
              <th>{test.suite_id}</th>
              <td>{test.case_name}</td>
              <td>{test.case_description}</td>
              <td>Pass</td>
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
        <h2 class="text-center text-xl">Add new Test Case</h2>
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
            bind:value={suite_id}
            class="input input-bordered w-full max-w-xs"
            id="suite_id"
            type="text"
            placeholder="Test Suite ID"
          />
        </div>
        <div class="mb-2">
          <input
            bind:value={case_name}
            class="input input-bordered w-full max-w-xs"
            id="case_name"
            type="text"
            placeholder="Test Case Name"
          />
        </div>
        <div>
          <textarea
            bind:value={case_description}
            id="case_description"
            class="textarea textarea-bordered mt-2 w-full max-w-xs"
            rows="6"
            placeholder="Test Case Description"
          />
        </div>
        <div class="flex items-center justify-between">
          <button class="btn btn-primary mt-2 w-full max-w-xs" type="submit">
            Add Test Case
          </button>
        </div>
      </form>
    </div>
  </div>
{:else}
  <div class="md:flex md:justify-center mt-20">
    <div class="w-full max-w-xs">
      {#if errorMessage}
        <h2 class="text-center text-xl text-red-600">{errorMessage}</h2>
      {:else if edited_id}
        <h2 class="text-center text-xl text-green-600">
          Edited test case with id: {edited_id}
        </h2>
      {:else}
        <h2 class="text-center text-xl">Edit Test Case</h2>
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
            bind:value={edit_suite_id}
            class="input input-bordered w-full max-w-xs"
            id="suite_id"
            type="text"
            placeholder="Test Suite ID"
          />
        </div>
        <div class="mb-2">
          <input
            bind:value={edit_case_name}
            class="input input-bordered w-full max-w-xs"
            id="case_name"
            type="text"
            placeholder="Test Case Name"
          />
        </div>
        <div>
          <textarea
            bind:value={edit_case_description}
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
