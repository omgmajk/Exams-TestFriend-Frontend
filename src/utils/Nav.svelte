<script context="module">
  // main navigation for the application
  import Testsuite from '../components/Testsuite.svelte';
  import Testplan from  '../components/Testplan.svelte';
  import Testcase from  '../components/Testcase.svelte';
  //import Testdev from   '../components/Testdev.svelte';

  // these are our 'pages' to navigate through (in order for this export to work the script tag MUST include context="module")
  export const navOptions = [
      { page: 'Test Plan',   component: Testplan },
      { page: 'Test Suite',  component: Testsuite },
      { page: 'Test Case',   component: Testcase },
      //{ page: 'Test Dev',    component: Testdev }
      // other navigation pages can go here
  ];
  </script>