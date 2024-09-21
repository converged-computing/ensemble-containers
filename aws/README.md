# Containers

Note that these are all CPU containers. I would not have the funds, and I doubt the cloud have the capacity, to use GPU.
I am also taking greater care in designing the layers.

### AWS 

> This typically means libfabric


| Container                                               |  Dockerfile                          |
|---------------------------------------------------------|--------------------------------------|
| ghcr.io/converged-computing/ensemble-amg2023:libfabric  | [Dockerfile](amg2023/Dockerfile)     |
| ghcr.io/converged-computing/ensemble-lammps:libfabric   | [Dockerfile](lammps/Dockerfile)      |

