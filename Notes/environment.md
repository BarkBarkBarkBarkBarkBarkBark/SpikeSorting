### Install miniConda on the jetson nano

```
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh \
     -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
~/miniconda3/conda/bin/conda init
exec $SHELL
```
#### create env called "py310" + pin Python version
```
conda create -y -n ml python=3.10   # uses the ARM channels automatically
conda activate ml
```

python 3.10
L4T_VERSION=36.4.3
JETPACK_VERSION=6.2
CUDA_VERSION=12.6

The purpose of this project is to make a CUDA enabled container complete with machine learning libraries to run spike sorting libraries. 

NVIDIA provides [pre-built containers](https://github.com/dusty-nv/jetson-containers/tree/master) with ML Library runtimes that avoid dependency hell. 

The base image for this project is `dustynv/l4t-pytorch:r36.4.0`. Linux for Tegra is a custom distribution for the jeston nano, which has an arm64/aarch64 arcitecture. That image was re-tagged as `spike_sorting`. To run that container, navigate to the spike sorting project root and run `jetson-containers run $(autotag spike_sorting)`

The project will be version controlled using my [Spike Sorting Repo](https://github.com/BarkBarkBarkBarkBarkBarkBark/SpikeSorting.git)

When the container is launched with `jetson-containers run $(autotag spike_sorting)`, the root directory the command was run from is bound to the `workspace` directory in the container. 


This project uses prebuild docker containers with pytorch , tensorflow, and other libraries installed in docker containers


## Setup

| Step                                          | Command Note    |
| --------------------------------------------- | -----------------------------| 
| **Update JetPack packages**                   | `sudo apt update && sudo apt dist-upgrade -y`|
| **Install Docker CE** (if not pre-installed)  | `sudo apt install -y docker.io`|
| **Add user to `docker` group**                | `sudo usermod -aG docker $USER && newgrp docker` (re-login afterwards)  |
| **Confirm NVIDIA runtime present**            | `docker info \| grep -A3 "Runtimes"` |
| **Clone helper repo**                         | `git clone https://github.com/dusty-nv/jetson-containers && cd jetson-containers` |
| **Install helper scripts**                    | `bash install.sh` → adds `jetson-containers` and `autotag` to `$PATH`   |
| **Run a ready-made container**                | `jetson-containers run $(autotag l4t-pytorch)`  | 
| **Run the bigger ML stack**                   | `jetson-containers run $(autotag l4t-ml)`  | 
| **Build a custom image**                      | `jetson-containers build --name=spike_sorting l4t-ml spikeinterface` |
| **Build on top of an existing base**          | `jetson-containers build --base=dustynv/l4t-ml:r36.4.1 --name=spike_sorting l4t-ml spikeinterface`|
| **Override CUDA / PyTorch versions on build** | `CUDA_VERSION=12.9 PYTORCH_VERSION=2.6 \ jetson-containers build --name=ml_cuda129 pytorch torchvision`|
| **Run the Docker Image**                      | `jetson-containers run $(autotag spike_sorting)`|
| **Install the correct PyTorch Image**         | `dustynv/l4t-pytorch:r36.4.0`|


## Sanity Checks

|Sanity Check|Bash|expected
|-|-|-|
|**Check Docker Images**|`docker images \| grep my_ml`||
|**Check all Docker Images**|`docker images`||
|**Nvidia Runtime Registered with Docker**|`docker info \| grep -A3 "Runtimes"`|List shows nvidia under Runtimes: – and ideally default-runtime: nvidia in the JSON above it|
|**Jetpack / L4T Version**|`cat /etc/nv_tegra_release`|head -n1|
|**Basic Docker Permissions**|`docker run --rm hello-world`|Container prints the “Hello from Docker!” message without sudo – confirms you’re in the docker group|
|**GPU Exposed in Container**|`docker run --rm --runtime nvidia \ dustynv/l4t-pytorch:r36.4.0 \ python3 -c "import torch, os; print('Torch', torch.__version__, '\| CUDA', torch.cuda.is_available(), '\| Dev', torch.cuda.get_device_name(0) if torch.cuda is_available() else 'n/a')"`|Prints True and NVIDIA Tegra X1 (or your SoC name) – shows CUDA libs match the driver|
|**CUDA Libraries Loaded**|`idconfig -p`|`grep libcuda.so`|
|**GPU Stats tool works**|`tegrastats --interval 2000 --logfile /dev/null (ctrl-C after a couple lines)`|GPU rail power and freq numbers stream – lightweight confirmation the GPU is alive (Jetson devices don’t have nvidia-smi)
