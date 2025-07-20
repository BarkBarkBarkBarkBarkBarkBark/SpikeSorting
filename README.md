# SpikeSorting

The purpose of this project is to run spike sorting libraries on a Jetson Nano Super, which is made by Nvidia and features CUDA enabled GPU acceleration. 

Installation instructions can be found in `environment.md` in the notes folder. 

Because of the arcitecture of the Jetson, and to avoid dependency hell, the sorter is run inside of a container. The containers are made by NVIDIA, specifically for the Jetson Nano. 

The base container contains PyTorch, 

If you want to run scripts from inside the container, make sure that a Dev Container plugin is installed in the IDE, I used Dev Containers by Anysphere. 

Run the container, explicitly binding the working directory to the container. 

# Run from the project root on the Jetson
```
vncserver :1        # or reconnect to existing session
# 2. start the container
export DISPLAY=:1
jetson-containers run -it \
  --name kilosort_gui \
  -v "$PWD":/workspace -w /workspace \
  -e DISPLAY=$DISPLAY \
  -v /home/jetson/.Xauthority:/root/.XAuthority:ro \
  -e XAUTHORITY=/root/.XAuthority \
  spike_sorting:gui-final
```

Run Spike sorter
```
python3 -m kilosort
```
## Confirm CUDA Available
```
python3 -c "import torch; print(torch.__version__, torch.cuda.is_available())"
```


Once thats Running, use the Dev Container Plugin to open the container in a new IDE window. Changes inside the dev container will be immediatly reflected on the host. 

Now that you're inside the container, go boo boo bananas. 


If you are running this via SSH Like I am, then a virtual display will need to be ported. Xquartz will work, install it form the web. From the security tab, enable forwarding over the network. 

in that terminal, `xhost + hostname` should add it. Then, shell into the remote machine with the -X flag, `ssh -X jetson@100.78.174.79`. This needs to be done from a terminal, rather than through the IDE. The reason, for me at least, is that Cursor doesnt forward the -X flag, so the display also 

