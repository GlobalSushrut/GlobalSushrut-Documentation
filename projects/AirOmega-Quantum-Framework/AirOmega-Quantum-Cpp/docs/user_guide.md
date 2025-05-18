# AirOmega-Quantum-Framework - user_guide

*This documentation is from the private repository AirOmega-Quantum-Framework.*

---

# AirOmega Quantum C++ Engine: User Guide

## Getting Started

This guide will help you get started with the AirOmega Quantum C++ Engine, a powerful framework for quantum computing that can connect to both simulators and real quantum hardware.

### System Requirements

- C++17 compatible compiler (GCC 8+, Clang 7+, MSVC 2019+)
- CMake 3.10+
- PennyLane 0.37.0 (for quantum machine learning integration)
- IBM Quantum account (for connecting to real quantum hardware)

### Installation

#### Building from Source

1. Clone the repository:
   ```bash
   git clone https://github.com/example/airomega-quantum-cpp.git
   cd airomega-quantum-cpp
   ```

2. Create a build directory:
   ```bash
   mkdir -p build && cd build
   ```

3. Configure with CMake:
   ```bash
   cmake ..
   ```

4. Build the library and examples:
   ```bash
   make
   ```

5. (Optional) Install the library system-wide:
   ```bash
   sudo make install
   ```

#### Using with an Existing Project

1. If you've installed the library:
   ```cpp
   #include <airomega/airomega_math.hpp>
   #include <airomega/tunnel_tree.hpp>
   #include <airomega/hardware_interface.hpp>
   #include <airomega/hardware_tester.hpp>
   ```

2. If you're including the library as a subproject:
   ```cpp
   #include "airomega_quantum_cpp/include/airomega_math.hpp"
   #include "airomega_quantum_cpp/include/tunnel_tree.hpp"
   #include "airomega_quantum_cpp/include/hardware_interface.hpp"
   #include "airomega_quantum_cpp/include/hardware_tester.hpp"
   ```

## Basic Usage

### Creating and Running a Simple Quantum Circuit

```cpp
#include "hardware_interface.hpp"
#include <iostream>
#include <memory>

int main() {
    // Create a quantum simulator
    auto simulator = std::make_shared<airomega::QuantumSimulator>();
    
    // Create a circuit with 1 qubit and 1 classical bit
    airomega::QuantumCircuit circuit(1, 1);
    
    // Add gates to create a superposition
    circuit.h(0);        // Hadamard gate on qubit 0
    circuit.measure(0, 0);  // Measure qubit 0 and store in bit 0
    
    // Execute the circuit
    auto counts = simulator->execute_circuit(circuit, 1000);
    
    // Print the results
    std::cout << "Results:\n";
    for (const auto& [state, count] : counts) {
        std::cout << "  |" << state << "> : " << count << " (" 
                  << (count / 10.0) << "%)\n";
    }
    
    return 0;
}
```

### Working with the Tree Tunnel

```cpp
#include "tunnel_tree.hpp"
#include <iostream>

int main() {
    // Create a tree tunnel
    airomega::TreeTunnel tree;
    
    // Initialize with a quantum state
    tree.initialize(0.1, 0.2, 0.3);
    
    // Build the tree
    tree.build_tree();
    
    // Retrieve nodes and analyze
    const auto& nodes = tree.get_nodes();
    std::cout << "Tree contains " << nodes.size() << " nodes.\n";
    
    // Get clean nodes
    auto clean_nodes = tree.get_clean_nodes();
    std::cout << "Found " << clean_nodes.size() << " clean nodes.\n";
    
    return 0;
}
```

### Connecting to Real Quantum Hardware

```cpp
#include "hardware_interface.hpp"
#include <iostream>
#include <memory>

int main() {
    // Your IBM Quantum API token
    std::string token = "YOUR_IBM_QUANTUM_TOKEN";
    
    // The backend to use (e.g., "ibmq_manila")
    std::string backend = "ibmq_manila";
    
    // Create a connection to IBM Quantum hardware
    auto ibm_hardware = std::make_shared<airomega::IBMQuantumHardware>(token, backend);
    
    // Check if connection is successful
    if (!ibm_hardware->is_connected()) {
        std::cerr << "Failed to connect to IBM Quantum hardware.\n";
        return 1;
    }
    
    // Create a circuit with 2 qubits and 2 classical bits
    airomega::QuantumCircuit circuit(2, 2);
    
    // Create a Bell state
    circuit.h(0);
    circuit.cnot(0, 1);
    circuit.measure(0, 0);
    circuit.measure(1, 1);
    
    // Execute on real hardware
    std::cout << "Executing on " << ibm_hardware->get_name() << "...\n";
    auto counts = ibm_hardware->execute_circuit(circuit, 1024);
    
    // Print the results
    std::cout << "Results:\n";
    for (const auto& [state, count] : counts) {
        std::cout << "  |" << state << "> : " << count << " (" 
                  << (count / 10.24) << "%)\n";
    }
    
    return 0;
}
```

## Running Hardware Tests

The AirOmega Quantum C++ Engine includes a comprehensive hardware testing framework to evaluate quantum hardware performance.

### Basic Testing

```cpp
#include "hardware_interface.hpp"
#include "hardware_tester.hpp"
#include <iostream>
#include <memory>

int main() {
    // Create a quantum simulator
    auto simulator = std::make_shared<airomega::QuantumSimulator>();
    
    // Create a hardware tester
    airomega::AirOmegaHardwareTester tester(simulator);
    
    // Run a single test
    auto results = tester.test_gate_fidelity(100);
    
    // Print results
    std::cout << "Gate Fidelity Test Results:\n";
    std::cout << "Conventional mean fidelity: " 
              << results.summary["conventional_mean_fidelity"] << "\n";
    std::cout << "AirOmega mean fidelity: " 
              << results.summary["airomega_mean_fidelity"] << "\n";
    std::cout << "Improvement: " 
              << results.summary["fidelity_improvement_percent"] << "%\n";
    
    return 0;
}
```

### Comprehensive Testing

```cpp
#include "hardware_interface.hpp"
#include "hardware_tester.hpp"
#include <iostream>
#include <memory>
#include <string>

int main(int argc, char** argv) {
    // Parse command line arguments
    bool use_real_hardware = false;
    std::string token;
    std::string backend = "ibmq_qasm_simulator";
    
    for (int i = 1; i < argc; ++i) {
        std::string arg = argv[i];
        if (arg == "--real-hardware") {
            use_real_hardware = true;
        } else if (arg == "--token" && i + 1 < argc) {
            token = argv[++i];
        } else if (arg == "--backend" && i + 1 < argc) {
            backend = argv[++i];
        }
    }
    
    // Run the demonstration function
    airomega::demonstrate_hardware_testing(use_real_hardware, token, backend);
    
    return 0;
}
```

## Advanced Features

### Custom Quantum Circuit Generation

The AirOmega framework provides utilities for creating specialized quantum circuits:

```cpp
#include "hardware_interface.hpp"
#include <iostream>

int main() {
    // Generate a single-qubit AirOmega circuit
    double theta = 0.25;
    double phi = 0.33;
    double psi = 0.45;
    
    auto circuit = airomega::AirOmegaCircuitGenerator::create_single_qubit_circuit(
        theta, phi, psi, true); // true = include measurement
    
    // Generate an entangled AirOmega circuit
    auto entangled_circuit = airomega::AirOmegaCircuitGenerator::create_entangled_circuit(
        0.1, 0.2, 0.3,   // Parameters for first qubit
        0.4, 0.5, 0.6,   // Parameters for second qubit
        true);           // Include measurements
    
    // Print the circuit
    std::cout << entangled_circuit.to_string() << std::endl;
    
    return 0;
}
```

### Working with Test Results

The test results can be saved, visualized, and analyzed:

```cpp
#include "hardware_tester.hpp"
#include <iostream>
#include <memory>

int main() {
    // Create a hardware tester
    auto simulator = std::make_shared<airomega::QuantumSimulator>();
    airomega::AirOmegaHardwareTester tester(simulator);
    
    // Run all tests
    auto all_results = tester.run_all_tests();
    
    // Save results to files
    tester.save_results(all_results["gate_fidelity"], "gate_fidelity.json", "json");
    tester.save_results(all_results["decoherence"], "decoherence.csv", "csv");
    
    // Visualize results
    tester.visualize_results(all_results["noise_resilience"], 
                            "Noise Resilience Test", 
                            "noise_resilience_plot.png");
    
    return 0;
}
```

## Troubleshooting

### Common Issues

1. **Compiler errors related to C++17**:
   Make sure your compiler supports C++17 and you've enabled it in your build:
   ```bash
   cmake -DCMAKE_CXX_STANDARD=17 ..
   ```

2. **Connection to IBM Quantum fails**:
   - Verify your API token is correct and has not expired
   - Check that the backend name is valid and available to your account
   - Ensure you have internet connectivity

3. **Missing PennyLane**:
   If you're using quantum machine learning features, ensure PennyLane 0.37.0 is installed:
   ```bash
   pip install pennylane==0.37.0
   ```

4. **Memory issues with large quantum systems**:
   The simulator has memory limitations based on your system. For large quantum systems:
   - Reduce the number of qubits
   - Lower the tree depth
   - Use a more powerful machine
   - Consider using real quantum hardware for smaller circuits

### Getting Help

If you encounter issues not covered in this guide:

1. Check the API documentation in the `docs/` directory
2. Review the example code in the `examples/` directory
3. Run the test suite to verify your installation:
   ```bash
   cd build
   make test
   ```

## PennyLane Integration

The AirOmega Quantum C++ Engine is compatible with PennyLane 0.37.0 for quantum machine learning applications. Here's a basic example of integration:

```cpp
#include <pennylane.hpp>
#include "hardware_interface.hpp"
#include <iostream>

int main() {
    // Create an AirOmega circuit
    auto circuit = airomega::AirOmegaCircuitGenerator::create_single_qubit_circuit(
        0.1, 0.2, 0.3, false); // no measurement
    
    // Convert to PennyLane operations
    // Note: This is a conceptual example, actual implementation may vary
    auto pl_dev = pennylane::LightningDevice(1);
    
    // Add the operations from our circuit
    pl_dev.apply("Hadamard", {0}, {});
    pl_dev.apply("RZ", {0}, {0.1 * 2 * M_PI});
    pl_dev.apply("RX", {0}, {0.2 * 2 * M_PI});
    pl_dev.apply("RZ", {0}, {0.3 * 2 * M_PI});
    
    // Measure the expectation value
    auto result = pl_dev.expval("PauliZ", {0}, {});
    
    std::cout << "Expectation value: " << result << std::endl;
    
    return 0;
}
```

## Conclusion

This user guide provides a starting point for working with the AirOmega Quantum C++ Engine. For more detailed information on specific classes and methods, please refer to the API documentation.

The AirOmega framework offers a unique approach to quantum computing with its specialized mathematical foundation and tree-based state representation. By leveraging these concepts, you can develop quantum applications that are more resilient to noise and decoherence, making them more suitable for execution on real quantum hardware.
