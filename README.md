# Jackaroo: The Shadow Path 

A complete digital reimagining of the classic Middle Eastern board game, Jackaroo. Built entirely in Java, this project transforms the traditional 4-player team game into a challenging single-player tactical experience against three automated CPU opponents. 

The project emphasizes strict adherence to Object-Oriented Programming (OOP) pillars, robust exception handling, and a completely custom, responsive JavaFX graphical interface inspired by cinematic, dark-mode samurai aesthetics.

## 🏗️ Architectural Overview (MVC Pattern)

To ensure high maintainability and scalable code, the project was strictly structured using the **Model-View-Controller (MVC)** architectural pattern. 

*   **The Model (Game Engine):** 
    The core logic operates entirely independent of the UI. It manages a 100-cell main track, isolated Safe Zones, and distinct Home Zones. It handles complex rule validation, such as marble collision (burning), Safe Zone entry blocking, and the unique behaviors of 15 different card types (including multi-step splits for the 'Seven' card and dynamic board alterations for Wild cards).
*   **The View (JavaFX UI):** 
    A completely custom, CSS-free graphical interface. Moving away from standard grids and Swing dialogs, the view utilizes a dynamically calculated circular track, custom `StackPane` card tiles with hover animations, and a `Canvas`-based ambient particle system (glowing embers) that renders independently of the structural nodes. 
*   **The Controller (Event Delegation):** 
    Input handlers inside the UI layer capture user intents (card selection, marble targeting) and strictly delegate them to the `Game` engine. The controller relies heavily on `try-catch` blocks; if the Model determines a move is illegal, it throws a custom `GameException` (e.g., `IllegalMovementException`, `SplitOutOfRangeException`). The Controller catches these and surfaces them as stylized native alerts without ever breaking the game loop.

## 🚧 Key Technical Challenges & Solutions

### 1. Dynamic Radial Layouts vs. Static Layout Managers
*   **The Problem:** Traditional board games are often mapped to 2D arrays or grid layouts. However, the design called for a majestic, circular track that could dynamically scale with the window size without overlapping the player's hand of cards or the action log.
*   **The Solution:** Bypassed standard layout managers (`GridPane`/`VBox`) for the board rendering. Instead, implemented trigonometric math (`Math.cos` and `Math.sin`) combined with JavaFX property binding. The board's center `(cx, cy)` dynamically recalculates based on the window's width and height (reserving a fixed bottom margin for the player's console). Marbles and cells are mathematically plotted along a radius, allowing the entire board to scale perfectly into fullscreen.

### 2. Threading Conflicts with Game Loops and Dialogs
*   **The Problem:** JavaFX strictly enforces that nested event loops (like `showAndWait()` for native dialog boxes) cannot be triggered while the system is processing a `Timeline` animation. Attempting to show the initial player setup dialog during the cinematic loading screen caused an `IllegalStateException`.
*   **The Solution:** Decoupled the initialization logic from the JavaFX animation thread. By pushing the initial delay to a background `Thread` and utilizing `Platform.runLater()`, the engine safely waits for the loading animation to conclude before passing control back to the main UI thread to prompt the user, preventing event loop crashes.

### 3. Asynchronous CPU Execution & State Synchronization
*   **The Problem:** After a human player completes a turn, three CPU players need to execute their logic sequentially. If they play instantly, the user cannot track what happened on the board.
*   **The Solution:** Implemented a cyclic `Timeline` acting as a turn-manager. The timeline fires every 1.2 seconds, checking the active player state. If it is a CPU turn, the Model calculates the optimal randomized move, updates the backend, and then the Controller triggers a full `updateUI()` refresh. This ensures the visual state remains perfectly synchronized with the backend data models while providing visual breathing room for the human player to read the Action Chronicle.

## 🚀 Tech Stack
*   **Language:** Java 8+
*   **Framework:** JavaFX (Base nodes, Canvas API, Hardware-accelerated Transitions)
*   **Concepts:** MVC Architecture, Polymorphism & Inheritance (Card hierarchies), Custom Exception Handling, Event-Driven Programming.
