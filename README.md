# Smart Drone-Based Traffic Control and Emergency Response System #

An autonomous Edge AI and robotics solution designed to monitor urban traffic dynamics, estimate vehicle velocities, and dynamically prioritize emergency response vehicles using aerial surveillance.

The system deploys optimized deep learning models onto an NVIDIA Jetson Nano mounted on an autonomous quadcopter, executing real-time computer vision tasks completely at the edge.

Key Features
1. Separate Edge AI Pipelines
To maximize inference efficiency and maintain high frame rates on resource-constrained hardware, the computer vision tasks are decoupled into two distinct pipelines that run independently:

Emergency Vehicle (EV) Detection Module: Constantly scans the aerial feed to identify high-priority emergency vehicles (such as ambulances and fire trucks) using a customized, lightweight object detection network.

1.Speed Estimation and Traffic Density Module: Tracks standard vehicles across frames to compute real-time velocity vectors and estimate localized traffic congestion indices.
<img width="230" height="230" alt="Screenshot 2025-12-31 215852" src="https://github.com/user-attachments/assets/01d36901-4f44-43a7-97ed-4920aabbc91d" />
<img width="230" height="230" alt="Screenshot 2025-12-12 221038" src="https://github.com/user-attachments/assets/19143194-b977-4141-af44-99cb63e765d7" />

2. Autonomous Aerial Surveillance
Waypoint Navigation: Autonomous flight routing utilizing pre-programmed or dynamically updated GPS coordinates via a flight controller.

3. Fail-Safe Protocols: Built-in automated routines for Low-Battery Return-to-Launch (RTL) and Signal Loss mitigation to ensure hardware safety.

4. Localized Data Logging (On-Chip Evidence)![Uploading Screenshot 2025-12-12 221952.png…]()

5. Zero-Dependency Storage: Instead of relying on constant cloud connectivity or external MQTT brokers which can fail during field operations, all critical event triggers (including traffic violations, speed limit breaches, and EV detections) are saved locally into a dedicated evidence folder on the Jetson Nano storage drive for post-flight analysis.

System Architecture
The architecture bridges high-level computer vision processing with low-level flight control loop dynamics:

[ Downward Camera ] ---> ( NVIDIA Jetson Nano )
                                |---> EV Detection Model
                                |---> Speed Estimation Model
                                |---> [ Local Evidence Folder ]
                                |
                   (Serial MAVLink over UART)
                                |
                     [ Flight Controller ] ---> [ BLDC Motors / ESCs ]
<img width="700" height="402" alt="WhatsApp Image 2026-06-05 at 9 18 47 PM" src="https://github.com/user-attachments/assets/07f7af66-2ac6-4a41-933d-37609e011392" />
<UL>
<LI>Perception Layer: A high-definition down-facing camera streams live video to the NVIDIA Jetson Nano.</LI>

<LI>Edge Processing Layer: The Jetson Nano runs standalone execution loops for vehicle classification, bounding-box tracking, and local logging.</LI>
</UL>

<table border="1" cellpadding="8" cellspacing="0" style="border-collapse: collapse; width: 100%; font-family: sans-serif;">
  <thead>
    <tr style="background-color: #f2f2f2; text-align: left;">
      <th style="padding: 12px; border: 1px solid #ddd;">Component / Tool</th>
      <th style="padding: 12px; border: 1px solid #ddd;">Core Subject Domain</th>
      <th style="padding: 12px; border: 1px solid #ddd;">Practical Application</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="padding: 12px; border: 1px solid #ddd; font-weight: bold;">NVIDIA Jetson Nano</td>
      <td style="padding: 12px; border: 1px solid #ddd;">Embedded Systems &amp; RTOS</td>
      <td style="padding: 12px; border: 1px solid #ddd;">Serves as the high-performance onboard companion computer executing concurrent pipeline threads.</td>
    </tr>
    <tr>
      <td style="padding: 12px; border: 1px solid #ddd; font-weight: bold;">YOLOv8 &amp; TensorRT</td>
      <td style="padding: 12px; border: 1px solid #ddd;">Computer Vision / Digital Image Processing</td>
      <td style="padding: 12px; border: 1px solid #ddd;">Utilized for deep-learning-based feature extraction, object detection, and model quantization for real-time edge execution.</td>
    </tr>
    <tr>
      <td style="padding: 12px; border: 1px solid #ddd; font-weight: bold;">PID Controllers</td>
      <td style="padding: 12px; border: 1px solid #ddd;">Control Systems</td>
      <td style="padding: 12px; border: 1px solid #ddd;">Implemented within the Flight Controller firmware to continuously minimize attitude error (Pitch, Roll, Yaw).</td>
    </tr>
    <tr>
      <td style="padding: 12px; border: 1px solid #ddd; font-weight: bold;">Sensor Fusion (IMU / GPS)</td>
      <td style="padding: 12px; border: 1px solid #ddd;">Digital Signal Processing (DSP)</td>
      <td style="padding: 12px; border: 1px solid #ddd;">Employs mathematical filtering (such as Kalman Filtering) to remove high-frequency physical vibrations from raw gyroscope/accelerometer data.</td>
    </tr>
    <tr>
      <td style="padding: 12px; border: 1px solid #ddd; font-weight: bold;">MAVLink Protocol</td>
      <td style="padding: 12px; border: 1px solid #ddd;">Wireless Communication</td>
      <td style="padding: 12px; border: 1px solid #ddd;">Formulates lightweight, low-overhead serialized telemetry packets for robust air-to-ground data transmission.</td>
    </tr>
  </tbody>
</table>
