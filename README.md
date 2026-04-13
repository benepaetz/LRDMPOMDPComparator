**THIS IS AN EXTENTION OF LRDM-POMDP BY TOBIAS KENNEDY https://github.com/Tobias-Kennedy/LRDM-POMDP**

# LRDM-POMDP

*A simulation of a remote mirroring network, using Partially Observable Markov Decision Processes (POMDPs) to optimise Non-Functional Requirement (NFR) satisfaction time and minimise latency.*

---

## Overview

**LRDMSim** is a simulator that emulates a Remote Data Mirroring (RDM) network.  
- Full explanation: [ACM Paper](https://dl.acm.org/doi/pdf/10.1145/3643915.3644106)  
- Built upon: [RDMSim](https://ieeexplore.ieee.org/document/9462042), which lacks latency tracking.

This project integrates with **[SolvePOMDP](https://github.com/AlgTUDelft/SolvePOMDP)** — a tool for computing and solving POMDPs.  
LRDMSim is packaged as a `.jar` dependency within this SolvePOMDP project, enabling:
- Control of the simulation via effectors
- Monitoring of network state via probes

---

## Setup Instructions

To run this project:

1. Add the following `.jar` dependencies (located in the `/lib` folder) to your Java classpath:

```
lib/
├── gurobi.jar
├── json-simple-1.1.jar
├── json-simple-3.1.1.jar
├── LRDMSim-1.0.0.jar
└── LRDMSim-1.0.0-jar-with-dependencies.jar
```

2. If needed, copy them to a different local directory and include that directory in your IDE's classpath settings.

---

## Configuration Files

### `sim.conf`

Controls simulator parameters:

```ini
# Original LRDMSim Parameters
debug=true
sim_time=500
num_mirrors=50
num_links_per_mirror=2
startup_time_min=5
startup_time_max=10
ready_time_min=2
ready_time_max=20
stop_time_min=2
stop_time_max=5
link_activation_time_min=5
link_activation_time_max=10
fileSize=80
min_bandwidth=2
max_bandwidth=8
fault_probability=0.005

# Added Parameters
bw_thresh           # Bandwidth threshold used for POMDP decisions and graphs
al_thresh           # Active link threshold used for POMDP decisions and graphs
ttw_thresh          # Time to write threshold used for POMDP decisions and graphs
second_bw_thresh    # Bandwidth threshold used for updating the threshold
second_al_thresh    # Active link threshold used for updating the thresholds
second_ttw_thresh   # Time to write threshold used for updating the threshold
third_bw_thresh     # Bandwidth threshold used for updating the threshold
third_al_thresh     # Active link threshold used for updating the thresholds
third_ttw_thresh    # Time to write threshold used for updating the threshold
```

### `domains/LRDM.POMDP`

This is the POMDP definition file used by SolvePOMDP.  
It includes:
- Actions, States, Observations
- Rewards and Transition Probabilities
- Discount factor for decision-making

---

## Output & Results

Two main output files are generated:

- `LATRegressionResultsPOMDP.txt` – Logs cumulative latency at each timestep
- `TOPChanged.txt` – Logs topology changes selected by the POMDP

> The MC, MP, and MR regression results files are currently not functional but would originally record the following at each timestep:
> - Cost (Bandwidth)
> - Performance (Time to Write)
> - Reliability (Active Links)

---

## Integration with SolvePOMDP

To connect LRDMSim to SolvePOMDP:

A custom connector class was developed:  
`src/main/java/remotemirroring/RDMSimConnector.java`

Responsibilities:
- Initialises the simulation
- Retrieves probes and effectors
- Parses thresholds from `sim.conf` for dynamic graphing


## Comparator

To compare the performance of different POMDPs on multiple LRDMSim configurations:

A Comparator package was created:
`src/main/java/Comparator`

Responsibilities:
- Executing SolvePOMDP multiple times
- collecting performance Data
- Visualization of the performance Data

## POMDP Generator

To generate multiple variants of the LRDM.POMDP

Python source code in:
`src/pythonPOMDPGenerator`
