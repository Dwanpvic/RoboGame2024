# ROBOGAME2024_SLEEPNO.1_upper_computer

## Overview
- In the context of the Robogame 2024 competition, the upper computer is responsible for computing the robot's control policy, receiving sensor data from the robot's environment and telling STM microcontroller what to do, with a final goal of autonomous navigation, exploration of the environment and catching and launching targets as much as possible. 
- This repository contains the implementation of a robot control policy on a Raspberry Pi or Windows machine, utilizing a USB-TTL converter to communicate with the STM controller. The robot is equipped with camera module and 8 visible cameras to navigate and interact with its environment. The policy module includes functionalities such as A* pathfinding, sensor data processing, intime self-localization, obstacle avoidance ,and real-time communication with an STM controller.

## Features
- **Astar Pathfinding**: Implements algorithms to find optimal paths for the agent within a defined environment.
- **Camera Integration**: Uses multiple cameras (front, back, left, right) for environmental perception ,self-localization and target detection.
- **Serial Communication**: Communicates with external devices (e.g., an STM microcontroller) for control commands and data exchange.
- **Multithreading**: Manages multiple tasks simultaneously using Python’s threading module to ensure real-time operations ,and thread_locking to prevent data corruption.

## Structure
- **policy.py**: Main script that initializes the environment and starts various threads for handling different components of the robot’s operations.(check the Mindmap.png file for more details)
- **SGBM_raspi.py**: Contains functions for handling stereo image processing to detect targets.(at first, I built a stereo camera system on rasepberry pi, but later I changed to a Windows machine, because of the poor power supply capacity of the USB interfaces on the raspberry pi)
- **get_theta.py**: Functions for fine tuning the machine position for better sucking the target and obtaining angular information after sucking the target of the ground.
- **Apriltag_detection.py**: Functions for detecting AprilTags in the environment and obtaining their pose information in order to self-localize the robot.

## Requirements

- Python 3.8+
- OpenCV (`cv2`)
- NumPy
- AprilTag Python library
- 8 cameras and a USB-TTL converter connected to your computer
- Pyserial

## Usage
**Start the Script**: Run the script using Python.

    ```
    python policy.py
    ```

## Problems during the competition
- 1. The visual error of SGBM: 
    - The SGBM algorithm used for stereo image processing is sensitive to lighting conditions and makes it hard to distinguish between the target and the environment, especially after Kmeans clustering is applied to the disparity map. This can be partly solved by changing the light exposure of camera and the blocksize and numDisparities of SGBM algorithm.
- 2. The direction of the camera: 
    - The direction of the camera should not be parallel to the light source, otherwise the camera may be too sensitive to the light and the smoothness of the targets.
- 3. Making sure the camera is able to take photo:
    - Due to the limited power supply capacity of the USB interface or other hardware issues, the camera may not be able to take photos even if ret is True, which means the frame can not be obtained.(ret, frame = cap.read())So I get one photo from each camera to make sure the camera is working.
- 4. Mutiprocess slowing down the program:
    - The multiprocessing module is used to handle multiple tasks simultaneously, but it slow down each process due to the GIL(Global Interpreter Lock) of Python and limited hardware resources,even after using the threading module.This problem did not affect the performance of the program very much, but the fine_tuning part, which needs the realtime visual feedback from the camera, is affected and may not make the car movements converge to the target. So I limited the times of fine_tuning. 

## Other
- Thanks to my brilliant teammates responsible for electronic control [1xx55](https://github.com/1xx55) and [smallxxpants](https://github.com/smallxxpants) for providing comprehensive functional implementation and various signal feedbacks for me to simplify the control policy.For the STM part and the communication protocol, check out [RG2024](https://github.com/1xx55/RG2024) for more details.
- Because the feedback is strongly relied on cameras and one of them is even parallel to the light source, it is important to make sure the camera is able to take photos and check the lighting  conditions of the environment, which can be very changable during the competition. I should have added an auto exposure function to the camera to make sure the lighting conditions are good instead of relying on the auto balance function of the camera itself.Besides, I should have checked the lighting conditions of the environment right before the competition even if it may postpone the start time. Great apologies for my misknowledge and lack of attention to details!