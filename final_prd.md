# Product Requirements Document

### 1. Overview & Goals
* **Core Problem**: Develop a performant, low-latency bubble shooter game using Java Swing for desktop environments.
* **User Benefit**: Provide a classic, responsive arcade experience with deterministic physics and state management.
* **KPIs**: Frame rate stability (60 FPS), input latency < 16ms, zero memory leaks during 30-minute sessions.

### 2. Personas (Compact)
* **Casual Gamer**: Seeks quick, offline entertainment with simple drag-and-aim mechanics.
* **Java Developer**: Seeks a modular, extensible codebase for learning Swing graphics and game loops.

### 3. User Stories & Epics
* **Game Loop**: As a player, I want a consistent game loop so that the game state updates smoothly.
  - AC: [1] Given game start, When loop runs, Then update physics/render. [2] Given pause, When triggered, Then freeze state.
* **Bubble Mechanics**: As a player, I want to aim and shoot bubbles so that I can clear the board.
  - AC: [1] Given mouse drag, When released, Then fire bubble vector. [2] Given collision, When bubble hits group, Then check match-3.
* **Scoring/Progression**: As a player, I want to earn points for matches so that I can track high scores.
  - AC: [1] Given match-3, When popped, Then update score. [2] Given board clear, When empty, Then trigger win state.

### 4. Functional Specs
* **UI Components (Swing)**:
    * `GamePanel`: Custom `JPanel` with `Graphics2D` rendering.
    * `ScoreBoard`: `JLabel` updates via `PropertyChangeListener`.
    * `InputHandler`: `MouseListener` for aiming; `KeyAdapter` for pause/restart.
* **Game Logic**:
    * `BubbleGrid`: 2D array `int[rows][cols]` representing bubble colors.
    * `CollisionEngine`: AABB (Axis-Aligned Bounding Box) for bubble-to-bubble/wall detection.
    * `MatchFinder`: DFS/BFS algorithm to identify connected clusters of same-color bubbles.
* **Error Handling**:
    * `Render Loop` -> Success: Frame draw, Error: Log exception, continue loop.
    * `Collision Detection` -> Success: Update grid, Error: Reset bubble position to last valid state.
    * `Game State Transition` -> Success: Update UI, Error: Revert to previous state.

### 5. Non-Functional Requirements
* **Performance**: Target 60 FPS on JVM 17+; use `BufferStrategy` for flicker-free rendering.
* **Resource Mgmt**: Heap size limit 256MB; explicit `System.gc()` discouraged; use object pooling for bubble instances.
* **Compatibility**: Windows 10/11, macOS, Linux (OpenJDK 17+).
* **Architecture**: MVC pattern; Model (Game Logic), View (Swing Components), Controller (Input Listeners).

### 6. Out of Scope & Release Plan
* **Exclusions**: 
    1. Network multiplayer/leaderboards.
    2. Asset loading from external databases.
    3. Advanced particle physics/shaders.
* **Release Plan**:
    1. **MVP**: Core loop, grid rendering, basic collision, match-3 logic.
    2. **Polish**: Score tracking, game-over screen, sound effects integration.