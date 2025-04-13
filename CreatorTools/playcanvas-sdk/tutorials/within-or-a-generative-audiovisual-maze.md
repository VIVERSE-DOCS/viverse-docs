---
description: >-
  This project is an interactive, generative audiovisual maze experience created
  using PlayCanvas and Tone.js for the Viverse platform.
cover: ../../.gitbook/assets/Screenshot 2025-04-13 at 9.18.57 AM.png
coverY: 0
---

# WITHIN | A Generative Audiovisual Maze

***

## Project Overview

This project is an interactive, generative audiovisual maze experience created using PlayCanvas and Tone.js for the VIVERSE platform. It combines procedural maze generation, real-time 3D rendering, and layered generative audio . The maze acts as both a spatial and musical composition tool, where user exploration progressively reveals new musical layers.

This project was created by Enrique Garcia-Alcalá, digital artist, creative technologist, and professor of digital art and interactive media at Tecnológico de Monterrey in Mexico.

### Project Resources

WITHIN can be experienced on VIVERSE here: [https://create.viverse.com/nmGuQ9Y](https://create.viverse.com/nmGuQ9Y)

The WITHIN PlayCanvas Project can be found here: Link TBD

An overview of this project can be found here: Link TBD

### Core Concepts and Techniques

\- Procedural content generation

\- Generative music with Tone.js

\- Interactivity through object collection

\- Use of template objects and tags for wall and object instantiation

<figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 9.18.57 AM.png" alt="" width="563"><figcaption></figcaption></figure>

## Maze Generation

### 1. Introduction to Maze Generation

Maze generation algorithms are techniques used to create connected, solvable labyrinths through procedural methods. They are widely applied in games, simulations, and generative art to construct spatial experiences that feel challenging, mysterious, or organic.

At their core, these algorithms decide how to link individual cells within a grid by removing walls between neighbors, which in turn defines the structure and rhythm of the maze.

### 2. Core Concepts

* Grid: A 2D matrix where each cell may connect to its adjacent neighbors.
* Linking: The act of removing a wall between two adjacent cells to form a passage.
* Bias: The directional tendency of an algorithm (e.g., favoring east or north links).

### 3. General Maze Algorithm Workflow

1. Define a grid: Create a 2D array of cells.
2. Select a starting point (often random).
3. Use a traversal algorithm to visit cells and remove walls between them.
4. Repeat until all cells are visited and connected.

<figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 9.23.51 AM.png" alt="" width="563"><figcaption><p>Fig 1. Grid array as an starting point for the maze construction</p></figcaption></figure>

### 4. Overview of Algorithms Used

#### Aldous-Broder Algorithm

This algorithm creates a uniform spanning tree by performing a random walk through the grid. It starts at a random cell and walks to a random neighbor. If that neighbor hasn’t been visited yet, it carves a passage.\
It continues until all cells have been visited and linked at least once.

* Start with a random cell.
* While there are unvisited cells:
  * Choose a random neighbor.
  * If that neighbor hasn’t been linked yet:
    * Link it.
    * Decrease the count of unvisited cells.
* Move to the neighbor and repeat.

<figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 9.26.25 AM.png" alt=""><figcaption><p>Fig 2.1-2.3. Variations on Aldous-Broder algorithm</p></figcaption></figure>

<div><figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 9.26.57 AM.png" alt=""><figcaption></figcaption></figure> <figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 9.26.50 AM.png" alt=""><figcaption></figcaption></figure></div>

#### Binary Tree Algorithm

A fast, simple algorithm that connects each cell to either its north or east neighbor (if available). This creates a maze with a strong diagonal bias and clear, repetitive patterns.

* For each cell in the grid:
  * Check if it has a north or east neighbor.
  * Randomly choose one of them (if any).
  * Link the current cell to that neighbor.

<figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 9.28.32 AM.png" alt=""><figcaption><p>Fig 3.1-3.3 Binary tree algorithm variations</p></figcaption></figure>

<div><figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 9.28.44 AM.png" alt=""><figcaption></figcaption></figure> <figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 9.28.38 AM.png" alt=""><figcaption></figcaption></figure></div>

### 5. Code Specifications

#### Cell Class

Each Cell represents a square in the maze grid. It knows its position (row, column), links to its neighbors (connections), and whether it has any special properties like an arch.

Constructor:

* `row`, `col`: The cell’s position in the grid.
* `gridBuilder`: A reference to the main grid manager, needed to coordinate things like placing arches.
* `links`: A Map of other cells this cell is linked to (passages).
* `hasArch`: A flag indicating whether an arch should be displayed in this cell.

<table><thead><tr><th width="232.074951171875">prop/method</th><th>description</th></tr></thead><tbody><tr><td>link(cell, bidirectional = true)</td><td>Links the current cell with a neighbor cell by removing the wall between them. If <code>bidirectional</code> is true (default), it also links the neighbor back to the current cell. A 20% chance is used to decide whether to request an arch between them, as long as both are not on the outer boundary.</td></tr><tr><td>isLinked(cell)</td><td>Returns true if this cell is linked to the given cell (i.e., if there’s a passage between them).</td></tr><tr><td>neighbors()</td><td>Returns a list of all non-null neighboring cells (north, south, east, west).</td></tr><tr><td>isBoundary()</td><td>Checks whether the cell lies on the outer boundary of the maze grid. Used to avoid placing arches on the outermost edges.</td></tr></tbody></table>

#### Grid Class

The Grid class is responsible for managing the entire maze. It creates and organizes all cells, establishes their neighbor relationships, and runs maze generation algorithms.

<table><thead><tr><th width="235.9840087890625">prop/method</th><th>description</th></tr></thead><tbody><tr><td>prepareGrid()</td><td>Creates a 2D array of cells using nested <code>Array.from()</code>, assigning each a row, column, and a reference to the grid builder.</td></tr><tr><td>configureCells()</td><td>Assigns each cell its four directional neighbors (north, south, east, west), if they exist within the grid bounds.</td></tr><tr><td>cellAt(row, col)</td><td>Returns the cell at the given row and column if it’s inside the grid; otherwise returns null. Used to safely access neighbors.</td></tr><tr><td>getExplorableCells()</td><td>Returns all cells that have at least one link (i.e., are part of the maze). Used to decide where to spawn interactive objects.</td></tr><tr><td>randomCell()</td><td>Picks a random cell from the grid. Used as a starting point for certain algorithms.</td></tr><tr><td>size()</td><td>Returns the total number of cells in the maze.</td></tr></tbody></table>

#### Visual Elements: Arches

In this project, some carved passages may include arches — a visual element. Arches are added with a 20% chance between two connected cells, as long as they’re not on the boundary of the maze.

When an arch is added:

* The `hasArch` property is set to true on both cells.
* The `requestArchPlacement()`method is called to handle the visual placement.

<figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 9.42.10 AM.png" alt=""><figcaption><p>Fig 4. Aldous-Broder algorithm variation with arch placement</p></figcaption></figure>

### 6. Full Code: Maze Generation

Below is the full code of the Cell and Grid classes for reference and analysis.

<details>

<summary>Cell Class</summary>

```javascript
class Cell {
    constructor(row, col, gridBuilder) {
        this.row = row;
        this.col = col;
        this.gridBuilder = gridBuilder; // Store reference to GridBuilder
        this.links = new Map();
        this.hasArch = false;
    }

    link(cell, bidirectional = true) {
        this.links.set(cell, true);
        if (bidirectional) {
            cell.link(this, false);
        }

        if (this.isBoundary() || cell.isBoundary()) {
            return;
        }

        if (Math.random() < 0.2 && this.gridBuilder) {
            this.hasArch = true;
            cell.hasArch = true;
            this.gridBuilder.requestArchPlacement(this, cell);
        }
    }

    isLinked(cell) {
        return this.links.has(cell);
    }

    neighbors() {
        return [this.north, this.south, this.east, this.west].filter(Boolean);
    }

    isBoundary() {
        return this.row === 0 || this.row === this.gridBuilder.rows - 1 ||
               this.col === 0 || this.col === this.gridBuilder.columns - 1;
    }
}
```

</details>

<details>

<summary>Grid Class</summary>

```javascript
class Grid {
    constructor(rows, columns, gridBuilder) {
        this.rows = rows;
        this.columns = columns;
        this.gridBuilder = gridBuilder;
        this.grid = this.prepareGrid();
        this.configureCells();
    }

    prepareGrid() {
        return Array.from({ length: this.rows }, (_, row) =>
            Array.from({ length: this.columns }, (_, col) => new Cell(row, col, this.gridBuilder))
        );
    }

    configureCells() {
        for (let row = 0; row < this.rows; row++) {
            for (let col = 0; col < this.columns; col++) {
                const cell = this.grid[row][col];
                cell.north = this.cellAt(row - 1, col);
                cell.south = this.cellAt(row + 1, col);
                cell.east = this.cellAt(row, col + 1);
                cell.west = this.cellAt(row, col - 1);
            }
        }
    }

    cellAt(row, col) {
        if (row < 0 || row >= this.rows || col < 0 || col >= this.columns) {
            return null;
        }
        return this.grid[row][col];
    }

    binaryTreeMaze() {
        this.grid.flat().forEach(cell => {
            const neighbors = [];
            if (cell.north) neighbors.push(cell.north);
            if (cell.east) neighbors.push(cell.east);
            const neighbor = neighbors[Math.floor(Math.random() * neighbors.length)];
            if (neighbor) {
                cell.link(neighbor);
            }
        });
    }

    aldousBroderMaze() {
        let cell = this.randomCell();
        let unvisited = this.size() - 1;

        while (unvisited > 0) {
            let neighbors = cell.neighbors();
            let next = neighbors[Math.floor(Math.random() * neighbors.length)];

            if (next.links.size === 0) {
                cell.link(next);
                unvisited--;
            }

            cell = next;
        }
    }

    getExplorableCells() {
        return this.grid.flat().filter(cell => cell.links.size > 0);
    }

    randomCell() {
        const row = Math.floor(Math.random() * this.rows);
        const col = Math.floor(Math.random() * this.columns);
        return this.grid[row][col];
    }

    size() {
        return this.rows * this.columns;
    }
}
```

</details>

## Wall Placement Logic

### 1. Overview

This section explains how walls and arches are placed in the maze based on the generated grid structure. The system dynamically places walls where cells are not connected and occasionally adds arches between connected cells for architectural variety.

### 2. Link to Maze Generation

Once the maze has been generated using either the Binary Tree or Aldous-Broder algorithm, each cell knows which of its neighboring cells it is connected to. Walls are placed around each cell where a connection (or passage) does not exist, effectively enclosing the maze except where corridors exist.

### 3. Wall Creation

The main function to create walls is `GridBuilder.prototype.createWalls`. It performs the following:

* Calculates the world position for each potential wall around a cell (north, south, east, west).
* Checks if the current cell is linked to a neighbor in that direction.
* If not linked, it places a wall prefab there.
* Occasionally (5% chance), it also places a decorative statue on that wall.\


<div><figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 9.49.36 AM.png" alt=""><figcaption><p>Fig 5. Variation with different wall models placed</p></figcaption></figure> <figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 9.49.32 AM.png" alt=""><figcaption><p>Fig 6. Variation with decorative statue spawning</p></figcaption></figure></div>

### 4. Wall Placement Logic

The function `createWall(x, y, z, rotation)` instantiates a wall prefab and positions it precisely in the 3D environment, avoiding duplicates using a key system. It also adds collision components and makes the wall static.

### 5. Arches and Passage Decoration

Arches are placed between connected cells if they are not on the boundary of the maze. This is handled by `requestArchPlacement(cellA, cellB)`, which calculates the midpoint and orientation between the two cells, then calls `createArch()`.

`createArch()` does the following:

* Instantiates the arch prefab and rotates it correctly.
* Calculates the dimensions of the arch using its bounding box.
* Positions two invisible box colliders (pillars) on either side of the arch.
* Adds rigidbody and collision components for physical interaction.
* Adds the arch and its colliders to the scene graph.

### 6. Full Code: Wall and Arch Placement

<details>

<summary>createWalls</summary>

```javascript
GridBuilder.prototype.createWalls = function (cell) {
    const x = cell.col * this.cellSize;
    const z = cell.row * this.cellSize;
    const y = this.wallHeight / 2;

    let wallPositions = [
        { x: x, z: z - this.cellSize / 2, rotation: 0, direction: "north" },
        { x: x, z: z + this.cellSize / 2, rotation: 0, direction: "south" },
        { x: x + this.cellSize / 2, z: z, rotation: 90, direction: "east" },
        { x: x - this.cellSize / 2, z: z, rotation: 90, direction: "west" }
    ];

    wallPositions.forEach(wall => {
        if (
            (wall.direction === "north" && !cell.isLinked(cell.north)) ||
            (wall.direction === "south" && !cell.isLinked(cell.south)) ||
            (wall.direction === "east" && !cell.isLinked(cell.east)) ||
            (wall.direction === "west" && !cell.isLinked(cell.west))
        ) {
            this.createWall(wall.x, y, wall.z, wall.rotation);
            if (Math.random() < 0.05) {
                this.placeStatueOnWall(wall.x, y, wall.z, wall.rotation);
            }
        }
    });
};

```

</details>

<details>

<summary>createWall</summary>

```javascript
GridBuilder.prototype.createWall = function (x, y, z, rotation) {
    const key = `${x}_${y}_${z}_${rotation}`;
    if (this.wallPositions.has(key)) return;

    let wallPrefabs = [this.wallPrefab1, this.wallPrefab2, this.wallPrefab3];
    let selectedPrefab = wallPrefabs[Math.floor(Math.random() * wallPrefabs.length)];

    if (!selectedPrefab || !selectedPrefab.resource) return;

    const wall = selectedPrefab.resource.instantiate();
    let modelEntity = wall.render ? wall : wall.findByName("Render") || wall.children.find(child => child.render);

    if (!modelEntity || !modelEntity.render || !modelEntity.render.meshInstances.length) return;

    let aabb = modelEntity.render.meshInstances[0].aabb;
    let halfExtents = aabb.halfExtents.clone();
    wall.setLocalPosition(x, y - this.wallHeight / 2, z);
    wall.setLocalEulerAngles(90, rotation, 0);
    [halfExtents.x, halfExtents.y, halfExtents.z] = [halfExtents.x, halfExtents.z, halfExtents.y + 2];

    wall.addComponent('collision', { type: 'box', halfExtents: halfExtents });
    wall.addComponent('rigidbody', { type: 'static' });

    this.app.root.addChild(wall);
    this.wallPositions.add(key);
};

```

</details>

<details>

<summary>requestArchPlacement</summary>

```javascript
GridBuilder.prototype.requestArchPlacement = function (cellA, cellB) {
    if (!this.archPrefab || !this.archPrefab.resource) return;
    if (cellA.isBoundary() || cellB.isBoundary()) return;

    let midX = ((cellA.col + cellB.col) / 2) * this.cellSize;
    let midZ = ((cellA.row + cellB.row) / 2) * this.cellSize;
    let y = this.wallHeight / 2;
    let rotation = (cellA.col !== cellB.col) ? 90 : 0;

    this.createArch(midX, y, midZ, rotation);
};

```

</details>

<details>

<summary>createArch</summary>

```javascript
GridBuilder.prototype.createArch = function (x, y, z, rotation) {
    const arch = this.archPrefab.resource.instantiate();
    arch.setLocalPosition(x, y - this.wallHeight / 2, z);
    arch.setLocalEulerAngles(90, rotation, 0);

    let modelEntity = arch.render ? arch : arch.findByName("Render") || arch.children.find(child => child.render);
    if (!modelEntity || !modelEntity.render || !modelEntity.render.meshInstances) return;

    let aabb = modelEntity.render.meshInstances[0].aabb;
    let archHalfExtents = aabb.halfExtents.clone();

    let archWidth = archHalfExtents.x * 2;
    let archHeight = archHalfExtents.y * 2;
    let archDepth = archHalfExtents.z * 2;

    let pillarWidth = archWidth * 0.2;
    let pillarHeight = archHeight;
    let pillarDepth = archDepth;

    let adjustedPillarWidth = pillarWidth;
    let adjustedPillarDepth = pillarDepth;
    if (rotation === 90 || rotation === 270) {
        adjustedPillarWidth = pillarDepth / 5;
        adjustedPillarDepth = pillarWidth * 5;
    }

    let pillarY = y - this.wallHeight / 2 + (pillarHeight / 2);

    let leftPillar = new pc.Entity("LeftPillar");
    let rightPillar = new pc.Entity("RightPillar");

    if (rotation === 0 || rotation === 180) {
        leftPillar.setLocalPosition(x - (archWidth / 2) + (pillarWidth / 2), pillarY, z);
        rightPillar.setLocalPosition(x + (archWidth / 2) - (pillarWidth / 2), pillarY, z);
    } else {
        leftPillar.setLocalPosition(x, pillarY, z - 2 - (archWidth / 2) + (pillarWidth / 2));
        rightPillar.setLocalPosition(x, pillarY, z + 2 + (archWidth / 2) - (pillarWidth / 2));
    }

    [leftPillar, rightPillar].forEach(pillar => {
        pillar.setLocalEulerAngles(0, rotation, 0);
        pillar.addComponent("collision", {
            type: "box",
            halfExtents: new pc.Vec3(adjustedPillarWidth / 2, pillarHeight / 2, adjustedPillarDepth / 2)
        });
        pillar.addComponent("rigidbody", { type: "static" });
    });

    arch.addComponent("rigidbody", { type: "static" });

    this.app.root.addChild(arch);
    this.app.root.addChild(leftPillar);
    this.app.root.addChild(rightPillar);
};
```

</details>

## Floor Tile Generation

### 1. Overview

Floor generation is a crucial step to visually support the grid-based structure of the maze. Each grid cell receives a tile, with randomized textures (materials) and colliders for physics interaction. This process enhances visual diversity and ensures correct player interaction with the environment.

<figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 9.56.27 AM.png" alt=""><figcaption><p>Fig 7. Variations on floor tiles</p></figcaption></figure>

### 2. Role in Maze Structure

The floor tiles correspond directly to the maze's 2D grid. A tile is placed for each cell, aligned with its (row, col) coordinates. Tiles are not affected by maze connections or walls, so they create a uniform base surface across the entire area.

### 3. Floor Tile Placement Logic

The function `createFloorTiles()` handles tile creation. Here's what it does:

* Iterates over the entire grid using `row` and `col`.
* Calculates the world coordinates for each cell.
* Instantiates the floor prefab and positions it with a consistent orientation.
* Randomly assigns one of the preloaded materials to the tile.
* Adds a box collider and rigidbody for physical interaction.
* Places the tile into the scene graph.

### 4. Material Randomization

To break visual monotony, a list of materials is used. Each tile randomly picks one, giving the floor a more dynamic, varied look. This supports immersion without impacting gameplay.

### 5. Colliders and Physics

Every tile receives a box collider sized to half the grid cell to prevent overlaps. Rigidbodies are set to 'static' so the tiles interact with physics but remain immobile.

### 6. Full Code: Floor Tiles

<details>

<summary>createFloorTiles</summary>

```javascript
GridBuilder.prototype.createFloorTiles = function () {
    console.log("Generating Floor Tiles with Proper Rotation, Materials, and Colliders...");

    if (!this.floorPrefab || !this.floorPrefab.resource) {
        console.warn(" Floor prefab not assigned!");
        return;
    }

    if (!this.floorMaterials || this.floorMaterials.length < 3) {
        console.warn(" Not enough floor materials assigned! Need at least 3.");
        return;
    }

    for (let row = 0; row < this.rows; row++) {
        for (let col = 0; col < this.columns; col++) {
            let x = col * this.cellSize;
            let z = row * this.cellSize;
            let y = 0;

            let floorTile = this.floorPrefab.resource.instantiate();
            floorTile.setLocalPosition(x, y, z);
            floorTile.setLocalEulerAngles(90, 0, 0);

            let modelEntity = floorTile.render ? floorTile : floorTile.findByName("Render") || floorTile.children.find(child => child.render);
            if (!modelEntity || !modelEntity.render) {
                console.warn("No valid render component found for floor tile.");
                continue;
            }

            let randomMaterialIndex = Math.floor(Math.random() * this.floorMaterials.length);
            let selectedMaterial = this.floorMaterials[randomMaterialIndex]?.resource;

            if (selectedMaterial) {
                modelEntity.render.material = selectedMaterial;
            } else {
                console.warn("Selected material is undefined.");
            }

            let colliderSize = this.cellSize / 2;
            floorTile.addComponent("collision", {
                type: "box",
                halfExtents: new pc.Vec3(colliderSize, colliderSize, 0.05)
            });

            floorTile.addComponent("rigidbody", {
                type: "static"
            });

            this.app.root.addChild(floorTile);
        }
    }

    console.log("Floor Prefab Tiles Generated Successfully With Correct Rotation, Materials, and Colliders!");
};

```

</details>

## Object Spawning and Audio Integration

### 1. Overview&#x20;

The object spawning system is responsible for placing collectible objects throughout the maze. These objects enhance interactivity by allowing the player to explore and trigger actions upon collection. The system ensures that each object is placed in a unique, explorable cell within the maze.

### 2. How it Works

Once the maze has been generated, the system identifies all explorable cells—those that have links to other cells. It then attempts to reposition preloaded objects tagged as 'Collectible' into these cells, avoiding overlap and ensuring each object is placed in a valid location.

### 3. Object Placement Logic

* Get all cells in the maze that are explorable (connected).
* Fetch all preloaded objects with the 'Collectible' tag.
* For each object:
  * Randomly select a cell.
  * Ensure no object has already been placed there.
  * Relocate the object to the cell.
  * Add a spherical collision if it doesn't already have one.
  * Enable trigger events for interaction (e.g., collection).

### 4. Trigger-based Collision

Each object is set up to detect collisions using trigger events. When a player or other entity enters the trigger volume, the `onCollectibleCollected` function is called, which can be customized to handle logic like scoring, sound playback, or activating new elements (e.g., music layers).

<figure><img src="../../.gitbook/assets/Screenshot 2025-04-13 at 10.03.13 AM.png" alt=""><figcaption><p>Fig 8. Variation with object spawning</p></figcaption></figure>

### 5. Full Code: Object Spawning

<details>

<summary>spawnObjects</summary>

```javascript
GridBuilder.prototype.spawnObjects = function () {
    const explorableCells = this.grid.getExplorableCells();
    let placedPositions = new Set();

    this.preloadedObjects = this.app.root.findByTag("Collectible");

    if (this.preloadedObjects.length === 0) {
        console.warn(" No preloaded collectibles found!");
        return;
    }

    if (explorableCells.length < this.preloadedObjects.length) {
        console.warn(` Not enough explorable cells to relocate all collectibles.`);
        return;
    }

    for (let i = 0; i < this.preloadedObjects.length; i++) {
        let object = this.preloadedObjects[i];
        let objectPlaced = false;
        let maxAttempts = 10;

        while (!objectPlaced && maxAttempts > 0) {
            maxAttempts--;

            let cellIndex = Math.floor(Math.random() * explorableCells.length);
            let cell = explorableCells[cellIndex];

            let x = cell.col * this.cellSize;
            let z = cell.row * this.cellSize;
            let positionKey = `${x}_${z}`;

            if (placedPositions.has(positionKey)) {
                continue;
            }

            placedPositions.add(positionKey);
            object.setLocalPosition(x, 0.6, z);

            if (!object.collision) {
                object.addComponent("collision", {
                    type: "sphere",
                    radius: 0.3
                });
            }

            object.collision.on("triggerenter", (event) => this.onCollectibleCollected(object, event), this);

            objectPlaced = true;
        }

        if (!objectPlaced) {
            console.warn(`Could not find a valid position for collectible ${object.name}`);
        }
    }
};

```

</details>

## Tone.js Audio System

### 1. Overview

This project integrates a dynamic, generative music system using Tone.js. It responds to player interaction by progressively layering musical elements based on collectibles. The soundscape evolves with gameplay, making it immersive and reactive.

### 2. Loading Tone.js and Starting Music

Tone.js is loaded dynamically using `loadToneJS`, and music begins with `startMusic`. The system ensures audio starts only after user interaction to comply with browser audio policies. Upon starting, `initMusic` initializes the audio routing and begins the ambient drone.

### 3. Mixer Initialization

`initMixer` sets up mixer channels using `Tone.Gain`, `Tone.Panner`, and a global `Tone.Reverb`. Each instrument group (ambient, drums, lead, bass, etc.) has its own channel for balancing and spatialization.

### 4. Layer Activation via Collectibles

`addNewMusicLayer` activates new music layers based on the name of the collectible object. This makes each collectible not just a visual or scoring element, but a contribution to the evolving musical composition.

### 5. Instrument Descriptions

#### Ambient Drone

A soft evolving pad built with `PolySynth` and `fatsine` oscillators, filtered by an LFO-modulated low-pass filter. It adds depth and calm, creating a base layer for the rest of the composition

#### Drum Beat

Includes a kick (`MembraneSynth`), snare (`MetalSynth`), hi-hat, and toms. Played via a programmed rhythmic `Tone.Loop` .

#### Generative Arpeggio&#x20;

A sawtooth polyphonic synth plays evolving patterns in eighth notes, with delay and randomness added for organic feel.

#### Dub Baseline

A `NoiseSynth` filtered and compressed to create a deep rumble. It plays continuously and contributes low-end power.

#### Lead Pan

A polyphonic sawtooth pad with slow attack and release, using filters and reverb to create evolving harmonies in 8-bar loops.

#### Bell Synth

A bell-like `MetalSynth` plays sparse, randomized melodies with added delay and reverb. Timings are randomized to add surprise and texture.

#### Radar Beeps

Sine wave synth producing short blips, spatialized with random panning and delays. Simulates an electronic radar feel and increases tension.

#### Wind Synth

A generative ambient element wind using filtered noise. It features bandpass filtering to evoke wind-blown electronic signals, enhancing the immersive and mysterious atmosphere of the maze.

### 6. Full Code: Tone.js Audio Generation

<details>

<summary>loadToneJS</summary>

```javascript
GridBuilder.prototype.loadToneJS = function (callback) {
    if (window.Tone) {
        callback();
        return;
    }
    var script = document.createElement("script");
    script.src = this.app.assets.find("Tone.js").getFileUrl();
    script.onload = callback;
    document.head.appendChild(script);
};

```

</details>

<details>

<summary>startMusic</summary>

```javascript
GridBuilder.prototype.startMusic = function () {
    if (Tone.context.state !== "running") {
        Tone.start().then(() => {
            console.log("🎶 Tone.js Started");
            this.initMusic();
        });
    }else{
        this.initMusic();
    }
};

```

</details>

<details>

<summary>initMusic</summary>

```javascript
GridBuilder.prototype.initMusic = function () {
   console.log(" Music System Initialized");
    this.initMixer();
    // Ambient drone
    this.startAmbientDrone();
    // Collectible-triggered instruments
    this.instruments = [];
};

```

</details>

<details>

<summary>addNewMusicLayer</summary>

```javascript
GridBuilder.prototype.addNewMusicLayer = function (name) {
    //console.log(` Adding new music layer for: ${name}`);
    let layer;

    switch (name) {
        case "MinotaurContainer":
            layer = this.createDrumBeat();
            break;
        case "MirrorContainer":
            layer = this.createGenerativeArpeggio();
            break;
        case "ThreadContainer":
            layer = this.createPad();
            break;
        case "RocksContainer":
            layer =  this.createRumble();
            break;
        case "CompassContainer":
            layer = this.createWindLayer();
            break;
        case "LanternContainer":
            layer = this.createBells();
            break;
        default:
            console.warn("No specific layer for this collectible.");
            return;
    }

    if (layer) {
        this.instruments.push(layer);
        Tone.Transport.start();
    }
};

```

</details>

<details>

<summary>initMixer</summary>

```javascript
GridBuilder.prototype.initMixer = function () {
   // console.log("Initializing Audio Mixer");
    // Master volume control
    this.masterVolume = new Tone.Volume(-9).toDestination();
    // Create mixer channels for each instrument group
    this.channels = {
        ambient: new Tone.Gain(0.09).connect(this.masterVolume),
        arpeggio: new Tone.Gain(0.01).connect(this.masterVolume),
        bass: new Tone.Gain(0.05).connect(this.masterVolume),
        drums: new Tone.Gain(0.07).connect(this.masterVolume),
        lead: new Tone.Gain(0.03).connect(this.masterVolume),
        bells: new Tone.Gain(0.04).connect(this.masterVolume),
        wind: new Tone.Gain(0.08).connect(this.masterVolume)
    };
      this.panners = {
        ambient: new Tone.Panner(0).connect(this.channels.ambient),
        arpeggio: new Tone.Panner(-1).connect(this.channels.arpeggio),
        bass: new Tone.Panner(1).connect(this.channels.bass),
        drums: new Tone.Panner(-0.4).connect(this.channels.drums),
        lead: new Tone.Panner(0.7).connect(this.channels.lead),
        wind: new Tone.Panner(0).connect(this.channels.lead)
    };

    // Global reverb for depth
    this.globalReverb = new Tone.Reverb(9).connect(this.masterVolume);
    
   //console.log("Mixer Initialized with Master Volume and Channels");
};
startAmbientDrone
GridBuilder.prototype.startAmbientDrone = function () {
    // Create the Drone Synth (Low Background Layer)
    this.droneSynth = new Tone.PolySynth(Tone.Synth, {
        oscillator: {
            type: "fatsine", // Thick, warm tone
            count: 3,
            spread: 12
        },
        envelope: {
            attack: 5,
            decay: 10,
            sustain: 0.8,
            release: 15
        }
    });

    //  Apply a Low-Pass Filter
    this.filter = new Tone.Filter({
        frequency: 900,
        type: "lowpass",
        Q: 0.8
    });
    // Add Effects (Chorus & Reverb)
    this.chorus = new Tone.Chorus(0.15, 3.5, 0.7);
    this.reverb = new Tone.Reverb(12);
    // Chain Effects
     this.droneSynth.chain(this.filter, this.chorus, this.reverb,this.channels.ambient, this.globalReverb);
    // Modulate Filter Over Time
    this.lfo = new Tone.LFO(0.02, 700, 1100).start();
    this.lfo.connect(this.filter.frequency);
    //  Play Slow-Evolving Drone Notes
    let droneNotes = ["C2","Db2","C3", "Db3", "E3", "F3", "G3", "Ab3", "B3","C4","Ab4"];
    let droneInterval = new Tone.Loop((time) => {
        let note = droneNotes[Math.floor(Math.random() * droneNotes.length)];
        let velocity = Math.random() * 0.3 + 0.6;
        this.droneSynth.triggerAttackRelease(note, "14s", time, velocity);
    }, "10s").start();
    //console.log("Drone & Random Bell Lead Active");
    // Start Tone.js Transport
    Tone.Transport.start();
};

```

</details>

<details>

<summary>createDrumBeat</summary>

```javascript
GridBuilder.prototype.createDrumBeat = function () {
  this.drumSynth = new Tone.MembraneSynth({
        pitchDecay: 0.1,
        octaves: 2,
        oscillator: { type: "sine" },
        envelope: { attack: 0.01, decay: 0.3, sustain: 0.1, release: 0.5 }
    });
       this.snareSynth = new Tone.MetalSynth({
    frequency: 220,
    envelope: { attack: 0.002, decay: 0.15, release: 0.05 },
    harmonicity: 3,
    modulationIndex: 20,
    resonance: 2000,
    octaves: 1.5
}).connect(this.channels.drums);
    this.hatSynth = new Tone.MetalSynth({
        frequency: 180, envelope: { attack: 0.005, decay: 0.15, release: 0.05 },
        harmonicity: 5, modulationIndex: 35, resonance: 3000, octaves: 1
    });
    this.percSynth = new Tone.MembraneSynth({
        pitchDecay: 0.08,
        oscillator: { type: "triangle" },
        envelope: { attack: 0.002, decay: 0.2, sustain: 0.05, release: 0.2 }
    });
    this.snareReverb = new Tone.Reverb(2);
    this.hatDelay = new Tone.FeedbackDelay("16n", 0.3);
    this.drumSynth.chain(this.channels.drums, this.globalReverb);
    this.snareSynth.chain(this.snareReverb, this.channels.drums, this.globalReverb);
    this.hatSynth.chain(this.hatDelay, this.channels.drums, this.globalReverb);
    this.percSynth.chain(this.channels.drums,this.globalReverb);
    let drumPattern = [
    { time: "0:0", drum: "kick" },
    { time: "0:0.5", drum: "kick" }, // Heartbeat effect
    { time: "0:1.5", drum: "snare" },
    { time: "0:2", drum: "tom" },
    { time: "0:2.75", drum: "hat" },
    { time: "0:3.5", drum: "tom" },
    { time: "1:0", drum: "kick" },
    { time: "1:0.5", drum: "kick" }, // Heartbeat effect
    { time: "1:1.75", drum: "snare" },
    { time: "1:2.5", drum: "hat" },
    { time: "1:3.25", drum: "tom" },
    { time: "2:0", drum: "kick" },
    { time: "2:0.5", drum: "kick" },
    { time: "2:1.5", drum: "snare" },
    { time: "2:2.25", drum: "tom" },
    { time: "2:3", drum: "hat" },
    { time: "3:0", drum: "kick" },
    { time: "3:0.5", drum: "kick" },
    { time: "3:1.75", drum: "snare" },
    { time: "3:2.5", drum: "tom" },
    { time: "3:3.25", drum: "hat" }
];

    const loop = new Tone.Loop((time) => {
        drumPattern.forEach(({ time: beatTime, drum }) => {
            let t = Tone.Time(beatTime).toSeconds() + time;
            switch (drum) {
                case "kick":
                    this.drumSynth.triggerAttackRelease("C1", "8n", t);
                    break;
                case "snare":
                    this.snareSynth.triggerAttackRelease("16n", t);
                    break;
                case "hat":
                    this.hatSynth.triggerAttackRelease("32n", t);
                    break;
                case "perc":
                    this.percSynth.triggerAttackRelease("G2", "16n", t);
                    break;
            }
        });
    }, "4m");
    loop.start(0);
    Tone.Transport.start();
};

```

</details>

<details>

<summary>createGenerativeArpeggio</summary>

```javascript
GridBuilder.prototype.createGenerativeArpeggio = function () {

    // Create a synth with a warm, slightly detuned sound
    this.arpSynth = new Tone.PolySynth(Tone.Synth, {
        oscillator: {
            type: "sawtooth",
            detune: -10       
        },
        envelope: {
            attack: 0.05,
            decay: 0.3,
            sustain: 0.4,
            release: 0.8
        }
    });
    // Apply a delay and reverb for depth
    this.arpDelay = new Tone.FeedbackDelay("8n", 0.4);
    this.arpSynth.chain(this.arpDelay, this.channels.arpeggio, this.globalReverb);
    // Define arpeggio note pattern
    let arpeggioNotes = ["E3", "G3", "A3", "B3", "D4", "E4", "G4", "A4"];
    let index = 0;
    const arpeggioLoop = new Tone.Loop((time) => {
        let note = arpeggioNotes[index % arpeggioNotes.length];
        let velocity = Math.random() * 0.3 + 0.7; // Subtle dynamics
        this.arpSynth.triggerAttackRelease(note, "8n", time, velocity);
        // Introduce slight timing variations for organic feel
        index++;
        if (Math.random() < 0.3) {
            index += 1;
        }
    }, "8n"); // Eighth-note rhythmic structure

    arpeggioLoop.start(0);
    Tone.Transport.start();
    //console.log("🔊 Generative Arpeggio Started");
};

```

</details>

<details>

<summary>createRumble</summary>

```javascript
GridBuilder.prototype.createRumble = function () {
    this.rumbleSynth = new Tone.NoiseSynth({
        noise: { type: "brown" }, 
        envelope: {
            attack: 1,
            decay: 8,
            sustain: 0.9,
            release: 10
        }
    });    
    this.rumbleFilter = new Tone.Filter({
        type: "lowpass",
        frequency: 400,
        Q: 1
    });
    this.rumbleCompressor = new Tone.Compressor(-30, 10);
    this.rumbleReverb = new Tone.Reverb(8);
    this.rumbleGain = new Tone.Gain(1.5);    
    this.rumbleSynth.chain(this.rumbleFilter, this.rumbleCompressor, this.rumbleReverb, this.rumbleGain, this.channels.bass, this.globalReverb);
    this.rumbleSynth.triggerAttack();
    Tone.Transport.start();    
};
```

</details>

<details>

<summary>createPad</summary>

```javascript
GridBuilder.prototype.createPad= function () {
    this.padSynth = new Tone.PolySynth(Tone.Synth, {
        oscillator: {
            type: "sawtooth", // Rich harmonic content
            detune: -5 // Slight detuning for warmth
        },
        envelope: {
            attack: 4,
            decay: 5,
            sustain: 0.7,
            release: 8
        }
    });
    // Apply a gentle filter and spatial effects
    this.padFilter = new Tone.Filter({
        type: "lowpass",
        frequency: 1200,
        Q: 1
    });
    this.padReverb = new Tone.Reverb(15);
    this.padChorus = new Tone.Chorus(0.3, 2, 0.5);
    this.padLFO = new Tone.LFO("0.1hz", 1000, 1500).start();
    this.padLFO.connect(this.padFilter.frequency);

    // Chain everything together
    this.padSynth.chain(this.padFilter, this.padChorus, this.padReverb, this.channels.lead, this.globalReverb);

    // Define a slow-moving chord progression
    let padChords = [
        ["C3", "E3", "G3", "B3"],
        ["D3", "F3", "A3", "C4"],
        ["E3", "G3", "B3", "D4"],
        ["F3", "A3", "C4", "E4"]
    ];
    let index = 0;
    const padLoop = new Tone.Loop((time) => {
        let chord = padChords[index % padChords.length];
        //console.log(`Playing pad chord: ${chord} at ${time}`);
        this.padSynth.triggerAttackRelease(chord, "8m", time, 0.6);
        index++;
    }, "8m"); // Slow, evolving changes
    padLoop.start(0);
    Tone.Transport.start();
};

```

</details>

<details>

<summary>createBells</summary>

```javascript
GridBuilder.prototype.createBells = function(){
    this.bellSynth = new Tone.MetalSynth({
        frequency: 300,
        envelope: { attack: 0.01, decay: 2, release: 1 },
        harmonicity: 8, // Creates a bell-like tone
        resonance: 700,
        modulationIndex: 10,
        volume: -12  
    });
    // Add Reverb and Delay to Bells
    this.bellReverb = new Tone.Reverb(8);
    this.bellDelay = new Tone.FeedbackDelay("4n", 0.5);
    this.bellSynth.chain(this.bellReverb, this.bellDelay, this.channels.bells, this.globalReverb);
    //  Function to Randomly Play Bells
    let bellNotes = ["C5", "E5", "G5", "B5", "D6"];
    let playBell = () => {
        let note = bellNotes[Math.floor(Math.random() * bellNotes.length)];
        let velocity = Math.random() * 0.4 + 0.6;
        this.bellSynth.triggerAttackRelease(note, "2s", "+0.1", velocity);
        // **Randomize next bell trigger time (between 15 and 25 seconds)**
        let nextTime = Math.random() * 10 + 15;
        setTimeout(playBell, nextTime * 1000);
    };
    // Start the First Bell
    setTimeout(playBell, Math.random() * 5 + 10); // Initial bell timing
    Tone.Transport.start();
};

```

</details>

<details>

<summary>createWindLayer</summary>

```javascript
GridBuilder.prototype.createWindLayer = function () {
  // Radar-like beeps
    this.radarBeep = new Tone.Synth({
        oscillator: {
            type: "sine"
        },
        envelope: {
            attack: 0.01,
            decay: 0.2,
            sustain: 0,
            release: 0.1
        }
    });    
    this.beepFilter = new Tone.Filter({
        type: "bandpass",
        frequency: 1000,
        Q: 6
    });    
    // Randomized panning for each beep
    this.beepPanner = new Tone.Panner(0);   
    this.beepDelay = new Tone.FeedbackDelay("8n", 0.3);
    this.radarBeep.chain(this.beepFilter, this.beepPanner, this.beepDelay, this.channels.ambient, this.globalReverb);
    // Schedule radar beeps with random panning
    const beepLoop = new Tone.Loop((time) => {
        let pitch = ["E5", "G#5", "B5", "C6", "D#6"][Math.floor(Math.random() * 5)];
        let delay = Math.random() * 4 + 2; // Randomized spacing between beeps
        let panValue = Math.random() * 2 - 1; // Random panning between -1 and 1
        
        this.beepPanner.pan.rampTo(panValue, 0.2); // Smoothly adjust panning
        this.radarBeep.triggerAttackRelease(pitch, "16n", time + delay);
    }, "4m");
    beepLoop.start(0);
    Tone.Transport.start();
};

```

</details>

## User Interaction

### 1. Overview

When a player collects a collectible object in the maze, it triggers a set of reactions that provide audiovisual feedback, update the UI, and potentially unlock new audio layers. This system creates a sense of progression and reward.

### 2. Triggering a Collection

Collectible objects are set up with spherical colliders that use trigger events. When the player enters the collider, the `onCollectibleCollected()` function is called. This ensures objects can be interacted with passively without requiring clicks or physics-based collisions.

### 3. Collection Logic Breakdown

The `onCollectibleCollected()` function performs the following tasks:

* Logs the collection event.
* Marks the object as collected (avoiding duplicates).
* Updates the counter displayed on screen.
* Updates the object’s image in the UI (if one is defined).
* Shows the full object UI panel.
* Plays a sound or adds a new music layer (once per object).

### 4. UI Closure and Ending Logic

The `hideUI()` function is called when the player closes the UI panel. If all objects are collected, it will:

* Hide the main UI.
* Show an ending screen.
* After a short delay, update the screen to display credits.

### 5. Full Code: Object Collection and Ending Sequence

<details>

<summary>onCollectibleCollected</summary>

```javascript
GridBuilder.prototype.onCollectibleCollected = function (object, event) {
    console.log(`Collected: ${object.name}`);
    if (object.collected) return;
    object.collected = true;
    this.collectedObjects++;
    console.log(` Objects Collected: ${this.collectedObjects}/${this.totalObjects}`);
    if (this.counterUIElements.counter && this.counterUIElements.counter.element) {
        this.counterUIElements.counter.element.text = `${this.collectedObjects}/${this.totalObjects}`;
    }
    if (this.uiElements.image) {
        let imageName = this.collectibleImages[object.name] || "ItemDescription.png";
        let imageAsset = this.app.assets.find(imageName);
        if (imageAsset) {
            this.uiElements.image.element.texture = imageAsset.resource;
            this.uiElements.image.enabled = true;
            this.uiElements.image.element._dirty = true;
        } else {
            console.warn(`Image asset ${imageName} not found.`);
        }
    }
    if (this.uiElements.objectView) {
        this.uiElements.objectView.enabled = true;
    }
    if (!object.soundPlayed) {
        this.addNewMusicLayer(object.name);
        object.soundPlayed = true;
    }
};

```

</details>

<details>

<summary>hideUI</summary>

```javascript
GridBuilder.prototype.hideUI = function () {
    if (this.uiElements.objectView) {
        this.uiElements.objectView.enabled = false;
    }
    if (this.collectedObjects >= this.totalObjects) {
        if (this.endingUIElements.ending) {
            let viverseUI = document.querySelector("#world-root");
            viverseUI.style.display = "none";
            this.endingUIElements.ending.enabled = true;
            setTimeout(() => {
                let creditsImage = this.app.assets.find("Credits.png");
                if (creditsImage && this.endingUIElements.endingImage.element) {
                    this.endingUIElements.endingImage.element.texture = creditsImage.resource;
                    this.endingUIElements.endingImage.element._dirty = true;
                    console.log("Changed EndingView to credits.");
                }
            }, 5000);
        }
    }
};

```

</details>

## Final Thoughts

While the current implementation successfully combines procedural maze generation, interactive collectibles, and a layered generative music system, there are several areas where the project can evolve further:

#### Technical Enhancements

* Dynamic Difficulty Scaling: Adjust maze complexity or collectible distribution based on player behavior or time.
* Mobile Optimization: Adapt controls, UI, and performance for touch devices and mobile web browsers.
* Spatial Audio: Incorporate 3D audio positioning for sound layers using Tone.Listener or Web Audio API for increased immersion.

#### Musical Expansion

* Live Modulation: Introduce real-time modulation of filters or rhythms based on player location or speed.

#### Artistic & UX Improvements

* Visual Effects for Sound: Implement shader-based visual cues when new layers are added.
* Expanded UI: Improve UI responsiveness and design consistency across devices.
* Sound Journal: Let players revisit sounds/layers they’ve discovered, building a "sound memory" map.

#### System Architecture

* Code Refactoring: Modularize and document all components more thoroughly (e.g., separate music logic from maze logic).
* Templates Management: Streamline template }usage and standardize naming for easier scalability.
* Analytics Integration: Track user interaction data for iterative design and testing.
