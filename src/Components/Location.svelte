<script lang="ts">
	import { onMount } from 'svelte';
	import { Button } from 'carbon-components-svelte';

	let latitude: string;
	let longitude: string;

	const getLocation = () => {
		if (navigator.geolocation) {
			navigator.geolocation.getCurrentPosition(
				(position) => {
					latitude = position.coords.latitude.toString();
					longitude = position.coords.longitude.toString();
				},
				(error) => {
					console.error('Error getting location:', error);
				}
			);
		} else {
			console.error('Geolocation is not supported by this browser.');
		}
	};

	onMount(() => {
		// You can also call getLocation() here to get the location on component mount
	});
</script>

<div class="h-full max-h-[40vh] pt-6">
	<Button
		kind="tertiary"
		class="text-[#4c4c4c] text-lg text-medium color-[#4c4c4c] h-14"
		on:click={getLocation}>Get Location</Button
	>
	<div class="mt-6 h-20">
		{#if latitude && longitude}
			<p>Latitude: {latitude}</p>
			<p>Longitude: {longitude}</p>
		{/if}
	</div>
</div>
