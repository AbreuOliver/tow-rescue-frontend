<script lang="ts">
	import { onMount } from 'svelte';
	import { Button } from 'carbon-components-svelte';
    import { config } from 'dotenv';
    config();

	let latitude: string | null = null;
	let longitude: string | null = null;
	let requestStatus: string | null = null;

	let city: string = '';
	let county: string = '';

	const mapboxAccessToken: string = process.env.MAPBOX_TOKEN!;

	const getLocation = () => {
		if (navigator.geolocation) {
			navigator.geolocation.getCurrentPosition(
				(position) => {
					latitude = position.coords.latitude.toString();
					longitude = position.coords.longitude.toString();
                    console.log("About to get location data")
					fetchLocationData();
				},
				(error) => {
					console.error('Error getting location:', error);
				}
			);
		} else {
			console.error('Geolocation is not supported by this browser.');
		}
	};

	const fetchLocationData = () => {
		const url: string = `https://api.mapbox.com/geocoding/v5/mapbox.places/${longitude},${latitude}.json?access_token=${mapboxAccessToken}`;

		fetch(url)
			.then((response) => response.json())
			.then((data) => {
				// Extract city and county from the response
				const features = data.features;
				if (features.length > 0) {
					// Get the first feature in the array
					const feature = features[0];

					// Check for city and county context
					const cityContext = feature.context.find((context: { id: string }) =>
						context.id.includes('place')
					);
					const countyContext = feature.context.find((context: { id: string }) =>
						context.id.includes('district')
					);

					// Assign city and county values
					city = cityContext ? cityContext.text : '';
					county = countyContext ? countyContext.text : '';

					console.log('City:', city);
					console.log('County:', county);
				}
			})
			.catch((error) => {
				console.error(error);
			});
	};

	const sendDataToFirebase = () => {
		const firebaseEndpoint = 'https://tow-rescue-default-rtdb.firebaseio.com/data.json';
		const newData = {
			latitude,
			longitude
		};

		fetch(firebaseEndpoint, {
			method: 'POST',
			body: JSON.stringify(newData)
		})
			.then((response) => {
				if (response.ok) {
					requestStatus = 'success';
					console.log('Data sent to Firebase successfully!');
				} else {
					requestStatus = 'error';
					console.error('Failed to send data to Firebase:', response.status);
				}
			})
			.catch((error) => {
				requestStatus = 'error';
				console.error('Error sending data to Firebase:', error);
			});
	};

	onMount(() => {
        console.log("Before")
		getLocation();
        console.log("After")
	});

	let shortenedLatitude: string | null = null;
	let shortenedLongitude: string | null = null;

	$: {
		// Calculate the shortened latitude and longitude values
		if (latitude && longitude) {
			shortenedLatitude = latitude.slice(0, latitude.indexOf('.') + 5);
			shortenedLongitude = longitude.slice(0, longitude.indexOf('.') + 5);
		}
	}
</script>

<div class="h-96 max-h-[80vh] w-full pt-8 flex items-end">
	<div class="p-[calc(8px + 1.5625vw)] flex flex-col pt-12 flex items-center">
		<Button
			kind="tertiary"
            class="text-white border-inherit text-bold text-lg w-full pl-8 text-white h-14 rounded-[50px] backdrop-blur-sm"
			on:click={sendDataToFirebase}
		>
			View Tow Trucks Near Me
		</Button>
		<div class="mt-6 h-20">
			{#if requestStatus === 'success'}
				<p>Your current location:</p>
				<p>
					{latitude?.slice(0, latitude.indexOf('.') + 5)}, {longitude?.slice(
						0,
						longitude.indexOf('.') + 5
					)}
				</p>
				<p>was sent to Firebase successfully!</p>
			{/if}
			<!-- {#if city && county} -->
			<p class="text-white">{city}, {county}</p>
			<!-- {/if} -->
		</div>
	</div>
</div>
