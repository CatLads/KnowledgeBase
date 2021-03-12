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



