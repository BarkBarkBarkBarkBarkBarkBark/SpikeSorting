# SpikeSorting

The purpose of this project is to run spike sorting libraries on a Jetson Nano Super, which is made by Nvidia and features CUDA enabled GPU acceleration. 

Installation instructions can be found in `environment.md` in the notes folder. 

Because of the arcitecture of the Jetson, and to avoid dependency hell, the sorter is run inside of a container. The containers are made by NVIDIA, specifically for the Jetson Nano. 

The base container contains PyTorch, 

If you want to run scripts from inside the container, make sure that a Dev Container plugin is installed in the IDE, I used Dev Containers by Anysphere. 

Run the container, explicitly binding the working directory to the container. 

```
# Run from the project root on the Jetson
cd ~/codespace/SpikeSorting           # <-- your git repo
jetson-containers run -d --name spike_dev \
  -v "$PWD":/workspace \
  -w /workspace \
  $(autotag spike_sorting)
```

Once thats Running, use the Dev Container Plugin to open the container in a new IDE window. Changes inside the dev container will be immediatly reflected on the host. 