# DaveyMaze: A Pathfinding Visualizer

## Overview

DaveyMaze is a web-based pathfinding visualizer built with Svelte. It allows users to visualize the shortest path between two points on a grid using popular algorithms like Dijkstra's and A*.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Algorithm Explanation](#algorithm-explanation)
- [Additional Features](#additional-features)
- [Future Enhancements](#future-enhancements)
- [Contributing](#contributing)
- [License](#license)

## Features

### Core Features

1. **Grid Creation**: Create a customizable grid to place start and end points.
2. **Obstacle Placement**: Add obstacles to the grid to make the pathfinding more challenging.
3. **Algorithm Selection**: Choose between Dijkstra's and A* algorithms.
4. **Pathfinding Execution**: Run the selected algorithm and visualize the process.
5. **Path Display**: View the shortest path once the algorithm finishes.

### Additional Features

1. **Heuristic Selection**: For A*, choose between different heuristics like Manhattan and Euclidean distance.
2. **Step-by-Step Execution**: Manually step through the algorithm to understand its inner workings.
3. **Performance Metrics**: View time and space complexity metrics for each algorithm run.
4. **Mobile Responsiveness**: The UI scales well on various devices.

## Getting Started

### Prerequisites

- Node.js
- npm

### Installation

1. Clone the repository:
    ```bash
    git clone https://github.com/yourusername/daveymaze.git
    ```
2. Navigate to the project directory:
    ```bash
    cd daveymaze
    ```
3. Install dependencies:
    ```bash
    npm install
    ```
4. Start the development server:
    ```bash
    npm run dev
    ```

## Usage

1. Open your web browser and navigate to `http://localhost:5000/`.
2. Use the UI to create a grid, place start and end points, and add obstacles.
3. Select an algorithm and run the visualizer.

## Algorithm Explanation

### Dijkstra's Algorithm

- **Basic Idea**: Finds the shortest path from a starting node to all other nodes in a weighted graph.
- **Time Complexity**: O(V^2), can be improved to O(V log V + E log V) with priority queues.

### A* Algorithm

- **Basic Idea**: An extension of Dijkstra's Algorithm optimized for pathfinding using heuristics.
- **Time Complexity**: Similar to Dijkstra's but often faster due to the heuristic.

## Additional Features

- [ ] Heuristic Selection
- [ ] Step-by-Step Execution
- [ ] Performance Metrics
- [ ] Mobile Responsiveness

## Future Enhancements

- Add more pathfinding algorithms like Bellman-Ford, Floyd-Warshall, etc.
- Implement a feature to save and load grid states.

## Contributing

Feel free to open issues and pull requests!

## License

This project is licensed under the MIT License.
