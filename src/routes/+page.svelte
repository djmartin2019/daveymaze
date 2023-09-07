<script>
	const rows = 10;
	const cols = 10;
	let startingPosition = null;
	let endingPosition = null;
	let currentAction = null;
	let errorMessage = null;
	let timerMessage = '';
	let timerId = null;
	let startTime = null;
	let priorityQueue = [];
	let isAlgorithmRunning = false;
	const directions = [
		[-1, 0], // Up
		[1, 0], // Down
		[0, -1], // Left
		[0, 1] // Right
	];
	let visited = new Set();

	let grid = Array.from({ length: rows }, () => Array(cols).fill(0));

	let distance = Array.from({ length: rows }, () => Array(cols).fill(Infinity));
	let parent = Array.from({ length: rows }, () => Array(cols).fill(null));

	async function dijkstra(startingPosition, endingPosition, distance) {
		if (startingPosition === null || endingPosition === null || distance === null) {
			errorMessage =
				'Missing configuration: Make sure to set both the starting and ending positions.';
			return;
		}
		startTimer();
		isAlgorithmRunning = true;
		errorMessage = null;
		priorityQueue.push({
			row: startingPosition.row,
			col: startingPosition.col,
			distance: 0
		});

		while (priorityQueue.length > 0) {
			await delay(100);
			priorityQueue.sort((a, b) => a.distance - b.distance);
			const currentCell = priorityQueue.shift();
			visited.add(`${currentCell.row},${currentCell.col}`);

			if (currentCell.row === endingPosition.row && currentCell.col === endingPosition.col) {
				break;
			}

			for (const [dx, dy] of directions) {
				const newRow = currentCell.row + dx;
				const newCol = currentCell.col + dy;

				// Skip if out of bounds or already visited
				if (
					newRow < 0 ||
					newRow >= rows ||
					newCol < 0 ||
					newCol >= cols ||
					visited.has(`${newRow},${newCol}`)
				) {
					continue;
				}

				// Calculate new distance
				const newDistance = currentCell.distance + 1;

				// Update distance if smaller
				if (newDistance < distance[newRow][newCol]) {
					distance[newRow][newCol] = newDistance;
					parent[newRow][newCol] = currentCell;

					// Add to priority queue
					priorityQueue.push({
						row: newRow,
						col: newCol,
						distance: newDistance
					});

					// Mark as checked for visualization
					cellIsChecked(newRow, newCol);
				}
			}
		}
		let pathCell = endingPosition;
		while (pathCell) {
			await delay(100);
			cellIsShortest(pathCell.row, pathCell.col);
			pathCell = parent[pathCell.row][pathCell.col];
		}
		stopTimer();
		isAlgorithmRunning = false;
	}

	function cellIsChecked(rowIndex, colIndex) {
		if (grid[rowIndex][colIndex] !== 'start' && grid[rowIndex][colIndex] !== 'end') {
			grid[rowIndex][colIndex] = 'checked';
		}
	}
	function cellIsShortest(rowIndex, colIndex) {
		if (grid[rowIndex][colIndex] !== 'start' && grid[rowIndex][colIndex] !== 'end') {
			grid[rowIndex][colIndex] = 'shortestPath';
		}
	}

	function delay(ms) {
		return new Promise((resolve) => setTimeout(resolve, ms));
	}

	function startTimer() {
		startTime = new Date().getTime();
		timerId = setInterval(() => {
			const currentTime = new Date().getTime();
			const elapsedTime = currentTime - startTime;
			const elapsedTimeInSeconds = elapsedTime / 1000;
			timerMessage = `Algorithm running for ${elapsedTimeInSeconds} seconds`;
		}, 1);
	}

	function stopTimer() {
		clearInterval(timerId);
		timerId = null;
		const endTime = new Date().getTime();
		const elapsedTime = endTime - startTime;
		const elapsedTimeInSeconds = elapsedTime / 1000;
		timerMessage = `Algorithm ran for ${elapsedTimeInSeconds} seconds`;
	}

	function setPoint(rowIndex, colIndex) {
		if (currentAction === 'start') {
			if (startingPosition) {
				grid[startingPosition.row][startingPosition.col] = 'empty';
				distance[startingPosition.row][startingPosition.col] = 0;
			}
			startingPosition = { row: rowIndex, col: colIndex };
			grid[rowIndex][colIndex] = 'start';
		} else if (currentAction === 'end') {
			if (endingPosition) {
				grid[endingPosition.row][endingPosition.col] = 'empty';
			}
			endingPosition = { row: rowIndex, col: colIndex };
			grid[rowIndex][colIndex] = 'end';
		}
	}

	function resetGrid() {
		grid = Array.from({ length: rows }, () => Array(cols).fill(0));
		distance = Array.from({ length: rows }, () => Array(cols).fill(Infinity));

		priorityQueue = [];
		visited.clear();

		startingPosition = null;
		endingPosition = null;
	}
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
							class="w-16 h-16 border border-gray-300 flex items-center justify-center cursor-pointer"
							style="background-color: {cell === 'start'
								? 'blue'
								: cell === 'end'
								? 'red'
								: cell === 'checked'
								? 'black'
								: cell === 'shortestPath'
								? 'green'
								: 'white'}"
							on:click={() => setPoint(rowIndex, colIndex)}
						>
							{rowIndex},{colIndex}
						</div>
					{/each}
				{/each}
			</div>
		</div>
		<div class="flex justify-center pb-5">
			{#if errorMessage}
				<span class="text-red-500 font-bold text-xl">{errorMessage}</span>
			{/if}
			{#if timerMessage}
				<span class="text-white-500 font-bold text-xl">{timerMessage}</span>
			{/if}
		</div>
		<div class="flex justify-center space-x-4">
			<button
				on:click={() => (currentAction = 'start')}
				class="variant-filled-secondary hover:variant-filled-secondary-800 text-white font-bold py-2 px-4 rounded"
			>
				Starting Position
			</button>
			<button
				on:click={() => (currentAction = 'end')}
				class="variant-filled-primary hover:variant-filled-primary-800 text-white font-bold py-2 px-4 rounded"
			>
				Ending Position
			</button>
			<button
				on:click={() => dijkstra(startingPosition, endingPosition, distance)}
				class="variant-filled-error hover:variant-filled-error-800 text-white font-bold py-2 px-4 rounded"
			>
				Pathfind
			</button>
			<button
				disabled={isAlgorithmRunning}
				on:click={resetGrid}
				class="variant-filled-error text-white font-bold py-2 px-4 rounded {isAlgorithmRunning
					? 'disabled-class'
					: 'enabled-class'}"
			>
				Reset Grid
			</button>
		</div>
	</div>
</div>

<style>
	.disabled-class {
		background-color: grey;
		cursor: not-allowed;
	}

	.enabled-class {
		background-color: red;
		cursor: pointer;
	}
</style>
