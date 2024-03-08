_This is an Apple Silicon adaptation of @quattrinili's guide [here](https://github.com/quattrinili/vnc-ros)_.

# ROS on Apple Silicon

Before following the next two steps, install Docker ([installation instructions for Mac](https://docs.docker.com/docker-for-mac/install/).

## 1. Setup
1. Open a terminal and clone this repository with the command `git clone https://github.com/quattrinili/ros-apple-silicon`
2. Enter in the cloned repository folder, `cd ros-apple-silicon`
3. Create a folder, which is the one shared between the host and the docker container, `mkdir workspace`
4. Run `docker-compose up --build`

(`ros.env` contains environment variables for ROS that can be modified before running the command in step 3.)

## 2. Running a ROS gazebo simulation for testing
Once the other terminal shows the following type of messages and remains running without errors

     ⠿ Container vnc-ros-ros-1    Cr...                           0.0s
     ⠿ Container vnc-ros-novnc-1  Recreated                       0.1s
    Attaching to vnc-ros-novnc-1, vnc-ros-ros-1
    ... (comment: more messages, with the latest being in the current version of Docker)

    vnc-ros-novnc-1  | 2023-03-29 19:45:10,919 INFO success: xterm entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)

(Note that there might be some slight variations of outputs, as long as the terminal is "occupied" by that command, you can go to the next step)


open another terminal:
1. Run `docker-compose exec ros bash` (`docker-compose up` has to be running)
2. Run `source /opt/ros/melodic/setup.bash`
3. Run `roslaunch turtlebot3_gazebo turtlebot3_world.launch` and you should see a number of messages, including `[ INFO] [1617035063.438483400, 0.126000000]: DiffDrive(ns = //): Advertise odom on odom `

To see whether it was successful, 
5. Open your browser to `localhost:8080/vnc.html` and click connect.
6. The robotic simulator is now running in your browser.

## 3. To terminate

In the terminal open for step 2., press ctrl+c, which will stop the execution of the simulator. Once that is stopped -- you should see it as the terminal can accept commands -- press ctrl+d to exit the Docker container.

Afterwards, in the terminal open for step 1., press ctrl+c. Once terminated, you should see the following messages

    Stopping ros-apple-silicon_ros_1   ... done
    Stopping ros-apple-silicon_novnc_1 ... done

At this point, both terminals can be closed if you wish.

## Editing your workspace
To link a local workspace folder to your container, add the following configuration to docker-compose.yml under `ros`:

```yml
volumes: 
  - <LOCAL_PATH>:/root/catkin_ws/
```

This will link the provided path to `~/catkin_ws` on the Docker container. However, if you want to run `catkin_make`, do so by creating a bash via `docker-compose exec ros bash` and running `catkin_make` in `/catkin_ws`.

## Installing other packages
Edit the `Dockerfile` line that installs packages and rebuild the container using `docker-compose build`.
