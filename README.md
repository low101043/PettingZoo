# PettingZoo
PettingZoo is Python library of enviroments for conducting research in multi-agent reinforcement learning. It's basically a multi-agent version of OpenAI's Gym library.


## Games and Installation

PettingZoo breaks its games down into several categories, largely including games from other's which we've ported to our consistent API, in many cases fixed, and centrally distribute.

* atari: A collection of easily runnable multi-player Atari games in the Stella emulator, similar to what you find in Gym.
* classic: Enviroments for classical games that two humans play against each other (rock paper scissors, chess, Texas hold 'em poker, go, etc.)
* gamma: Graphical games developed by us, in PyGame. All games are cooperative, and many pose features very challenging to reinforcement learning.
* magent: A set of enviroments involving massive numbers of agents doing various tasks, originally from https://github.com/geek-ai/MAgent
* mpe: 'Multi-agent Particle Enviroments', a set of simple nongraphical communication tasks created by OpenAI: https://github.com/openai/multiagent-particle-envs
* robotics: A collection of 3D multiagent robot enviroments, simulated with PyBullet
* sisl: An eclectic collection of 3 games developed by SISL, originally from https://github.com/sisl/MADRL

To install a set of games, use `pip3 install pettingzoo[atari]`, substituting atari for other classes of games when desired.

We support Python 3.6, 3.7 and 3.8.


## Base API

Using environments in PettingZoo is very similar to Gym, i.e. you load an enviroment via:

```
from pettingzoo.gamma import pistonball
env = pistonball.env([custom enviroment parameters])
```

Our API models the games as Agent Enviroment Cycle (AEC) games. This is because the conventional game model (Markov Games) descendant APIs for multi-agent RL can't model many games that you'd like to, [Arxiv Link].

Assuming you have a list of policy functions, interacting with your enviroment looks this looks like:

```
policy_list = [policy_1, policy_2 ... policy_n]
observation = env.reset()
while True:
	for agent in range(0,env.num_gents):
		policy  = policy_list[agent]
		action = eval(policy(observation)
    	observation, reward, done, info = env.turn(action)
 ```

This is almost the same as a normal Gym game, but with one noteably exception: You use env.turn(action) instead of env.step(action). A turn moves a single agent at a time, comprising a full step after all agent's have taken their turn.


## Utils API

We include popular preprocessing methods out of the box:

```
from pettingzoo.utils import wrapper
env = wrapper(env, frame_stacking=4, color_reduction='', down_scale=(x_factor,y_factor), flatten=False,
scale=(env_min,env_max))
```

Only games with a graphical observation space can be greyscaled, downscaled, or flattened.

Additionally, we have a basic test to check for enviroment compliance, if you've made your own custom enviroment with PettingZoo and want to get a good guess about whether or not you did it right.

```
from pettingzoo.utils import children
children(env, save_image_observations=False)
```

Set `save_image_observations=True` if you want to save all of the observations of the first 2 steps of enviroment to disk as .png files, in the directory in which you run this command. This is very helpful in debugging graphical enviroments. 

## Demos

Often, you want to be able to play a game or watch it play to get an impression of how it works before trying to learn it. Only games with a graphical output, or certain vector output games with a visualization added, can be rendered. 

Of the games that can be played, many can be played by humans, and functionality to do so is included.

```
from pettingzoo.gamma import pistonball
pistonball.manual_control([enviroment specs])
```

For viewable games that can't be played by humans, you easily can get an impression for them by watching a random policy control all the actions, via:

```
from pettingzoo.utils import random_demo
random_demo(env)
```

## Documentation
For more detailed documentation about all the different enviroments, and configuration options for them go to [website].

We maintain a leaderboard for the best performance on every game, included in the documentation for each game. If you have a score that you would like to be included, please submit a pull request. Only pull requests that link to code for reproducibility will be accepted. You must also use the default parameters for every game, with the exception that we 2 have seperate leaderboards for games that support both continuous and discrete action spaces.


## Development Notes

All game code should be compliant with flake8 --ignore E501,E731,E741. We're open to adding more exceptions at this time if needed.

The following games should be done (though they aren't compliant with the wrapper API yet):

* gamma/knights_archers_zombies
* gamma/pistonball
* gamma/cooperative_pong
* sisl/pursuit
* sisl/multiwalker
* sisl/waterworld

The following games are under active development:

* gamma/prospector (Rui)
* gamma/prison (Mario)
* classic/rock_paper_scissors (Sharry)
* classic/rock_paper_scissors_lizard_spock (Sharry)
* clasic/checkers (Tianchen)
* classic/tictactoe (Upamanyu)
* classic/connect_four (Upamanyu)
* classic/mahjong (rlcard) (Luis)
* classic/texasholdem (rlcard) (Luis)
* classic/texasholdem_nolimit (rlcard) (Luis)
* classic/uno (rlcard) (Luis)
* mpe/*

Development has not yet started on the following games:

* classic/backgammon
* classic/chess (https://github.com/niklasf/python-chess)
* classic/go
* magent/*
* atari/*
* robotics/*

Future wrapper work:
"action_cropping and obs_padding implement the techniques described in *Parameter Sharing is Surprisingly Useful for Deep Reinforcement Learning* to standardized heterogenous action spaces."

## Requirements

Requirements are being kept below until we get the requirments.txt issues fixed

```
gym>=0.15.4	
pygame==2.0.0.dev6	
scikit-image>=0.16.2	
numpy>=1.18.0	
matplotlib>=3.1.2
pymunk>=5.6.0
gym[box2d]>=0.15.4
python-chess
```

## OS Support

We support Linux first and it's what we do our CI testing on. We support on macOS, but do not do CI testing on it. We don't explicitly support Windows, but we will accept PRs related to problems running on Windows. We're open to adding formally supporting Windows and adding CI for macOS and Windows if a party was interested in helping and had servers to run the CI on, but we don't currently have the resources to do so.


## Old API for Development Reference

```
observations = env.reset()
while True:
    actions = policy(observations)
    # add env.render() here if you want to watch the game playing and the game supports it
    observations, rewards, dones, info = env.step(actions)
```

The way we handle multiple agents is that the enviroment assigns each agent an integer ID, and everything is passed as dictionaries with the IDs as keys, i.e.:

```
observations = {0:[first agent's observation], 1:[second agent's observation] ... n:[n-1th agent's observation]}
actions = {0:[first agent's action], 1:[second agent's action] ... n:[n-1th agent's action]}
rewards = {0:[first agent's reward], 1:[second agent's reward] ... n:[n-1th agent's reward]}
dones = {0:[first agent's done state], 1:[second agent's done state] ... n:[n-1th agent's done state]}
```
