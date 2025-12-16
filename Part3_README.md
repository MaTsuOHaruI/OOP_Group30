# Warehouse Robot - OOP Reinforcement Learning Project

## Overview
A custom Gymnasium environment demonstrating **Object-Oriented Programming** principles through a warehouse robot navigation task. The robot must navigate a grid with obstacles to collect packages using learned behavior.

## OOP Principles Demonstrated

### 1. **Abstraction**
- `GameObject` abstract base class defines interface for all game entities
- `Agent` abstract base class defines interface for all learning agents

### 2. **Inheritance**
- Multi-level hierarchy: `GameObject` → `Robot`, `Package`, `Wall`, `Floor`
- Agent hierarchy: `Agent` → `QLearningAgent`, `RandomAgent`

### 3. **Polymorphism**
- `interact()` method behaves differently for each GameObject subclass:
  - `Floor`: Allows movement, small penalty
  - `Wall`: Blocks movement, larger penalty
  - `Package`: Allows movement, large reward, ends episode
- Environment calls `interact()` without type checking (true polymorphism)

### 4. **Encapsulation**
- Learning logic encapsulated in Agent classes
- Environment state management encapsulated in WarehouseEnv
- Q-table and learning parameters private to QLearningAgent

## Project Structure

```
sprites/
├── warehouse_env.py    # Custom Gymnasium environment + GameObject hierarchy
├── agent.py            # Agent abstract class + Q-Learning implementation
├── train.py            # Training script with metrics and visualization
├── demo.py             # Trained agent demonstration
├── main.py             # Manual control (human playable)
├── test_env.py         # Unit tests for OOP structure
├── models/             # Saved trained agents (created during training)
│   ├── best_agent.pkl
│   ├── final_agent.pkl
│   └── training_curves.png
└── README.md           # This file
```

## Installation

```bash
pip install gymnasium pygame numpy matplotlib
```

## Usage

### 1. Train the Agent
Train a Q-learning agent to solve the environment:

```bash
python train.py
```

This will:
- Train for 1000 episodes
- Save best model to `models/best_agent.pkl`
- Generate training curves in `models/training_curves.png`
- Display progress every 50 episodes

### 2. Watch Trained Agent
See the trained agent in action:

```bash
python demo.py
```

### 3. Play Manually
Control the robot yourself:

```bash
python main.py
```

Use arrow keys to navigate, collect the package while avoiding walls!

### 4. Run Tests
Verify OOP structure:

```bash
python test_env.py
```

## Environment Details

- **Grid Size**: **Configurable** (default 8×8, supports arbitrary sizes)
- **Observation Space**: Box(4) - [robot_x, robot_y, package_x, package_y]
- **Action Space**: Discrete(4) - [Up, Right, Down, Left]
- **Rewards**:
  - Package collected: +100
  - Wall collision: -0.5
  - Floor movement: -0.01
  - Distance-based shaping: ±0.1 per tile closer/farther
- **Episode Termination**:
  - Success: Robot reaches package
  - Truncation: 200 steps reached

## Grid Size Scalability

The environment supports **arbitrary grid dimensions**, demonstrating excellent scalability:

### Tested Sizes
- ✅ 8×8 (64 tiles) - Default
- ✅ 10×10 (100 tiles) - Minimum suggested
- ✅ 12×12 (144 tiles)
- ✅ 15×15 (225 tiles)
- ✅ 20×20 (400 tiles)

### Usage
```python
# Create 10x10 environment
env = WarehouseEnv(grid_size=10, wall_density=0.15, max_steps=200)

# Create 20x20 environment
env = WarehouseEnv(grid_size=20, wall_density=0.15, max_steps=400)
```

### Dynamic Adaptation
- **Observation Space**: Automatically adjusts bounds (e.g., Box(0, 19, ...) for 20×20)
- **Rendering**: Window size scales dynamically (grid_size × 64 pixels)
- **Game Logic**: All components adapt to grid size automatically

### Testing Scalability
```bash
# Test multiple grid sizes
python test_scalability.py

# Visual demonstration
python demo_grid_sizes.py

# Train on different sizes
python example_multi_size_training.py
```

See [SCALABILITY.md](SCALABILITY.md) for detailed documentation.


## Q-Learning Configuration

- **Learning Rate (α)**: 0.1
- **Discount Factor (γ)**: 0.95
- **Epsilon Decay**: 0.995 (1.0 → 0.01)
- **Training Episodes**: 1000

## Expected Performance

After training, the agent should achieve:
- **Success Rate**: >70%
- **Average Steps**: ~20-40 steps per episode
- **Q-table Size**: ~500-1000 unique states

## OOP Design Highlights

### Polymorphic Interaction System
```python
# Environment doesn't need to know object type!
obj = get_object_at(target_x, target_y)  # Could be Wall, Floor, or Package
can_move, reward, done = obj.interact(agent)  # Polymorphic call
```

### Abstract Agent Interface
```python
class Agent(ABC):
    @abstractmethod
    def select_action(self, observation, training=True): pass
    
    @abstractmethod
    def learn(self, state, action, reward, next_state, done): pass
```

This allows easy addition of new agent types (DQN, PPO, etc.) without changing environment code.

## Files Description

| File | Purpose | Key OOP Features |
|------|---------|------------------|
| `warehouse_env.py` | Environment + GameObject hierarchy | Abstraction, Inheritance, Polymorphism |
| `agent.py` | Agent classes | Abstraction, Inheritance, Encapsulation |
| `train.py` | Training loop | Modularity, Separation of Concerns |
| `demo.py` | Visualization | Encapsulation |
| `main.py` | Manual control | User interaction |

## Author
Part 3: Choose Your Own Adventure - OOP Demonstration Project

## License
Educational project for demonstrating OOP principles in reinforcement learning.

松尾春輝　B123040055
