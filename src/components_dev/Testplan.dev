<script>
  const fetchApi = (async () => {
  const response = await fetch('http://localhost:3000/api/test_plan/');
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
        <th>Test Plan Name</th>
        <th>Test Plan Description</th>
      </tr>
    </thead>
    <tbody>
{#await fetchApi}
  <p>Waiting for data...</p>
{:then test_plans}
{#each test_plans as test}
      <tr>
        <th>{test.id}</th>
        <th>{test.plan_name}</th>
        <th>{test.plan_description}</th>
      </tr>
{/each}
{:catch error}
  <p>An error has occured... {error}</p>
{/await}
</tbody>
</table>
</div>
</div>