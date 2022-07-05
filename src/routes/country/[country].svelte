<script context="module">
	export async function load({ fetch, params }) {
		const today = new Date();
		const startDate = new Date(today.getFullYear(), today.getMonth(), today.getDate() - 7);
		const endDate = new Date(today.getFullYear(), today.getMonth(), today.getDate());

		const url = `https://api.covid19api.com/country/${params.country}?from=${startDate}&to=${endDate}`;
		const response = await fetch(url);
		const data = await response.json();
		return {
			props: {
				data
			}
		};
	}
</script>