<script>
  import { getPingData } from "../state/api";

  import NotFound from "../components/NotFound.svelte";

  export let params;
  const pingDataPromise = getPingData(params.app, params.ping);
</script>

<style>
  .table-header {
    @apply table-auto;
    @apply my-4;
  }

  .table-header td {
    @apply border;
    @apply p-2;
  }
</style>

{#await pingDataPromise then ping}
  <p>
    <a href={`/apps/${params.app}/tables/${params.ping}`}>BigQuery table</a>
  </p>
  <h1>{ping.name}</h1>
  <table class="table-header">
    <tr>
      <td>Description</td>
      <td>{ping.description}</td>
    </tr>
    <tr>
      <td>Related Bugs</td>
      <td>
        {#each ping.bugs as bug}
          {#if typeof bug === 'number'}
            <a
              class="mr-2"
              href={`https://bugzilla.mozilla.org/show_bug.cgi?id=${bug}`}>{bug}</a>
          {:else}<a class="mr-2" href={bug}>{bug}</a>{/if}
        {/each}
      </td>
    </tr>
    <tr>
      <td>Reviews Data</td>
      <td>
        {#each ping.data_reviews as review}
          <a class="mr-2" href={review}>{review}</a>
        {/each}
      </td>
    </tr>
    <tr>
      <td>Includes Client Identifier</td>
      <td>{ping.client_id ? 'Yes' : 'No'}</td>
    </tr>
    <tr>
      <td>
        Notification Email{ping.notification_emails.length > 1 ? 's' : ''}
      </td>
      <td>
        {#each ping.notification_emails as email}
          <span class="block">{email}</span>
        {/each}
      </td>
    </tr>
  </table>

  <h2>Metrics</h2>
  <ul>
    {#each ping.metrics as metric}
      <li>
        <a href={`/apps/${params.app}/metrics/${metric.name}`}>{metric.name}</a>
        <i>{metric.description}</i>
      </li>
    {/each}
  </ul>
{:catch}
  <NotFound pageName={params.ping} itemType="ping" />
{/await}
