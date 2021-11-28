<script>
import Timer from "./Timer.svelte";

</script>

<main>
	<div class="container mx-auto px-8 min-w-min xl:px-60 sm:px-0">
		<h1 class="bg-gray-700 pb-2 rounded-t-xl border-t-4 border-solid border-gray-900">Pomodoro timer 🍅</h1>
		<Timer/>
	</div>

</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;

	}
	:global(body) {
		background-color: rgb(31, 41, 55);
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 3em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>