# What is it?
A Dockerfile based on nvidia/cudagl that allows GPU based training for Unity ml-agents. 

__**It only works for an NVIDIA GPU and you must have the native NVIDIA driver 390 or newer installed.**__ This has only been testing on Debian Stretch stable with nvidia-driver 390.87-2~bpo9+1.

This Dockerfile is a modified verison of the original [Unity ml-agents
Dockerfile](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Using-Docker.md)
but is based on
[nvidia/cudagl](https://hub.docker.com/r/nvidia/cudagl). nvidia/cudagl
provides both CUDA and OpenGL. The Dockerfile also installs cuDNN.

You must have
[nvidia-docker2](https://github.com/nvidia/nvidia-docker/wiki/Installation-%28version-2.0%29)
installed. nvidia-docker2 provides an NVIDIA docker runtime that
enables use of the GPU from docker.

Since OpenGL is also installed you are not required to use a headless training environment.

# Building the Docker image

```
$ git clone https://github.com/Unity-Technologies/ml-agents.git
$ cd ml-agents
```

Edit the ml-agents/ml-agents/setup.py script and change 'tensorflow>=1.7,<1.8' to 'tensorflow-gpu>=1.7,<1.8'.

The following shows the diff after the edit:

```
$ git diff -w ml-agents/setup.py
diff --git a/ml-agents/setup.py b/ml-agents/setup.py
index 83852da6..63f48a94 100644
--- a/ml-agents/setup.py
+++ b/ml-agents/setup.py
@@ -28,7 +28,7 @@ setup(
     packages=find_packages(exclude=['tests', 'tests.*', '*.tests', '*.tests.*']),  # Required

     install_requires=[
-        'tensorflow>=1.7,<1.8',
+        'tensorflow-gpu>=1.7,<1.8',
         'Pillow>=4.2.1',
         'matplotlib',
         'numpy>=1.13.3,<=1.14.5',
```

Copy the Dockerfile from this repo to ml-agents/Dockerfile then build the Docker image.

```
$ docker build -t ml-agents .
```

# Usage

See Unity's [Using Docker For ML-Agents](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Using-Docker.md) for instructions on how to build your environment and for a description of the arguments to mlagents-learn.

Build your training environment and copy your training config file to the same directory then run the following command in that directory:

```
$ docker run --runtime=nvidia -ti --rm -e DISPLAY -e USER -v /tmp/.X11-unix:/tmp/.X11-unix -v /etc/passwd:/etc/passwd:ro -v /etc/shadow:/etc/shadow:ro -v /etc/group:/etc/group:ro -p 5005:5005 -v $PWD:/unity-volume -u $UID ml-agents <trainer-config-file> --env=<environment-name> --train --run-id=<run-id>
```
