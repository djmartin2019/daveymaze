<script>
	// Define the size of the grid
	const rows = 10;
	const cols = 10;

	// Create a 2D array to represent the grid
	let grid = Array.from({ length: rows }, () => Array(cols).fill(0));
</script>

<div
	class="flex items-center justify-center min-h-screen min-w-screen bg-gradient-to-br variant-gradient-secondary-tertiary text-white"
>
	<div class="bg-opacity-50 backdrop-blur-md p-8 rounded-lg">
		<h1 class="text-6xl font-bold mb-4 text-center">Welcome to DaveyMaze!</h1>
		<p class="text-xl mb-8 text-center">This is a pathfinding visualizer built with Svelte.</p>
		<div class="flex justify-center items-center variant-filled-primary p-10 rounded-md mb-5 mt-5">
			<!-- Grid container -->
			<div class="grid grid-cols-[repeat(10,minmax(0,1fr))] gap-2">
				{#each grid as row, rowIndex}
					{#each row as cell, colIndex}
						<!-- Individual grid cell -->
						<div
							class="w-16 h-16 border border-gray-300 flex items-center justify-center text-white"
							style="background-color: {cell === 0 ? 'white' : 'black'}"
							on:click={() => (grid[rowIndex][colIndex] = grid[rowIndex][colIndex] === 0 ? 1 : 0)}
						>
							{rowIndex},{colIndex}
						</div>
					{/each}
				{/each}
			</div>
		</div>

		<div class="flex justify-center space-x-4">
			<button
				class="variant-filled-primary hover:variant-glass-primary-800 text-white font-bold py-2 px-4 rounded"
			>
				Get Started
			</button>
			<button
				class="variant-filled-secondary hover:variant-glass-secondary-800 text-white font-bold py-2 px-4 rounded"
			>
				Learn More
			</button>
		</div>
	</div>
</div>

<style>
	/* Additional styles can go here */
</style>
