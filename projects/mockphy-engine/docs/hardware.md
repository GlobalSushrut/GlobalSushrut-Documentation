# mockphy-engine - hardware

*This documentation is from the private repository mockphy-engine.*

---

# Hardware Integration Guide

This document explains how to integrate the physics engine with hardware systems for Hardware-in-the-Loop (HIL) testing, sensor simulation, and real-time applications.

## Table of Contents
1. [Introduction](#introduction)
2. [Hardware-in-the-Loop (HIL) Testing](#hardware-in-the-loop-hil-testing)
3. [Sensor Simulation](#sensor-simulation)
4. [Real-time Performance](#real-time-performance)
5. [Hardware Requirements](#hardware-requirements)
6. [Physical Test Systems](#physical-test-systems)
7. [Case Studies](#case-studies)

## Introduction

The physics engine supports hardware integration for applications that require physical hardware interaction, including automotive testing, robotics, and sensor calibration.

## Hardware-in-the-Loop (HIL) Testing

HIL testing allows real hardware to interact with simulated physics environments, creating a bridge between physical components and virtual physics.

### Setting Up a HIL Environment

```cpp
// Create HIL simulation environment
auto hil = std::make_shared<physics::HardwareInLoop>();

// Configure HIL parameters
physics::HILConfig config;
config.updateRate = 1000;  // 1000 Hz update rate
config.latencyMs = 5;      // 5ms expected latency
config.jitterMs = 2;       // 2ms expected jitter
config.timeoutMs = 100;    // 100ms timeout for hardware responses

hil->setConfig(config);
```

### Connecting to Physical Hardware

```cpp
// Set up hardware interfaces
hil->setHardwareInterface("CAN", "/dev/can0");
hil->setHardwareInterface("Serial", "/dev/ttyS0");
hil->setHardwareInterface("UDP", "192.168.1.100:8080");

// Register hardware callbacks
hil->registerInputCallback([](const physics::HardwareInput& input) {
    // Process hardware input
    return true;
});

hil->registerOutputCallback([](physics::HardwareOutput& output) {
    // Process hardware output
    return true;
});
```

### Testing Scenarios

```cpp
// Create a test scenario
auto scenario = std::make_shared<physics::HILTestScenario>("LaneChange", hil);

// Initialize with configuration
std::string config = R"({
    "duration": 30.0,
    "lanes": 3,
    "traffic": true,
    "weather": "rain"
})";
scenario->initialize(config);

// Start scenario
scenario->start();

// Run the simulation
while (!scenario->isComplete()) {
    // Step the scenario
    scenario->update(0.001);  // 1ms timestep
    
    // Get status
    std::string status = scenario->getStatus();
    // Process status...
}
```

### Fault Injection

```cpp
// Set up fault injection
physics::FaultParameters faultParams;
faultParams.type = physics::FaultType::SensorDrift;
faultParams.target = "LIDAR";
faultParams.magnitude = 0.05;  // 5% drift
faultParams.startTime = 10.0;  // 10 seconds into simulation
faultParams.duration = 5.0;    // 5 seconds duration

hil->injectFault(faultParams);
```

## Sensor Simulation

The physics engine can simulate various sensors and their interaction with the physical world.

### Supported Sensor Types

- **Camera**: RGB, Depth, Infrared, Thermal
- **LIDAR**: Single beam, Multi-beam, Spinning
- **RADAR**: Short range, Long range
- **IMU**: Accelerometer, Gyroscope
- **GPS**: Standard, RTK
- **Ultrasonic**: Distance sensors

### Camera Sensor Example

```cpp
// Create a camera sensor
auto camera = std::make_shared<physics::VirtualCamera>();

// Configure camera parameters
physics::CameraParams params;
params.resolution = {1920, 1080};
params.fov = 60.0;
params.frameRate = 30;
params.distortion = 0.05;
params.noise = 0.02;

camera->setParameters(params);

// Attach camera to a rigid body
auto body = engine->createRigidBody(1.0, physics::Vector3d(0, 0, 0));
camera->attachTo(body, physics::Vector3d(0, 0, 1), physics::Vector3d(0, 1, 0));

// Get camera output
physics::ImageData image = camera->captureImage();
```

### LIDAR Sensor Example

```cpp
// Create a LIDAR sensor
auto lidar = std::make_shared<physics::VirtualLidar>();

// Configure LIDAR parameters
physics::LidarParams params;
params.beams = 64;
params.rotationRate = 10;  // 10 Hz
params.rangeMin = 0.1;     // 10 cm
params.rangeMax = 100.0;   // 100 m
params.fov = {360.0, 30.0}; // 360° horizontal, 30° vertical

lidar->setParameters(params);

// Attach LIDAR to a rigid body
auto body = engine->createRigidBody(1.0, physics::Vector3d(0, 0, 0));
lidar->attachTo(body, physics::Vector3d(0, 0, 1.8), physics::Quaterniond::Identity());

// Get LIDAR scan
physics::PointCloud pointCloud = lidar->scan();
```

### Sensor Fusion

```cpp
// Create sensor fusion module
auto fusion = std::make_shared<physics::SensorFusion>();

// Add sensors
fusion->addSensor(camera);
fusion->addSensor(lidar);
fusion->addSensor(imu);

// Process sensor data
fusion->update(engine->getTime());

// Get fused data
physics::FusedSensorData data = fusion->getData();
```

## Real-time Performance

### Real-time Configuration

```cpp
// Configure engine for real-time operation
physics::RealTimeConfig rtConfig;
rtConfig.targetFrameRate = 1000;  // 1000 Hz
rtConfig.maxStepSize = 0.001;     // 1 ms maximum step
rtConfig.minStepSize = 0.0001;    // 0.1 ms minimum step
rtConfig.timeScaleFactor = 1.0;   // Real-time (no scaling)

engine->setRealTimeConfig(rtConfig);
```

### Real-time Synchronization

```cpp
// Create real-time synchronizer
auto sync = std::make_shared<physics::RealTimeSynchronizer>();

// Set up synchronization parameters
sync->setTargetFrequency(1000);  // 1000 Hz
sync->setMaxLatency(0.002);      // 2 ms maximum latency

// Run synchronized simulation
while (running) {
    auto startTime = std::chrono::high_resolution_clock::now();
    
    // Step physics
    engine->step(sync->getTimeStep());
    
    // Update hardware
    hil->update();
    
    // Wait for next sync point
    sync->synchronize();
}
```

## Hardware Requirements

### Minimum Hardware

For real-time HIL applications:
- **CPU**: 4+ cores, 3.0+ GHz
- **Memory**: 8+ GB RAM
- **Storage**: SSD for low-latency I/O
- **Network**: Gigabit Ethernet for hardware communication
- **Interfaces**: USB 3.0, CAN, Serial ports as needed

### Recommended Hardware

For complex scenes with many sensors:
- **CPU**: 8+ cores, 3.5+ GHz
- **Memory**: 16+ GB RAM
- **GPU**: NVIDIA RTX series (for sensor simulation)
- **Storage**: NVMe SSD
- **Network**: 10 Gigabit Ethernet
- **Interfaces**: USB 3.1, CAN-FD, FPGA-based I/O

### Real-time Operating Systems

For deterministic performance:
- **Linux**: RT_PREEMPT patch or Xenomai
- **Windows**: Windows 10 IoT Enterprise LTSC
- **Custom**: QNX, VxWorks

## Physical Test Systems

### Vehicle Testbed Integration

```cpp
// Create a vehicle testbed connection
auto testbed = std::make_shared<physics::VehicleTestbed>();

// Configure testbed
physics::TestbedConfig config;
config.interface = "CAN";
config.deviceId = "can0";
config.protocol = "J1939";

testbed->setConfig(config);

// Connect to physical systems
testbed->connect();

// Map physical inputs to simulation
testbed->mapInput("SteeringAngle", "vehicle.steering.angle");
testbed->mapInput("ThrottlePosition", "vehicle.throttle.position");
testbed->mapInput("BrakePosition", "vehicle.brake.position");

// Map simulation outputs to physical systems
testbed->mapOutput("vehicle.wheel.speed", "WheelSpeed");
testbed->mapOutput("vehicle.acceleration", "Acceleration");
```

### Robot Integration

```cpp
// Create a robot interface
auto robot = std::make_shared<physics::RobotInterface>();

// Configure robot
physics::RobotConfig config;
config.interface = "ROS";
config.rosUri = "http://localhost:11311";
config.robotModel = "UR5";

robot->setConfig(config);

// Connect to robot
robot->connect();

// Map joint states
robot->mapJoint("shoulder", "robot.joint1");
robot->mapJoint("elbow", "robot.joint2");
robot->mapJoint("wrist", "robot.joint3");

// Enable force feedback
robot->enableForceFeedback(true);
```

## Case Studies

### Automotive HIL Testing

A major automotive manufacturer used our physics engine for testing ADAS systems:

- **Hardware**: Actual ECU, sensor inputs, actuator outputs
- **Software**: Virtual environment with simulated traffic
- **Integration**: CAN bus for real-time communication
- **Results**: 97% correlation with physical road tests

### Robotics Development

A robotics company developed a new gripper using our physics engine:

- **Hardware**: Physical gripper with force sensors
- **Software**: Simulated objects with varying properties
- **Integration**: ROS for communication
- **Results**: Reduced development time by 60%

### Sensor Calibration

A sensor manufacturer used our physics engine for LIDAR calibration:

- **Hardware**: Physical LIDAR units
- **Software**: Virtual environment with precise features
- **Integration**: Custom UDP protocol for data exchange
- **Results**: Calibration accuracy improved by 35%
