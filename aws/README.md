# Containers

Note that these are all CPU containers. I would not have the funds, and I doubt the cloud have the capacity, to use GPU.
I am also taking greater care in designing the layers.

### AWS 

> This typically means libfabric


| Container                                               |  Dockerfile                          |
|---------------------------------------------------------|--------------------------------------|
| ghcr.io/converged-computing/ensemble-amg2023:libfabric  | [Dockerfile](amg2023/Dockerfile)     |
| ghcr.io/converged-computing/ensemble-lammps:libfabric   | [Dockerfile](lammps/Dockerfile)      |
| ghcr.io/converged-computing/ensemble-stream:libfabric   | [Dockerfile](stream/Dockerfile)      |
| ghcr.io/converged-computing/ensemble-kripke:libfabric   | [Dockerfile](kripke/Dockerfile)      |
| ghcr.io/converged-computing/ensemble-laghos:libfabric   | [Dockerfile](laghos/Dockerfile)      |
| ghcr.io/converged-computing/ensemble-minife:libfabric   | [Dockerfile](minife/Dockerfile)      |
| ghcr.io/converged-computing/ensemble-mixbench:libfabric | [Dockerfile](mixbench/Dockerfile)      |
| ghcr.io/converged-computing/ensemble-mt-gemm:libfabric  | [Dockerfile](mt-gemm/Dockerfile)      |
| ghcr.io/converged-computing/ensemble-osu:libfabric      | [Dockerfile](osu/Dockerfile)      |
| ghcr.io/converged-computing/ensemble-quicksilver:libfabric   | [Dockerfile](quicksilver/Dockerfile)      |
| ghcr.io/converged-computing/ensemble-stream:libfabric   | [Dockerfile](stream/Dockerfile)      |
