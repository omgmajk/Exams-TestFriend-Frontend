<script>
  import { onMount } from "svelte";

  let cases = [];

  onMount(async () => {
    const res = await fetch("http://localhost:3000/api/test_case/");
    cases = await res.json();
  });

  let plan_id;
  let suite_id;
  let case_name;
  let case_description;

  let errorMessage;

  async function refreshData() {
    const res = await fetch('http://localhost:3000/api/test_case/');
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
        errorMessage = '';
      } else {
        errorMessage = data.error;
      }

      //console.log(data);
    } catch (error) {
      //console.log(error);
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

  const editTestCase = async (id) => {
    try {
      const response = await fetch(
        `http://localhost:3000/api/test_case/${id}`,
        {
          method: "PUT",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            plan_id: plan_id,
            suite_id: suite_id,
            case_name: case_name,
            case_description: case_description,
          }),
        }
      );

      console.log(await response.json());
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
          <th>Delete Test Case</th>
          <th>Edit Test Case</th>
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
              <td
                ><button
                  class="btn-sm btn-primary w-full max-w-xs"
                  on:click={() => editTestCase(test.id)}>Edit</button
                ></td
              >
              <td
                ><button
                  class="btn-sm btn-primary w-full max-w-xs"
                  on:click={() => deleteTestCase(test.id)}>Delete</button
                ></td
              >
            </tr>
          {/each}
        {:catch error}
          <p>An error has occured... {error}</p>
        {/await}
      </tbody>
    </table>
  </div>
</div>

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
          class="input input-bordered w-full max-w-xs"
          id="plan_id"
          type="text"
          bind:value={plan_id}
          placeholder="Test Plan ID"
        />
      </div>
      <div class="mb-2">
        <input
          class="input input-bordered w-full max-w-xs"
          id="suite_id"
          type="text"
          bind:value={suite_id}
          placeholder="Test Suite ID"
        />
      </div>
      <div class="mb-2">
        <input
          class="input input-bordered w-full max-w-xs"
          id="case_name"
          type="text"
          bind:value={case_name}
          placeholder="Test Case Name"
        />
      </div>
      <div>
        <textarea
          id="case_description"
          class="textarea textarea-bordered mt-2 w-full max-w-xs"
          rows="6"
          bind:value={case_description}
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
