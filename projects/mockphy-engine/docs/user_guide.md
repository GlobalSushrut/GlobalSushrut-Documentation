# mockphy-engine - user_guide

*This documentation is from the private repository mockphy-engine.*

---

# User Guide

## Table of Contents
1. [Getting Started](#getting-started)
2. [Basic Usage](#basic-usage)
3. [Core Components](#core-components)
4. [Perceptual Physics Features](#perceptual-physics-features)
5. [Advanced Usage](#advanced-usage)
6. [Performance Optimization](#performance-optimization)
7. [Troubleshooting](#troubleshooting)

## Getting Started

### Installation

#### Prerequisites
- C++17 compatible compiler (GCC 7+, Clang 5+, MSVC 2019+)
- CMake 3.14 or newer
- Eigen 3.3 or newer

#### Building from Source
```bash
# Clone the repository
git clone https://github.com/yourusername/physics-engine.git
cd physics-engine

# Create build directory
mkdir build && cd build

# Configure and build
cmake ..
make -j4

# Run tests
ctest
```

### Integrating with Your Project

#### CMake Integration
```cmake
# In your CMakeLists.txt
find_package(PhysicsEngine REQUIRED)
target_link_libraries(your_target PRIVATE physics_engine)
```

#### Manual Integration
1. Add the include directory to your compiler's include path
2. Link against the physics_engine library

## Basic Usage

### Creating a Physics Engine Instance

```cpp
#include "physics/Engine.hpp"

// Create a physics engine with default settings
auto engine = physics::createPhysicsEngine();

// Or with custom dimensions and deterministic mode
auto customEngine = physics::createPhysicsEngine(3, true);
```

### Creating and Managing Rigid Bodies

```cpp
// Create a rigid body with mass and position
double mass = 1.0;
physics::Vector3d position(0.0, 10.0, 0.0);
auto body = engine->createRigidBody(mass, position);

// Set velocity
body->setLinearVelocity(physics::Vector3d(1.0, 0.0, 0.0));

// Set orientation (using quaternion)
physics::Vector4d orientation(1.0, 0.0, 0.0, 0.0); // w, x, y, z
body->setOrientation(orientation);

// Make static (immovable)
body->setStatic(true);
```

### Running the Simulation

```cpp
// Time step in seconds
const double timeStep = 1.0/60.0;

// Run the simulation for 1 minute
for (int i = 0; i < 60 * 60; ++i) {
    engine->step(timeStep);
    
    // Get updated position
    physics::Vector3d position = body->getPosition();
    // Do something with the position...
}
```

## Core Components

### Field System

The field system manages force fields, which can influence rigid bodies within them.

```cpp
// Create a field grid 
auto fieldGrid = std::make_shared<physics::FieldGrid>(10, 10, 10, 1.0);

// Set field values
for (int x = 0; x < 10; x++) {
    for (int y = 0; y < 10; y++) {
        for (int z = 0; z < 10; z++) {
            physics::Vector3d fieldValue(x * 0.1, y * 0.1, z * 0.1);
            fieldGrid->setValue(x, y, z, fieldValue);
        }
    }
}

// Get a value from the field
physics::Vector3d value = fieldGrid->getValue(5, 5, 5);
```

### Constraints

Constraints restrict movement between rigid bodies.

```cpp
// Create two bodies
auto bodyA = engine->createRigidBody(1.0, physics::Vector3d(0, 0, 0));
auto bodyB = engine->createRigidBody(1.0, physics::Vector3d(0, 5, 0));

// Create a fixed constraint between them
auto constraint = engine->createConstraint(bodyA, bodyB);
constraint->setType(physics::ConstraintType::Fixed);
```

## Perceptual Physics Features

Our physics engine includes novel perceptual physics features based on Mock Integer Theory, allowing for human-like perception simulation.

### Creating Mock Integer Components

```cpp
// Create a mock integer tensor
auto mockTensor = physics::MockIntegerFactory::createDefaultTensor({64, 64, 32});

// Create an entro-mock processor
auto entroProcessor = physics::MockIntegerFactory::createEntroMockProcessor({64, 64, 32});

// Create a visual cortex
auto visualCortex = physics::MockIntegerFactory::createVisualCortex(
    physics::MockVisualCortex::ProcessingMode::HumanLike);
```

### Processing Fields with Perceptual Physics

```cpp
// Process a standard physics field to get a perceptually modified field
auto processedField = entroProcessor->processField(*fieldGrid, timeStep);
```

### Processing Rigid Bodies with Perceptual Physics

```cpp
// Apply perceptual processing to a list of rigid bodies
std::vector<std::shared_ptr<physics::RigidBody>> bodies;
// ... Add bodies to the vector ...
entroProcessor->processRigidBodies(bodies, timeStep);
```

### Computing Perceptual Metrics

```cpp
// Get the standard position
physics::Vector3d standardPos = body->getPosition();

// Compute the perceived position 
int bodyIndex = 0; // Use the body's index or ID
physics::Vector3d perceivedPos = entroProcessor->computeRetroPosition(bodyIndex, time);

// Calculate perceptual salience
physics::Vector3d velocity = body->getLinearVelocity();
double salience = entroProcessor->computeSalience(
    standardPos, velocity, standardPos, time);

// Calculate perception error metrics
physics::Vector3d errorMetrics = entroProcessor->computePerceptionError(
    standardPos, perceivedPos, velocity);

// Extract individual error components
double magnitudeError = errorMetrics.x();
double directionError = errorMetrics.y(); // in radians
double lagFactor = errorMetrics.z();

// Print the metrics
std::cout << "Magnitude Error: " << magnitudeError 
          << (magnitudeError > 0 ? " (perceived > actual)" : " (actual > perceived)") << std::endl;
std::cout << "Direction Error: " << (directionError * 180.0 / M_PI) << "°" << std::endl;
std::cout << "Lag Factor: " << lagFactor 
          << (lagFactor > 0 ? " (perception lags behind)" : " (perception leads ahead)") << std::endl;
```

## Advanced Usage

### Vehicle Physics

```cpp
// Create a vehicle with advanced dynamics
auto vehicle = std::make_shared<physics::AdvancedVehicle>();

// Set drivetrain type
auto drivetrain = std::make_shared<physics::AdvancedDrivetrain>(
    physics::AdvancedDrivetrain::Type::AllWheelDrive,
    physics::AdvancedDrivetrain::TransmissionType::Automatic);
vehicle->setDrivetrain(drivetrain);

// Add suspension to the vehicle
auto suspension = std::make_shared<physics::SymbolicSuspension>(
    physics::SymbolicSuspension::Type::Independent,
    vehicle->getChassis(),
    vehicle->getWheels()[0]);
vehicle->addSuspension(suspension);

// Apply vehicle controls
vehicle->setThrottle(0.5);
vehicle->setSteering(-0.2);
vehicle->setBrake(0.0);
```

### Hardware-in-the-Loop Simulation

```cpp
// Create a hardware-in-loop simulation
auto hil = std::make_shared<physics::HardwareInLoop>();

// Set up a test scenario
auto scenario = std::make_shared<physics::HILTestScenario>("LaneChange", hil);
scenario->initialize("{\"duration\": 10.0, \"lanes\": 3}");

// Run the scenario
while (!scenario->isComplete()) {
    scenario->update(timeStep);
    
    // Get status
    std::string status = scenario->getStatus();
    // Parse and use status information...
}
```

## Performance Optimization

### Tuning Physics Parameters

```cpp
// Configure solver iterations for accuracy vs. speed
engine->setSolverIterations(10); // Default is 10, higher is more accurate

// Set constraint solver relaxation 
engine->setSolverRelaxation(0.2); // Lower values are more stable but slower

// Enable/disable continuous collision detection
engine->setContinuousCollisionDetection(true); // More accurate for fast objects
```

### Spatial Optimization

```cpp
// Set broad phase type
engine->setBroadPhaseType(physics::BroadPhaseType::DynamicAABBTree);

// Configure cell size for spatial partitioning
engine->setCellSize(2.0); // Larger cells = fewer cells but more objects per cell
```

### Multithreading

```cpp
// Set number of threads for physics computation
engine->setThreadCount(4); // Set to number of available cores

// Enable/disable parallel constraint solving
engine->setParallelConstraintSolving(true);
```

## Troubleshooting

### Common Issues and Solutions

#### Unstable Simulation
- **Symptom**: Objects jitter or explode apart
- **Solution**: Reduce time step, increase solver iterations, or adjust constraint relaxation

#### Poor Performance
- **Symptom**: Frame rate drops when many objects are present
- **Solution**: Enable multithreading, optimize broad phase settings, increase cell size

#### Objects Passing Through Each Other
- **Symptom**: Fast-moving objects tunnel through obstacles
- **Solution**: Enable continuous collision detection, reduce time step

### Debugging Tools

```cpp
// Enable debug visualization
engine->setDebugVisualization(true);

// Log physics state
engine->setLoggingLevel(physics::LogLevel::Verbose);

// Get detailed constraint information
auto constraintInfo = engine->getConstraintDebugInfo();
for (const auto& info : constraintInfo) {
    std::cout << "Constraint ID: " << info.id << std::endl;
    std::cout << "Error: " << info.error << std::endl;
    std::cout << "Iterations: " << info.iterations << std::endl;
}
```

### Support

For additional support:
- Check the API documentation in the `docs/api` directory
- Visit our GitHub issues page
- Join our Discord community channel
