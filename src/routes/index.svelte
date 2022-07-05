<script context="module">
	export async function load({ fetch }) {
		const url = 'https://api.covid19api.com/summary';
		const response = await fetch(url);
		const data = await response.json();
		return {
			props: {
				data
			}
		};
	}
</script>

<script>
	export let data;
	let filter = 'Global';
	let search = '';
	let filtered = data.Countries;
	const locale = {
		NewConfirmed: 'New Confirmed',
		TotalConfirmed: 'Total Confirmed',
		NewDeaths: 'New Deaths',
		TotalDeaths: 'Total Deaths'
	};

	$: {
		if (search !== '') {
			filtered = data.Countries.filter((country) => {
				return country.Country.toLowerCase().includes(search.toLowerCase());
			});
		} else {
			filtered = data.Countries;
		}
	}
</script>

<svelte:head>
	<title>Today | COVID-19 Statistics</title>
</svelte:head>

<div class="flex flex-col text-center">
	<h1 class="text-xl font-bold uppercase">Summary</h1>
	<h2 class="text-lg font-semibold italic">{filter}</h2>
	<div class="dropdown mx-auto">
		<label tabindex="0" class="btn m-1"><i class="fas fa-filter pr-2" /> Filter</label>
		<ul tabindex="0" class="dropdown-content menu p-2 shadow bg-base-100 rounded-box w-32">
			<li>
				<button
					on:click={() => {
						filter = 'Global';
					}}>Global</button
				>
			</li>
			<li>
				<button
					on:click={() => {
						filter = 'Countries';
					}}>Countries</button
				>
			</li>
		</ul>
	</div>
</div>

{#if filter === 'Countries'}
	<input
		type="text"
		placeholder="🔍 Search"
		class="input input-bordered w-full max-w-xs block mx-auto mt-3"
		bind:value={search}
	/>
{/if}

<div class="flex flex-col gap-10 mt-5">
	{#if filter === 'Global'}
		<div class="flex flex-col mb-5 items-center">
			<div class="stats stats-vertical lg:stats-horizontal shadow bg-base-200 w-fit">
				{#each Object.entries(data.Global) as [stat, value]}
					{#if stat in locale}
						<div class="stat">
							<div class="stat-title">{locale[stat]}</div>
							<div class="stat-value">{value.toLocaleString('en-us')}</div>
						</div>
					{/if}
				{/each}
			</div>
		</div>
	{:else if filter === 'Countries'}
		<div class="flex flex-row flex-wrap gap-10 justify-center">
			{#each filtered as country}
				<div class="flex flex-col">
					<h2 class="text-center mb-3 text-lg font-semibold"><a class="hover:underline hover:text-gray-700" href="/country/{country.Slug}">{country.Country}</a></h2>
					<div class="stats stats-vertical lg:stats-horizontal shadow bg-base-200">
						{#each Object.entries(country) as [stat, value]}
							{#if stat in locale}
								<div class="stat">
									<div class="stat-title">{locale[stat]}</div>
									<div class="stat-value">{value.toLocaleString('en-us')}</div>
								</div>
							{/if}
						{/each}
					</div>
				</div>
			{/each}
		</div>
	{/if}
</div>
