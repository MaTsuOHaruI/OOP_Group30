# Warehouse Robot - System UML Diagrams

This document contains visual representations of the system architecture and execution flow.

## 1. Class Diagram

This diagram shows the Object-Oriented structure, including Inheritance (GameObject hierarchy) and Composition (WarehouseEnv).

### 1.1 Mermaid Format (Renderable)

```mermaid
classDiagram
    %% Abstract Component
    class GameObject {
        <<Abstract>>
        +x: int
        +y: int
        +image: Surface
        +render(screen, tile_size)
        +interact(agent)*
    }

    %% Concrete Game Objects
    class Floor {
        +interact(agent): (True, -0.01)
    }
    class Wall {
        +interact(agent): (False, -0.5)
    }
    class Package {
        +interact(agent): (True, 100.0)
    }
    class Robot {
        +move(dx, dy, width, height)
        +interact(agent): (False, 0)
    }

    %% Inheritance Relationships
    GameObject <|-- Floor
    GameObject <|-- Wall
    GameObject <|-- Package
    GameObject <|-- Robot

    %% Agent Hierarchy
    class Agent {
        <<Abstract>>
        +select_action(obs)*
        +learn(s, a, r, s')*
        +save(path)*
        +load(path)*
    }

    class QLearningAgent {
        -q_table: Dict
        -epsilon: float
        -learning_rate: float
        +select_action(obs)
        +learn(s, a, r, s')
        -_get_q_value(s, a)
    }

    Agent <|-- QLearningAgent

    %% Environment Composition
    class WarehouseEnv {
        +action_space: Discrete
        +observation_space: Box
        -grid_size: int
        -walls: List~Wall~
        -agent: Robot
        -target: Package
        +reset()
        +step(action)
        +render()
    }

    %% Relationships
    WarehouseEnv *-- "many" Wall : contains
    WarehouseEnv *-- "1" Robot : controls
    WarehouseEnv *-- "1" Package : tracks
    WarehouseEnv *-- "many" Floor : contains
```

### 1.2 ASCII Art Format (Text-Based)

```text
       +------------------+
       |   GameObject     | (Abstract)
       +------------------+
       | + x: int         |
       | + y: int         |
       | + image          |
       +------------------+
       | + interact()*    | <--- Polymorphic Method
       | + render()       |
       +--------^---------+
                |
    +-----------+-----------+----------------+----------------+
    |           |           |                |                |
+---+---+   +---+---+   +---+-------+   +----+------+   +-----------+
| Floor |   | Wall  |   | Package   |   |   Robot   |   |   Agent   | (Abstract)
+-------+   +-------+   +-----------+   +-----------+   +-----------+
|inter- |   |inter- |   |interact() |   | move()    |   | select_()*|
|act()  |   |act()  |   |-> Reward! |   |           |   | learn()*  |
+-------+   +-------+   +-----------+   +-----------+   +-----^-----+
                                                              |
                                                       +------+-------+
                                                       | QLearningAgent|
  +--------------+                                     +---------------+
  | WarehouseEnv |                                     | - q_table     |
  +--------------+                                     | - epsilon     |
  | - agent      |<>-----------------------------------| + learn()     |
  | - walls      |<>--(contains many)- Wall            +---------------+
  | - target     |<>--(contains 1)---- Package
  | - grid_size  |
  +--------------+
  | + reset()    |
  | + step()     |
  | + render()   |
  +--------------+
```

---

## 2. Sequence Diagram

This diagram shows the step-by-step execution flow of a single training step.

### 2.1 Mermaid Format (Renderable)

```mermaid
sequenceDiagram
    participant Main as TrainingLoop
    participant Agent as QLearningAgent
    participant Env as WarehouseEnv
    participant Obj as GameObject (Wall/Pkg)

    Note over Main: Start of Step

    Main->>Agent: select_action(state)
    activate Agent
    Agent-->>Main: action (e.g., UP)
    deactivate Agent
    
    Main->>Env: step(action)
    activate Env
    
    Env->>Env: Calculate target (x, y)
    
    Env->>Obj: interact(agent)
    activate Obj
    Note right of Obj: Polymorphism in action!<br/>Obj decides outcome
    Obj-->>Env: (can_move, reward, done)
    deactivate Obj
    
    alt move allowed
        Env->>Env: Update Agent Position
    end
    
    Env->>Env: Calculate Shaping Reward
    
    Env-->>Main: next_state, reward, done
    deactivate Env
    
    Main->>Agent: learn(state, action, reward, next_state)
    activate Agent
    Agent->>Agent: Update Q-Table (Bellman Eq)
    deactivate Agent

    Note over Main: End of Step
```

### 2.2 ASCII Art Format (Text-Based)

```text
TrainingLoop        Agent            WarehouseEnv      GameObject (Wall/Pkg)
     |                |                   |                    |
     | select_action()|                   |                    |
     |--------------->|                   |                    |
     |                |                   |                    |
     |    action      |                   |                    |
     |<---------------|                   |                    |
     |                |                   |                    |
     |  step(action)  |                   |                    |
     |--------------->|                   |                    |
     |                |  calc_target()    |                    |
     |                |-------------------|                    |
     |                |                   |                    |
     |                |    interact()     |                    |
     |                |--------------------------------------->|
     |                |                   |                    |
     |                |  (move, r, done)  |    Polymorphic     |
     |                |<---------------------------------------|
     |                |                   |     Response       |
     |                |                   |                    |
     |                | update_pos()      |                    |
     |                | [If move allowed] |                    |
     |                |---|               |                    |
     |                |   |               |                    |
     |                |<--|               |                    |
     |                |                   |                    |
     | obs, reward... |                   |                    |
     |<---------------|                   |                    |
     |                |                   |                    |
     |     learn()    |                   |                    |
     |--------------->|                   |                    |
     |                |                   |                    |
     | update_q()     |                   |                    |
     |---|            |                   |                    |
     |   |            |                   |                    |
     |<--|            |                   |                    |
     |                |                   |                    |
     v                v                   v                    v
```
