# Running a PyTorch training job on Kubernetes

During my UCSC AIEA internship, I configured a GPU workload on the Nautilus cluster to train a CIFAR-10 classifier. The training script follows the [PyTorch image-classifier tutorial](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html). My work focused on running it reliably on the cluster.

## How the files fit together

1. [pvc.yaml](pvc.yaml) requests 5 GiB of persistent storage. The storage class is specific to the cluster.
2. [deployment.yaml](deployment.yaml) starts a container using an existing PyTorch CUDA image and mounts the volume at `/pvcvolume`. This lets the training script and dataset survive a disconnected client.
3. Copy [cifar10train.py](cifar10train.py) into the mounted volume. Stop the staging deployment before starting the job to free its GPU and volume attachment.
4. [job.yaml](job.yaml) mounts the same volume, requests a GPU, installs matplotlib, and runs the training script. Adding the missing dependency fixed a failed run.

Inspect the YAML before using it on your own cluster. Running it requires a namespace with GPU quota, a compatible storage class, and network access for the image, dependency, and CIFAR-10 download. The manifests use the active kubectl namespace.
