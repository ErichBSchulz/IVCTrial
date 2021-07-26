# IVCTrial

This is set of notebooks for doing power calculations based on simulated 
`student` success in their `attempt`s at a procedure, as a repeated binary outcome.

Power calculations used the following steps:

1. Definition of a generic exponential  learning curve model based on definition of:
    1. Baseline success rate
    1. Final success rate
    1. Number of attempts to improve half way
1. Four separate populations of students were randomly generated: (`initial` = "best guess", `second` = "best guess at more experienced students", `uniform` = "a favourable low variabililty population", `variable` = "a population with great variability in success"): These populations are generated randomly by specifying:
    1. alpha and beta parameters of a beta distribution of initial success
    1. a maximal proportion of failure risk to discount which is discounted by a uniformly random (0-1) number
    1. the Rayleigh distribution parameter for the number of attempts to improve halfway
1. For each student population a series of student groups (`trials`) were randomly drawn then a set of random `attempt`s were simulated in groups. Each group had their predicted failure rate discounted by a fixed proportion to represent different effects. The groups included a `nodif` group which acts as a reference for detecting type 2 error, and a series of groups representing different `interventionX`s with growing difference from the control group (`generate_trial_data()`). These are saved, for convenience at different levels of aggregation:
    1. `ad` - main attempt level records
    2. `partd` - participant
    3. `td` - group level data
1. From the simulated groups different outcomes were assessed using "Generalized Estimating Equations" (`fit_models()`) using trials of different pairs of groups and increasing sample sizes. Each comparison was the classified as:
    1. wrong (p < 0.05 and wrong conclusion reached)
    1. ns (p >= 0.05)
    1. sig (0.05 > p > 0.01)
    1. vsig (p <= 0.01)
1. The simulated studies were then filtered to find the smallest study where at least 80% reached a significance of p < 0.05. (Using the  `SimulationAnalysis`)
