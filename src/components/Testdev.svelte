<script>
  import { onMount } from 'svelte';

  let cases = [];

  onMount(async () => {
    const res = await fetch('http://localhost:3000/api/test_case/');
    cases = await res.json();
  });

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
      <h2 class="text-center text-xl">Add new Test Case</h2>
      <form class="shadow-md rounded px-1 pt-6 pb-8 mb-4">
          <div class="mb-2">
            <input
                class="input input-bordered w-full max-w-xs"
                id="plan_id"
                type="text"
                placeholder="Test Plan ID"
            />
          </div>
          <div class="mb-2">
            <input
                class="input input-bordered w-full max-w-xs"
                id="Test Suite ID"
                type="text"
                placeholder="Test Suite ID"
            />
          </div>
          <div class="mb-2">
            <input
                class="input input-bordered w-full max-w-xs"
                id="case_name"
                type="text"
                placeholder="Test Case Name"
            />
          </div>
          <div>
            <textarea 
              class="textarea textarea-bordered mt-2 w-full max-w-xs"
              rows="6" 
              placeholder="Test Case Description"></textarea>
          </div>
          <div class="flex items-center justify-between">
              <button
                  class="btn btn-primary mt-2 w-full max-w-xs"
                  type="button"
              >
                  Add Test Case
              </button>
          </div>
      </form>
  </div>
</div>
