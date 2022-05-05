<script>
  const fetchApi = (async () => {
  const response = await fetch('http://localhost:3000/api/test_suite/');
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
        <th>Suite Name</th>
        <th>Suite Goals</th>
        <th>Suite Description</th>
      </tr>
    </thead>
    <tbody>
{#await fetchApi}
  <p>Waiting for data...</p>
{:then test_suites}
{#each test_suites as test}
      <tr>
        <th>{test.id}</th>
        <th>{test.plan_id}</th>
        <th>{test.suite_name}</th>
        <th>{test.suite_goal}</th>
        <th>{test.suite_description}</th>
      </tr>
{/each}
{:catch error}
  <p>An error has occured... {error}</p>
{/await}
</tbody>
</table>
</div>
</div>