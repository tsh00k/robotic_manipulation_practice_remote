# pi0.5 Remote Code

This repository contains the code and configuration used on the remote GPU
server for LeRobot pi0.5 fine-tuning and inference.

The local ROS/MuJoCo project and generated datasets are separate. Do not commit
model weights, datasets, credentials, virtual environments, or run outputs.

The remote runtime uses Python 3.12 and LeRobot. The local ROS runtime uses a
separate Python 3.10 environment; communication crosses an explicit RPC or IPC
boundary.

