# Ensemble Containers

We have been using the [metrics operator](https://github.com/converged-computing/metrics-operator) typically as a means to run experiments, or different apps in Kubernetes. It is designed to take as input the application name and parameters, and provide output to logs in a way that is parse-able via the log file and a custom Python module. For another project, the [ensemble-operator](https://github.com/converged-computing/ensemble-operator) we can define ensembles of work, and then rules (algorithms) for autoscaling. This is an OK approach, but I want more. Specifically:

- a collection of apps that can be orchestrated and controlled to simulate a larger study
- more definition of patterns within the provided applications
- derivatives of the applications with different MPI, etc.

What I'd like is an ability to programmatically generate an experiment that uses a set of containers (with known application patterns) that feeds into an ensemble that can then be further studied. The use case is testing across different cloud environments, networking or other technologies within a space, or user-space Kubernetes.

While the parameter space is large and we cannot collect everything, I don't think we have to. A sampling of the space is enough.
In terms of how this has worked, this has largely been a "hard coded with MPI via ssh" approach. However, what I've realized is that we get much better orchestration with Flux, both in terms of having the job queue and controlling the mapping of the apps. Yes, this can be done with MPIrun, but it's an environment that has slightly less hooks and controls with respect to job preambles and cleanup, monitoring, etc. Thus, these are the changes that I want to make.

1. A matrix of HPC application containers built with different technologies, all oriented to use Flux.
2. For each application in the matrix, running with simple benchmarks to measure common patterns. Likely we can start with percentages of MPI like I originally wanted to earlier this year. We would want to start with one node, and do strong scaling and see how the application performs in an environment. 

The above will require a design to run an application, and measure it. This likely will involve something with Kubernetes, or Flux. I need to think about it. It could be very application-dependent.

## New Thinking

The new thinking is that we then want to take what we know and have learned for the above, and be able to design workflows that we know will have particular durations and patterns of design. These are the dummy workflows we can use to test different attributes of User-space Kubernetes. Flux in Kubernetes is an ideal environment because it will:

1. Allow us to build isolated application environments
2. Not worry about integration between a container and the host, the container is the host
3. Have Flux added on the fly.

For this first stage of work, I'm going to build a ton of containers. I am scoping to CPU because obtaining and funding GPU in the cloud is nontrivial. I'm then going to understand the patterns of the applications inside under a particular set of conditions. I'll then go back to the ensemble operator and create a tool that generates the ensembles (the full experiments) to be orchestrated with Flux via the Flux Operator, which is already implemented for the operator. I need to rethink how the operator works with the Flux MiniCluster - right now there is an associated Python module that gets paired, but maybe we can do better. I'd like to also add an ability for the operator to complete the runs, save the output and events, and then push and clean up.

## Containers

- [aws](aws): these are typically oriented to have libfabric.

All containers will also have oras for pushing and saving artifacts.

## License

HPCIC DevTools is distributed under the terms of the MIT license.
All new contributions must be made under this license.

See [LICENSE](https://github.com/converged-computing/cloud-select/blob/main/LICENSE),
[COPYRIGHT](https://github.com/converged-computing/cloud-select/blob/main/COPYRIGHT), and
[NOTICE](https://github.com/converged-computing/cloud-select/blob/main/NOTICE) for details.

SPDX-License-Identifier: (MIT)

LLNL-CODE- 842614

