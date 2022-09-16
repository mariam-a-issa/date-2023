# Experiment `maximal_attack-v3`_`dqn`

This is an experiment in the `maximal_attack-v3` environment.
An environment where the attack is following the `attack_maximal` attack policy.
The `attack_maximal` policy entails that the attacker will always attack the attribute with
the maximum value out of all of its neighbors. The defender is implemented with a
random defense policy.
 
This experiment trains a defender agent using DQN to act optimally in the given
environment and detect the attacker.

The network configuration of the environment is as follows:

- `num_layers=2` (number of layers between the start and end nodes)
- `num_servers_per_layer=3`
- `num_attack_types=10`
- `max_value=9`

<p align="center">
<img src="docs/env.png" width="600">
</p>

The starting state for each node in the environment is initialized as follows (with some randomness for where the vulnerabilities are placed).

- `defense_val=2`
- `attack_val=0`
- `num_vulnerabilities_per_node=1` (which type of defense at the node that is vulnerable is selected randomly when the environment is initialized)
- `det_val=2`
- `vulnerability_val=0` 
- `num_vulnerabilities_per_layer=3`

The environment has sparse rewards (+1,-1 rewards are given at the terminal state of each episode).
 The environment is partially observed (attacker can only see attack attributes of neighboring nodes, defender can only see defense attributes)

## Environment 

- Env: `random_attack-v3`

## Algorithm

- DQN with linear exploration annealing 
 
## Instructions 

To configure the experiment use the `config.json` file. Alternatively, 
it is also possible to delete the config file and edit the configuration directly in
`run.py` (this will cause the configuration to be overridden on the next run). 

Example configuration in `config.json`:

```json
{
    "attacker_type": 6,
    "defender_type": 1,
    "env_name": "idsgame-maximal_attack-v3",
    "idsgame_config": null,
    "initial_state_path": null,
    "logger": null,
    "mode": 0,
    "output_dir": "/media/kim/HDD/workspace/gym-idsgame/experiments/training/v3/maximal_attack/dqn",
    "py/object": "gym_idsgame.config.client_config.ClientConfig",
    "q_agent_config": {
        "alpha": 1e-05,
        "attacker": true,
        "attacker_load_path": null,
        "checkpoint_freq": 1000,
        "defender": false,
        "defender_load_path": null,
        "dqn_config": {
            "batch_size": 32,
            "gpu": true,
            "hidden_activation": "ReLU",
            "hidden_dim": 64,
            "input_dim": 88,
            "loss_fn": "Huber",
            "lr_decay_rate": 0.9999,
            "lr_exp_decay": true,
            "num_hidden_layers": 1,
            "optimizer": "Adam",
            "output_dim": 80,
            "py/object": "gym_idsgame.agents.q_learning.dqn.dqn_config.DQNConfig",
            "replay_memory_size": 10000,
            "replay_start_size": 1000,
            "target_network_update_freq": 1000,
            "tensorboard": true,
            "tensorboard_dir": "/media/kim/HDD/workspace/gym-idsgame/experiments/training/v3/maximal_attack/dqn/results/tensorboard"
        },
        "epsilon": 1,
        "epsilon_decay": 0.999,
        "eval_episodes": 100,
        "eval_frequency": 1000,
        "eval_log_frequency": 1,
        "eval_render": false,
        "eval_sleep": 0.9,
        "gamma": 0.99,
        "gif_dir": "/media/kim/HDD/workspace/gym-idsgame/experiments/training/v3/maximal_attack/dqn/results/gifs",
        "gifs": true,
        "logger": null,
        "min_epsilon": 0.05,
        "num_episodes": 10000,
        "py/object": "gym_idsgame.agents.q_learning.q_agent_config.QAgentConfig",
        "random_seed": 0,
        "render": false,
        "save_dir": "/media/kim/HDD/workspace/gym-idsgame/experiments/training/v3/maximal_attack/dqn/results/data",
        "train_log_frequency": 100,
        "video": true,
        "video_dir": "/media/kim/HDD/workspace/gym-idsgame/experiments/training/v3/maximal_attack/dqn/results/videos",
        "video_fps": 5,
        "video_frequency": 101
    },
    "random_seeds": [
        0,
        999,
        299,
        399,
        499
    ],
    "run_many": true,
    "simulation_config": null,
    "title": "TrainingDQNAgent vs DefendMinimalDefender"
}
```

Example configuration in `run.py`:

```python
dqn_config = DQNConfig(input_dim=88, output_dim=80, hidden_dim=64, replay_memory_size=10000,
                           num_hidden_layers=1,
                           replay_start_size=1000, batch_size=32, target_network_update_freq=1000,
                           gpu=True, tensorboard=True, tensorboard_dir=default_output_dir() + "/results/tensorboard",
                           loss_fn="Huber", optimizer="Adam", lr_exp_decay=True, lr_decay_rate=0.9999)
q_agent_config = QAgentConfig(gamma=0.999, alpha=0.00001, epsilon=1, render=False, eval_sleep=0.9,
                              min_epsilon=0.05, eval_episodes=100, train_log_frequency=100,
                              epsilon_decay=0.999, video=True, eval_log_frequency=1,
                              video_fps=5, video_dir=default_output_dir() + "/results/videos", num_episodes=10000,
                              eval_render=False, gifs=True, gif_dir=default_output_dir() + "/results/gifs",
                              eval_frequency=1000, attacker=True, defender=False, video_frequency=101,
                              save_dir=default_output_dir() + "/results/data", dqn_config=dqn_config,
                              checkpoint_freq=1000)
env_name = "idsgame-maximal_attack-v3"
client_config = ClientConfig(env_name=env_name, attacker_type=AgentType.DQN_AGENT.value,
                             mode=RunnerMode.TRAIN_ATTACKER.value,
                             q_agent_config=q_agent_config, output_dir=default_output_dir(),
                             title="TrainingDQNAgent vs DefendMinimalDefender",
                             run_many=True, random_seeds=[0, 999, 299, 399, 499])
```

After the experiment has finished, the results are written to the following sub-directories:

- **/data**: CSV file with metrics per episode for train and eval, e.g. `avg_episode_rewards`, `avg_episode_steps`, etc.
- **/gifs**: If the gif configuration-flag is set to true, the experiment will render the game during evaluation and save gif files to this directory. You can control the frequency of evaluation with the configuration parameter `eval_frequency` and the frequency of video/gif recording during evaluation with the parameter `video_frequency`
- **/hyperparameters**: CSV file with hyperparameters for the experiment.
- **/logs**: Log files from the experiment
- **/plots**: Basic plots of the results
- **/videos**: If the video configuration-flag is set to true, the experiment will render the game during evaluation and save video files to this directory. You can control the frequency of evaluation with the configuration parameter `eval_frequency` and the frequency of video/gif recording during evaluation with the parameter `video_frequency`
  

## Example Results

<p align="center">
<img src="docs/avg_summary.png" width="800">
</p>

### Policy Inspection

#### Evaluation after 0 Training Episodes

<p align="center">
<img src="docs/episode_0.gif" width="600">
</p> 

#### Evaluation after 15000 Training Episodes

<p align="center">
<img src="docs/episode_15000.gif" width="600">
</p>  


## Commands

Below is a list of commands for running the experiment

### Run

**Option 1**:
```bash
./run.sh
```

**Option 2**:
```bash
make all
```

**Option 3**:
```bash
python run.py
```

### Run Server (Without Display)

**Option 1**:
```bash
./run_server.sh
```

**Option 2**:
```bash
make run_server
```

### Clean

```bash
make clean
```
