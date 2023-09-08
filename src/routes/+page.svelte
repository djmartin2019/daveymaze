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

	let selectedTheme = 'wintry'; // Default theme

	function changeTheme() {
		document.body.setAttribute('data-theme', selectedTheme);
	}

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
	class="flex items-center justify-center bg-gradient-to-br from-primary-800 to-secondary-400 text-white w-screen h-screen overflow-hidden"
>
	<div
		class="pt-10 pb-10 rounded-lg flex flex-col items-center bg-secondary-600 w-full md:w-1/2 shadow-lg shadow-black"
	>
		<h1 class="text-4xl md:text-6xl font-bold mb-4 text-center text-shadow text-white">
			Welcome to DaveyMaze!
		</h1>
		<p class="text-lg md:text-xl mb-8 text-center w-4/5 md:w-1/2 text-shadow font-xl text-white">
			"Dijkstra's Algorithm is a smart way to find the shortest path between two points on a map.
			Imagine you're at point A and want to get to point B. This algorithm looks at all the possible
			routes you could take and picks the quickest one for you, just like a GPS does when you're
			driving!
		</p>

		<select
			bind:value={selectedTheme}
			on:change={changeTheme}
			class="w-52 border rounded shadow-sm text-lg focus:outline-none focus:border-blue-500 text-black"
		>
			<option value="wintry">Wintry</option>
			<option value="modern">Modern</option>
			<option value="rocket">Rocket</option>
			<option value="seafoam">Seafoam</option>
		</select>

		<div
			class="flex justify-center items-center variant-filled-primary p-10 rounded-md mb-5 mt-5 shadow-md shadow-black"
		>
			<!-- Grid container -->
			<div class="grid grid-cols-[repeat(10,minmax(0,1fr))] gap-2">
				{#each grid as row, rowIndex}
					{#each row as cell, colIndex}
						<!-- Individual grid cell -->
						<div
							class="w-8 md:w-16 h-8 md:h-16 border border-gray-300 flex items-center justify-center cursor-pointer shadow-md shadow-black text-white"
							style="background-color: {cell === 'start'
								? 'green'
								: cell === 'end'
								? 'red'
								: cell === 'checked'
								? 'black'
								: cell === 'shortestPath'
								? 'lime'
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
		<div class="flex flex-wrap justify-center space-x-2 md:space-x-4 space-y-2 md:space-y-0">
			<button
				on:click={() => (currentAction = 'start')}
				class="rounded-md bg-primary-500 px-3.5 py-2.5 text-sm font-semibold text-white shadow-md hover:bg-primary-400 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-primary-500 shadow-black hover:shadow-slate-500"
			>
				Starting Position
			</button>
			<button
				on:click={() => (currentAction = 'end')}
				class="rounded-md bg-secondary-500 px-3.5 py-2.5 text-sm font-semibold text-white shadow-md hover:bg-secondary-400 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-secondary-500 shadow-black hover:shadow-slate-500"
			>
				Ending Position
			</button>
			<button
				on:click={() => dijkstra(startingPosition, endingPosition, distance)}
				class="rounded-md bg-tertiary-500 px-3.5 py-2.5 text-sm font-semibold text-white shadow-md hover:bg-tertiary-400 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-tertiary-500 shadow-black hover:shadow-slate-500"
			>
				Pathfind
			</button>
			<button
				disabled={isAlgorithmRunning}
				on:click={resetGrid}
				class="rounded-md bg-error-500 px-3.5 py-2.5 text-sm font-semibold text-white shadow-md hover:bg-error-400 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-error-500 shadow-black hover:shadow-slate-500 {isAlgorithmRunning
					? 'disabled-class'
					: 'enabled-class'}"
			>
				Reset Grid
			</button>
		</div>
	</div>
</div>

<style>
	body {
		margin: 0;
		padding: 0;
		background: linear-gradient(to right, from-secondary, to-primary);
		background-size: cover;
		background-attachment: fixed;
		min-height: 100vh;
		min-width: 100vw;
	}

	.disabled-class {
		background-color: grey;
		cursor: not-allowed;
	}
</style>
