<script>
	import Header from '../Components/Header.svelte';
</script>

<div class="flex flex-col w-[100vw] items-center overflow-hidden">
	<div class="h-full max-w-[600px] border-2 image-container">
		<Header />
		<div class="flex flex-col items-start min-w-[100vw] h-full px-4">
			<slot />
		</div>
	</div>
</div>

<style>
	.image-container {
		background-image: url('../Images/background.jpg');
		background-size: cover;
		background-position: center;
		height: 100vh;
		opacity: 0.9;
		overflow-y: none;
	}
</style>
