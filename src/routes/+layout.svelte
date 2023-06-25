<script>
	import { Button } from 'carbon-components-svelte';
	import Header from '../Components/Header.svelte';
</script>

<div class="flex flex-col items-start min-w-[100vw] h-[100vh]" style="border: 1px solid green;">
	<Header />

	<div class="flex flex-col items-start min-w-[100vw] px-4">
		<slot />
	</div>

	<div class="flex flex-col justify-center w-full px-4 h-full max-h-[40vh]" style="border: 1px solid purple;">
		<Button class="bg-blue-500 hover:bg-blue-600 h-14 text-white font-bold w-full mb-4 text-lg"
			>Request Truck</Button
		>
		<Button kind="tertiary" class="w-full h-14 text-lg">Learn More</Button>
	</div>
</div>
