<script>
  import { page, navigating } from "$app/stores";
  import { beforeNavigate, afterNavigate, goto } from "$app/navigation";

  const preservedSearchParam = "preserveThisParam";

  $: console.log(`$page.url: ${$page.url.toString()}`);
  $: if ($navigating)
    console.log(
      `$navigating: ${$navigating.from?.url.toString()} → ${$navigating.to?.url.toString()}`
    );

  beforeNavigate(({ from, to }) => {
    const previousParamValue = from?.url.searchParams.get(preservedSearchParam);
    if (typeof previousParamValue === "string") {
      to?.url.searchParams.set(preservedSearchParam, previousParamValue);
    }
    console.log(
      `beforeNavigate: ${from?.url.toString()} → ${to?.url.toString()}`
    );
  });

  afterNavigate(({ from, to }) => {
    console.log(
      `afterNavigate: ${from?.url.toString()} → ${to?.url.toString()}`
    );
  });

  $: addSearchParam = () => {
    const newURL = new URL($page.url);
    newURL.searchParams.set(preservedSearchParam, "true");
    goto(newURL);
  };
</script>

<main class="container">
  <slot />
  <button on:click={addSearchParam}>Add search param</button>
</main>

<style>
  .container {
    max-width: 70ex;
    margin-left: auto;
    margin-right: auto;
  }
</style>
