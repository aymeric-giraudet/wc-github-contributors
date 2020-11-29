<svelte:options tag="github-contributors" immutable={true} />

<script>
	export let repo;
	export let maxSize = 10;
	
	let promise;
	$: promise = (async () => {
		const response = await fetch(`https://api.github.com/repos/${repo}/contributors`);
		if(!response.ok) {
			throw response.statusText;
		}
		return await response.json();
	})();
</script>

{#await promise}
	{#each {length: maxSize} as _}
		<span class="placeholder"></span>
	{/each}
{:then contributors}
		{#each contributors.slice(0, maxSize) as contributor}
			<a href={contributor.html_url} target="_blank"><img src={contributor.avatar_url} alt={contributor.login}></a>
		{/each}
{:catch error}
	<p>Github Repo {error}</p>
{/await}

<style>
	img{
		width: 42px;
		height: 42px;
		border-radius: 50%;
		margin-right: .5em;
	}
	.placeholder {
		height: 42px;
		width: 42px;
		background-color: #bbb;
		border-radius: 50%;
		margin-right: .5em;
		display: inline-block;
}
</style>