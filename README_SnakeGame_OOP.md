# Snake Game

> A classic Snake game built in Java using Swing, demonstrating core OOP principles through a clean class hierarchy, a welcome screen with speed selection, a special food mechanic, and persistent score logging.

---

## Overview

The player controls a snake that grows by eating food on a 600×600 grid. A special golden food tile appears randomly and awards a burst of extra length. The game ends when the snake collides with itself or the boundary. Scores are logged to a file at the end of each session.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Java |
| GUI | Java Swing (`JFrame`, `JPanel`, `Timer`) |
| Input | `KeyListener` (arrow keys) |
| File I/O | `BufferedWriter` / `FileWriter` (score logging) |
| IDE | IntelliJ IDEA |
| Course | Object-Oriented Programming |

---

## Features

- **Welcome Screen** — `WelcomePage` presents a title, a speed dropdown (Slow / Medium / Fast), and a Start button before the game begins; the frame swaps panels seamlessly on launch
- **Adjustable Speed** — mapped to `Timer` delay: Slow = 200 ms, Medium = 100 ms, Fast = 50 ms
- **Special Food Mechanic** — a 10% chance on each food placement spawns a `SpecialFood` tile (rendered yellow); eating it grants 2–6 extra body segments at once instead of 1
- **Score Display** — live score (body length) is drawn on the canvas; "Game Over: N" replaces it when the game ends
- **Score Logging** — final score is appended to a text file via `saveScore()` using `BufferedWriter`
- **Collision Detection** — checked against all body segments and all four walls on every game tick

---

## Project Structure

```
oopproject/
├── src/
│   ├── App.java              # Entry point — creates JFrame and loads WelcomePage
│   ├── WelcomePage.java      # Pre-game panel: speed selector + start button
│   └── SnakeGame.java        # Core game panel: rendering, movement, input, food logic
├── output.txt                # Appended score log (auto-generated at runtime)
└── oopproject.iml            # IntelliJ module file
```

---

## How to Run

**Prerequisites:** JDK 8+

```bash
javac src/*.java -d out/
java -cp out/ App
```

Or open in **IntelliJ IDEA** and run `App.java`.

> **Note:** `saveScore()` currently uses an absolute Windows path for `output.txt`. Change this to a relative path (e.g., `"output.txt"`) before running on a different machine.

---

## Class Design & OOP Concepts

### `App.java` — Entry Point
Creates the `JFrame`, instantiates `WelcomePage`, and makes the window visible. Keeps the entry point minimal and delegates all behaviour.

### `WelcomePage.java` — Pre-Game Panel
- Extends `JPanel`, implements `ActionListener`.
- Reads the selected speed, maps it to a delay value, creates a `SnakeGame` instance, sets the timer delay, then removes itself from the frame and adds the game panel — a clean panel-swap pattern.

### `SnakeGame.java` — Game Engine
- Extends `JPanel`, implements `ActionListener` (game loop tick) and `KeyListener` (direction input).
- **Inner class `Tile`** — lightweight coordinate holder (`x`, `y`).
- **Inner class `SpecialFood extends Tile`** — inherits position from `Tile`; its type is checked at runtime with `instanceof` to trigger the burst-growth behaviour. This is a direct application of **inheritance** and **polymorphism**.
- `ArrayList<Tile> snakeBody` — the body is a dynamic list; on each tick the tail is walked backward and the head's old position is copied down the chain.
- `placeFood()` — generates random coordinates and uses a 10% probability branch to decide whether to instantiate `SpecialFood` or a plain `Tile`.

### OOP Principles in Practice

| Principle | Where Applied |
|---|---|
| Encapsulation | All game state (velocity, board dimensions, food, body) is private to `SnakeGame` |
| Inheritance | `SpecialFood extends Tile` — shares coordinate storage, adds identity |
| Polymorphism | `instanceof SpecialFood` check in `move()` and `draw()` drives different behaviour at runtime |
| Abstraction | `WelcomePage` and `SnakeGame` expose clean interfaces to `App`; internal logic is hidden |
| Separation of Concerns | Each class has one job — entry, welcome UI, or game logic |

---

## Game Controls

| Key | Action |
|---|---|
| ↑ Arrow | Move up |
| ↓ Arrow | Move down |
| ← Arrow | Move left |
| → Arrow | Move right |

Direction reversal into the opposite axis is blocked (e.g., pressing ↑ while moving ↓ is ignored).

---

## Known Limitations / Improvement Areas

- `saveScore()` uses a hard-coded absolute file path — should be replaced with a relative path or a configurable setting.
- No restart button on game over — requires closing and relaunching the application.
- Score file is append-only with no timestamps; adding `LocalDateTime` would make the log more useful.
