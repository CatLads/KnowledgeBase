# Flatland

## Observations

The `GlobalObsForRailEnv` is the default one, returning:

```python
class GlobalObsForRailEnv(ObservationBuilder):
    """
    Gives a global observation of the entire rail environment.
    The observation is composed of the following elements:

        - transition map array with dimensions (env.height, env.width, 16),\
          assuming 16 bits encoding of transitions.

        - obs_agents_state: A 3D array (map_height, map_width, 5) with
            - first channel containing the agents position and direction
            - second channel containing the other agents positions and direction
            - third channel containing agent/other agent malfunctions
            - fourth channel containing agent/other agent fractional speeds
            - fifth channel containing number of other agents ready to depart

        - obs_targets: Two 2D arrays (map_height, map_width, 2) containing respectively the position of the given agent\
         target and the positions of the other agents targets (flag only, no counter!).
    """
```

Every `env.step()` provides two dicts: the `observation_dict` and the `informations_dict`.

This is what happens when we `env.reset()`

```python
({0: (array([[[0., 0., 0., ..., 0., 0., 0.],
           [0., 0., 1., ..., 0., 0., 0.],
           [0., 0., 1., ..., 0., 0., 0.],
           ...,
           [0., 0., 0., ..., 0., 0., 0.],
           [0., 0., 0., ..., 0., 0., 0.],
           [0., 0., 0., ..., 0., 0., 0.]]]), 
      array([[[-1., -1., -1., -1.,  0.],
          [[-1., -1., -1., -1.,  0.],
           [-1., -1., -1., -1.,  0.],
           [-1., -1., -1., -1.,  0.],
           ...,
           [-1., -1., -1., -1.,  0.],
           [-1., -1., -1., -1.,  0.],
           [-1., -1., -1., -1.,  0.]]]), 
      array([
          [[0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.]],
   				...
          [[0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.],
           [0., 0.]],
          ]))},
 {'action_required': {0: True},
  'malfunction': {0: 0},
  'speed': {0: 1.0},
  'status': {0: <RailAgentStatus.READY_TO_DEPART: 0>}})
```

## Global observation[¶](https://flatland.aicrowd.com/getting-started/env/observations.html#global-observation)

The global observation is the simplest one. In this case, every agent is provided a global view of the full Flatland environment. This can be compared to the full, raw-pixel data used in Atari games. The size of the observation space is `h × w × c`, where `h` is the height of the environment, `w` is the width of the environment and `c` is the number of channels of the environment. These channels can be modified by the participants but in the initial configuration, we include the following `h × w` channels:

- **Transition maps:** provides a unique value for each type of transition map and its orientation. Its dimensions is `h × w × 16` assuming 16 bits encoding of transitions. Transition maps represent the movements allowed on a cell, [read more about them here](https://flatland.aicrowd.com/getting-started/env/custom_observations.html#transitions-maps).

  - ```python
    env.reset()[0][0][0].shape -> (16, 16, 16)
    ```

- **Agent states:** A 3D array `h × w × 5` containing:

  - **Channel 0:** one-hot representation of the self agent position and direction

  - **Channel 1:** other agents’ positions and direction

  - **Channel 2:** self and other agents’ malfunctions

  - **Channel 3:** self and other agents’ fractional speeds

  - **Channel 4:** number of other agents ready to depart from that position

  - ```python
    env.reset()[0][0][1].shape -> (16, 16, 5)
    ```

- **Agent targets:** A 3D arrays `h × w × 2` containing respectively the position of the current agent target, and the positions of the other agents’ targets. The positions of the targets of the other agents is a simple `0`/`1` flag, therefore this observation doesn’t indicate where each other agent is heading to.

  - ```python
    env.reset()[0][0][2].shape -> (16, 16, 2)
    ```

This observation space is well suited for single-agent navigation but does not provide enough information to solve the multi-agent navigation task, thus participants must improve on this observation space to solve the challenge.

The return of `step()` is the following:

```python
return self._get_observations(), self.rewards_dict, self.dones, info_dict
```

While the one from `reset()` is **different** (porca puzzolina):

```python
return observation_dict, info_dict
```

So, in step, we get a different "end of the return" after the obs:

```python
 {0: -1.0},
 {0: False, '__all__': False},
 {'action_required': {0: True},
  'malfunction': {0: 0},
  'speed': {0: 1.0},
  'status': {0: <RailAgentStatus.READY_TO_DEPART: 0>}})
```

To get the reward, we simply `env.step({0: RailEnvActions.DO_NOTHING})[1]`

## Reimplementing an Env

There's a TF doc [here](https://www.tensorflow.org/agents/tutorials/2_environments_tutorial) that does exactly what we want to do. Nice.

