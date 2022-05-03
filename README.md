# Mean Field Adversarial Inverse Reinforcement Learning

## Table of Contents

- [Background](#background)
- [Install](#install)
- [Usage](#usage)

## Background
This project is the code implementation for the paper ***ean Field Adversarial Inverse Reinforcement Learning***.There are two packages: ```MADDPG4MFG``` and ```MFIRL```. ```MADDPG4MFG``` implements the forward reinforcement learning for mean field agent that computes a mean field equilibrium. ```MFIRL``` implements the agent-level inverse reinforcement learning for mean field games that learns a reward function given demonstrations sampled from the mean field equilibrium by ```MADDPG4MFG```. Two simulated environments are implemented: *malware spread* and *investment in product quality*.

## Install
This project uses [Python](https://www.python.org/downloads/release/python-383/) 3.8.3, [Gym](http://gym.openai.com)  0.17.2, [PyTorch](https://pytorch.org) 1.5.0, and [NumPy](https://numpy.org)  1.18.1.

```sh
$ pip3 install torch==1.5.0
```
```sh
$ pip3 install gym==0.17.2
```
```sh
$ pip3 install numpy==1.18.1
```

## Usage
### Experiments for Original Environments
#### Train the Expert Policy
* Navigate to the project workspace 
* Run the following command

```sh
$ python3 ./MADDPG4MFG/main.py --scenario_name malware_spread --is_original_dynamics 0
```
```sh
$ python3 ./MADDPG4MFG/main.py --scenario_name investment --is_original_dynamics 0
```
The files of saved models would be found in ```MADDPG4MFG/output_models/```. Log files would be found in ```./MADDPG4MFG/output_tracjectories/```.

#### Generate Expert Demonstrations
* Run the following command to generate expert demonstrations consisting of a collection of state-action trajectories.

```sh
$ python3 ./MADDPG4MFG/demo_gen.py --scenario_name malware_spread
```
```sh
$ python3 ./MADDPG4MFG/demo_gen.py --scenario_name investment
```
The files of output trajectories would be found in ```MADDPG4MFG/output_demos/```.

#### Mean-Field Inverse Reinforcement Learning
* Copy expert demonstrations to the workspace of ```MFIRL```

```sh
$ cp -r ./MADDPG4MFG/output_demos/malware_spread  ./MFIRL/demos/
```
```sh
$ cp -r ./MADDPG4MFG/output_demos/investment  ./MFIRL/demos/
```
* Run MFIRL-QBOL algorithm

```sh
$ python3 ./MFIRL/main.py --scenario_name malware_spread --is_original_dynamics 0 --policy_model BOL
```
```sh
$ python3 ./MFIRL/main.py --scenario_name investment --is_original_dynamics 0 --policy_model BOL
```

* Run MFIRL-QMAX algorithm

```sh
$ python3 ./MFIRL/main.py --scenario_name malware_spread --is_original_dynamics 0 --policy_model MAX
```
```sh
$ python3 ./MFIRL/main.py --scenario_name investment --is_original_dynamics 0 --policy_model MAX
```

*  Learned reward models would be output to ```./MFIRL/models_output/ALMFIRL/```. Elicited policies would be output to ```./MFIRL/policy_output ```. Log files would be output to ```./MFIRL/loss_output ```.

#### Behaviour Cloning
* Run the following command

```sh
$ python3 ./MFIRL/behaviour_cloning.py --scenario_name malware_spread --train_or_simulate 0
```
```sh
$ python3 ./MFIRL/behaviour_cloning.py --scenario_name investment --train_or_simulate 0
```

* Learned policy models  would be output to ```./MFIRL/models_output/BC/```. Log files would be output to ```./MFIRL/loss_output ```.

#### Random Selection
* Run the following command

```sh
$ python3 ./MFIRL/random_select.py --scenario_name malware_spread --train_or_simulate 0
```
```sh
$ python3 ./MFIRL/random_select.py --scenario_name investment --train_or_simulate 0
```
* Log files would be output to ```./MFIRL/loss_output ```.

### Experiments for New Environments

#### Retrain an Expert Policy
* Run the following command

```sh
$ python3 ./MADDPG4MFG/main.py --scenario_name malware_spread --is_original_dynamics 1
```
```sh
$ python3 ./MADDPG4MFG/main.py --scenario_name investment --is_original_dynamics 1
```

* The files of saved models would be found in ```MADDPG4MFG/output_models/``.

#### Train a policy Under the Learned Reward Function of MFIRL
* Copy the reward model to the workspace of ```MADDPG4MFG```.

```sh
$ cp -r ./MFIRL/models_output/ALMFIRL/ ./MADDPG4MFG/reward_models/
```
* Rename the reward model as follows: **malware_spread_BOL.pt**, **malware_spread_MAX.pt** , **investment_BOL.pt** and **investment_MAX.pt**.
* Use reward function learned by MFIRL-QBOL

```sh
$ python3 ./MADDPG4MFG/main.py --scenario_name malware_spread --is_original_dynamics 1 reward_model 1
```
```sh
$ python3 ./MADDPG4MFG/main.py --scenario_name investment --is_original_dynamics 1 reward_model 1
```
* Use reward function learned by MFIRL-QMAX

```sh
$ python3 ./MADDPG4MFG/main.py --scenario_name malware_spread --is_original_dynamics 1 reward_model 2
```
```sh
$ python3 ./MADDPG4MFG/main.py --scenario_name investment --is_original_dynamics 1 reward_model 2
```
* The files of saved models would be found in ```MADDPG4MFG/output_models/```. Log files would be found in ```./MADDPG4MFG/output_tracjectories/```.

#### Use the policy model learned by Behaviour Cloning
* Copy expert policies and mean fields to the workspace of ```MFIRL```

```sh
$ cp -r ./MADDPG4MFG/output_tracjectories/ ./MFIRL/new_dynamics/
```
* Run the following command

```sh
$ python3 ./MFIRL/behaviour_cloning.py --scenario_name malware_spread --train_or_simulate 1 --load_model_dir ./MFIRL/output_models/BC/your BC model name
```
```sh
$ python3 ./MFIRL/behaviour_cloning.py --scenario_name investment --train_or_simulate 1 --load_model_dir ./MFIRL/output_models/BC/your file name
```
* Log files would be found in ```./MFIRL/loss_output/scenario_name/new_dynamics```.

#### Random Selection
* Run the following command

```sh
$ python3 ./MFIRL/random_select.py --scenario_name malware_spread --train_or_simulate 1 
```
```sh
$ python3 ./MFIRL/random_select.py --scenario_name investment --train_or_simulate 1 
```
* Log files would be found in ```./MFIRL/loss_output/scenario_name/new_dynamics```.
