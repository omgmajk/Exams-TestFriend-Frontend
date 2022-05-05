<script>
  const fetchApi = (async () => {
  const response = await fetch('http://localhost:3000/api/test_case/');
  return await response.json();
})();
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
{#await fetchApi}
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