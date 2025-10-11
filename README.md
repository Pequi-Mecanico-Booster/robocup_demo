# Robocup Demo
## Introduction
The Booster T1 Robocup official demo allows the robot to make autonomous decisions to kick the ball and complete the full Robocup match. It includes three programs: vision, brain, and game_controller.

- vision
    - The Robocup vision recognition program, based on Yolo-v8, detects objects such as robots, soccer balls, and the field, and calculates their positions in the robot's coordinate system using geometric relationships.
- brain
    - The Robocup decision-making program reads visual data and GameController game control data, integrates all available information, makes judgments, and controls the robot to perform corresponding actions, completing the match process.
- game_controller
    - Reads the game control data packets broadcast by the referee machine on the local area network, converts them into ROS2 topic messages, and makes them available for the brain to use.

## Install extra dependency
sudo apt-get install ros-humble-backward-ros

## Build and Run

### Note    
This repo support both jetpack 6.0 and 6.2. If the repo is deployed on jetpack 6.2 machine, please modify src/vision/config/vision.yaml to selelct correct trt model

vision.yaml for jetpack 6.0 machine
``` yaml
detection_model:
  model_path: "./src/vision/model/best_orin.engine"
  confidence_threshold: 0.2
```
vision.yaml for jetpack 6.2 machine
```yaml
detection_model:
  model_path: ""./src/vision/model/best_orin_10.3.engine"
  confidence_threshold: 0.2
```

To decide jetpack version, please execute `dpkg -l | grep jetpack` on host.

# Build the programs
./scripts/build.sh

# Run in the simultion environment
./scripts/sim_start.sh

# Run on the actual robot
./scripts/start.sh


## Documents
[Chinese Version](https://booster.feishu.cn/wiki/P5kJw6nDGib5wskZ3Yfc289lnIg)

[English Version](https://booster.feishu.cn/wiki/XY6Kwrq1bizif4kq7X9c14twnle)



# RoboCup Demo - Docker Setup

This repository provides a **Docker-based setup** for the **robocup_demo** project, adapted to run on **NVIDIA Jetson** devices with **CUDA** support.  
Custom `Dockerfile` and `docker-compose.yml` files were created to easily start the system components, including **robocup_demo** and **zed**.

---

## Project Structure

- **dockerfile.jetson**: main image based on **JetPack 6.2**, containing all necessary libraries to run robocup_demo.
- **docker-compose.yml**: orchestrates the simultaneous execution of both `robocup_demo` and `zed` containers.
- **robocup_demo container**: contains the project source code and its dependencies.
- **zed container**: runs the ZED camera system, based on version **5.0**, compatible with JetPack 6.2.

---

## Requirements

- [Installed **Docker** and **Docker Compose**](https://www.cytron.io/tutorial/docker-setup-for-jetson-orin-nano-super-jp6.2?srsltid=AfmBOoobhJK4ev8m_QYQQtgzMrFMoV9xdgf6AkJuBkOsLImfvIJPU8Zg)
---

## Important Notes

1. **Image Build**
   - A **direct Docker build** fails because CUDA/NVIDIA libraries are missing at build time.  
   - The correct procedure is:
     1. Build the base image using `dockerfile.jetson`  
     2. Enter the container  
     3. Build the repository manually inside the container  
     4. Perform a **docker commit** to create the final image


2. **ZED Integration**
   - When starting the `robocup_demo` service through `docker-compose`, the `zed` service is also launched automatically, since the demo depends on ZED camera data.

---

## Build and Run Steps

### Build Jetson Image

```bash
docker buildx build -f dockerfile.jetson -t robocup_demo_v1:1.0 .
docker buildx build -f dockerfile.zed -t zed .
```

### Enter the Container
```bash
docker compose run robocup_demo bash
```

### Build Repository Inside the Container
```bash
source /opt/ros/humble/setup.bash
./scripts/build.sh

```

### Commit the Compiled Container (in another terminal)
```bash
docker ps  # Get the CONTAINER ID
docker commit <CONTAINER ID> robocup_demo_v1:1.0
```

### Run the System via Docker Compose
```bash
docker compose down
docker compose up robocup_demo
```
---
