# Model-Based RL Agent for FrozenLake

## Project Title & Overview
This project implements a Model-Based Reinforcement Learning agent to solve the Gymnasium FrozenLake-v1 environment. The agent employs a hybrid approach, combining Model-Free Q-Learning with Model-Based Value Iteration. By learning an approximate model of the environment's dynamics, the agent can plan and refine its policy more effectively than using Q-learning alone.

## Key Features / Algorithms
1.  **Hybrid Approach**: The agent uses **Q-Learning** (Model-Free) for immediate value updates based on experience and **Value Iteration** (Model-Based) for long-term planning using the learned model.
2.  **Model Learning**: The environment model is learned empirically:
    *   **Transition Dynamics ($P(s'|s, a)$)**: Estimated by tracking `transition_counts` for every state-action-next_state triplet.
    *   **Reward Function ($R(s'|s, a)$)**: Estimated by accumulating `reward_sums` and averaging them over the number of transitions.
3.  **Exploration Strategy**: An **$\epsilon$-greedy strategy** is used to balance exploration and exploitation, with $\epsilon$ decaying over time to favor exploitation as the agent learns.

## Dependencies
To actuate this project, you need the following Python libraries:
*   `gymnasium`
*   `numpy`
*   `matplotlib`
*   `pickle` (Standard library)

You can install the necessary external dependencies using pip:
```bash
pip install gymnasium numpy matplotlib
```

## How to Run
Execute the main script from the terminal to train and evaluate the agent:
```bash
python frozen_lake.py
```

## Output Files
Upon successful execution, the following files will be generated in the working directory:
*   `model_based_agent.pkl`: A serialized file containing the trained `ModelBasedAgent` object.
*   `model_based_learning.png`: A plot visualizing the learning progress (moving sum of rewards over the last 100 episodes).

## Contribution List
*   The core code was implemented by the student/developer.


---
### 2. UML Class Diagram Explanation
Here is the detailed specification for the [ModelBasedAgent](cci:2://file:///c:/Users/haru2/OneDrive/%E6%96%87%E4%BB%B6/12_10/frozen_lake.py:5:0-59:85) class diagram.
**Class Name:** [ModelBasedAgent](cci:2://file:///c:/Users/haru2/OneDrive/%E6%96%87%E4%BB%B6/12_10/12_15_2.py:5:0-59:85)
**Attributes:**
*   `- n_states`: `int` — Total number of states in the environment.
*   `- n_actions`: `int` — Total number of actions available.
*   `- gamma`: `float` — Discount factor ($\gamma$) for future rewards.
*   `- epsilon`: `float` — Current exploration rate ($\epsilon$).
*   `- epsilon_decay`: `float` — Rate at which $\epsilon$ decreases.
*   `- min_epsilon`: `float` — Minimum value for $\epsilon$.
*   `- q_table`: `numpy.ndarray` — Matrix storing Q-values for (state, action) pairs.
*   `- transition_counts`: `numpy.ndarray` — 3D array counting occurrences of $(s, a, s')$ transitions.
*   `- reward_sums`: `numpy.ndarray` — 3D array accumulating structure for rewards observing $(s, a, s')$.
*   `- model_prob`: `numpy.ndarray` — Learned transition probability model $P(s'|s, a)$.
*   `- model_rewards`: `numpy.ndarray` — Learned expected reward model $R(s'|s, a)$.
*   `- V`: `numpy.ndarray` — Value function array used specifically for Model-Based planning.
**Methods:**
*   `+ __init__(n_states, n_actions, gamma, epsilon, epsilon_decay, min_epsilon)` — Initializes the agent with environment parameters and hyperparameters.
*   `+ choose_action(state, training=True)` — Selects an action using the $\epsilon$-greedy policy during training or greedy policy during evaluation.
*   `+ update_q(state, action, reward, next_state)` — Performs a standard Model-Free Q-learning update on the `q_table`.
*   `+ update_model(state, action, reward, next_state)` — Updates the internal environment model (`transition_counts` and `reward_sums`) based on the observed transition.
*   `+ value_iteration()` — Executes the Model-Based planning step to update policy values using `model_prob` and `model_rewards`.
*   `+ decay_epsilon()` — Reduces the epsilon value by the decay factor.
To generate a visual UML diagram using a tool like Mermaid or PlantUML, you can use the following descriptive block:
```mermaid
classDiagram
    class ModelBasedAgent {
        -int n_states
        -int n_actions
        -float gamma
        -float epsilon
        -float epsilon_decay
        -float min_epsilon
        -ndarray q_table
        -ndarray transition_counts
        -ndarray reward_sums
        -ndarray model_prob
        -ndarray model_rewards
        -ndarray V
        
        +__init__(n_states, n_actions, gamma, epsilon, epsilon_decay, min_epsilon)
        +choose_action(state, training)
        +update_q(state, action, reward, next_state)
        +update_model(state, action, reward, next_state)
        +value_iteration()
        +decay_epsilon()
    }