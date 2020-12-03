<script>
  export let repo
  export let max = 10

  let maxSize

  let promise
  $: promise = (async () => {
    const response = await fetch(
      `https://api.github.com/repos/${repo}/contributors`,
    )
    if (!response.ok) {
      throw response.statusText
    }
    return await response.json()
  })()
  $: maxSize = parseInt(max)
</script>

<style>
  img {
    width: 42px;
    height: 42px;
    border-radius: 50%;
    margin-right: 0.5em;
  }
  a {
    text-decoration: none;
  }
  .placeholder {
    height: 42px;
    width: 42px;
    background-color: #bbb;
    border-radius: 50%;
    margin-right: 0.5em;
    display: inline-block;
  }
</style>

<svelte:options tag="github-contributors" immutable={true} />
{#await promise}
  {#each { length: maxSize } as _}
    <span class="placeholder" />
  {/each}
{:then contributors}
  {#each contributors.slice(0, maxSize) as contributor}
    <a href={contributor.html_url} target="_blank" rel="noopener noreferrer">
      <img src={contributor.avatar_url} alt={contributor.login} />
    </a>
  {/each}
{:catch error}
  <p>Github Repo {error}</p>
{/await}
