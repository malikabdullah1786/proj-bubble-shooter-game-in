# System Architecture

## 1. Tech Stack
```yaml
Language: Java (OpenJDK 17+)
GUI Framework: Java Swing (`javax.swing`, `java.awt`, `BufferStrategy`)
Architecture Pattern: Model-View-Controller (MVC)
Concurrency: `java.util.concurrent.ScheduledExecutorService` (Fixed-rate Game Loop)
Build System: Apache Maven / Gradle
```

## 2. Topology
```yaml
Topology: Single-Process Desktop Application (Zero-Network)
Components:
  - View (GamePanel, ScoreBoard): Custom Swing components rendering via Graphics2D and BufferStrategy.
  - Controller (InputHandler): Captures MouseListener and KeyAdapter events, dispatches to Model.
  - Model (GameEngine, BubbleGrid, CollisionEngine, MatchFinder): Pure Java state management, deterministic physics, and spatial logic.
Communication: Direct method invocation and PropertyChangeListeners for decoupled UI updates.
```

## 3. Database Schema
```yaml
Database: None — In-memory state management only; state lifecycle bounded to application execution runtime per session constraints.
```

## 4. API Contracts
```yaml
API: None — Local desktop application running within a single JVM process.
```

## 5. Directory Tree
```text
bubble-shooter/
├── pom.xml
├── build.gradle
└── src/
    └── main/
        └── java/
            └── com/
                └── arcade/
                    └── bubbleshooter/
                        ├── Main.java
                        ├── controller/
                        │   └── InputHandler.java
                        ├── model/
                        │   ├── Bubble.java
                        │   ├── BubbleGrid.java
                        │   ├── CollisionEngine.java
                        │   ├── GameEngine.java
                        │   ├── MatchFinder.java
                        │   └── Vector2D.java
                        └── view/
                            ├── GameFrame.java
                            ├── GamePanel.java
                            └── ScoreBoard.java
```

## 6. Security/Perf
- **Frame Rate Stability**: Enforce a fixed 60 FPS update/render cadence via a high-precision `ScheduledExecutorService` combined with active rendering `BufferStrategy` to bypass standard Swing EDT bottlenecks and prevent screen tearing.
- **Memory & GC Control**: Implement an explicit object pooling pattern (`BubblePool`) for `Bubble` instances to eliminate runtime object allocation during firing sequences, maintaining a strict footprint below the 256MB heap limit and preventing GC pauses.
- **Input Latency Mitigation**: Decouple input polling from the render loop via event listeners (`MouseListener`, `KeyAdapter`) ensuring action-to-state reaction times remain under the 16ms threshold.
- **Collision Boundary Safety**: Utilize deterministic AABB and radial distance bounding checks with immediate position clamping to prevent out-of-bounds memory corruptions or invalid array indexing in the `BubbleGrid` matrix.