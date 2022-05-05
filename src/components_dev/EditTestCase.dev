<script>
  let plan_id;
  let suite_id;
  let case_name;
  let case_description;

  const editTestCase = async (id, test) => {
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
    } catch (error) {
      console.log(error);
    }
  };
</script>

<div class="md:flex md:justify-center mt-20">
  <div class="w-full max-w-xs">
    <h2 class="text-center text-xl">Add new Test Case</h2>
    <form
      class="shadow-md rounded px-1 pt-6 pb-8 mb-4"
      on:submit|preventDefault={editTestCase}
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
