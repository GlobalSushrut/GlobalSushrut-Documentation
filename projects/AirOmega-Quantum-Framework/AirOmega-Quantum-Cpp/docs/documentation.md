# AirOmega-Quantum-Framework - documentation

*This documentation is from the private repository AirOmega-Quantum-Framework.*

---

# AirOmega Quantum C++ Engine: API Documentation

## Table of Contents

1. [Mathematical Utilities](#mathematical-utilities)
2. [Quantum Tree Tunnel](#quantum-tree-tunnel)
3. [Hardware Interface](#hardware-interface)
4. [Hardware Testing](#hardware-testing)
5. [Circuit Generation](#circuit-generation)

## Mathematical Utilities

### Namespace: `airomega`

#### Complex Type

```cpp
using Complex = std::complex<double>;
using ComplexVector = std::vector<Complex>;
```

#### Constants

```cpp
// Classification thresholds
constexpr double CLEAN_THRESHOLD = 1.5;
constexpr double DAMPED_THRESHOLD = 2.0;
```

#### Functions

##### `Complex omega(double theta)`

Calculates the omega function value for a given angle.

- **Parameters**:
  - `theta`: Angle parameter in range [0,1)
- **Returns**: Complex value representing e^(i*2π*theta)

##### `Complex normalized_omega(double theta)`

Calculates the normalized omega function value.

- **Parameters**:
  - `theta`: Angle parameter in range [0,1)
- **Returns**: Complex value with magnitude 1 and phase corresponding to theta

##### `ComplexVector t_omega_vector(double theta, double phi, double psi)`

Creates a T-omega vector from three angle parameters.

- **Parameters**:
  - `theta`: First angle parameter
  - `phi`: Second angle parameter
  - `psi`: Third angle parameter
- **Returns**: Vector of three complex values

##### `double calculate_norm_squared(double theta, double phi, double psi)`

Calculates the squared norm of the T-omega vector.

- **Parameters**:
  - `theta`: First angle parameter
  - `phi`: Second angle parameter
  - `psi`: Third angle parameter
- **Returns**: Sum of the squared magnitudes of the T-omega vector components

##### `std::string classify_node(double norm_squared)`

Classifies a quantum state based on its norm squared value.

- **Parameters**:
  - `norm_squared`: Squared norm of the T-omega vector
- **Returns**: Classification string ("Clean", "Damped", "Collapse", or "Invalid")

## Quantum Tree Tunnel

### Namespace: `airomega`

#### `TunnelNode` Class

```cpp
class TunnelNode {
public:
    double theta;
    double phi;
    double psi;
    int level;
    int parent_id;
    std::string classification;
    
    TunnelNode(double theta, double phi, double psi, int level, int parent_id);
};
```

##### Constructor: `TunnelNode(double theta, double phi, double psi, int level, int parent_id)`

Creates a new tunnel node with the specified parameters.

- **Parameters**:
  - `theta`: First angle parameter
  - `phi`: Second angle parameter
  - `psi`: Third angle parameter
  - `level`: Depth level in the tree
  - `parent_id`: ID of the parent node (-1 for root)

#### `TreeTunnel` Class

```cpp
class TreeTunnel {
public:
    TreeTunnel();
    ~TreeTunnel();
    
    void initialize(double theta, double phi, double psi);
    void build_tree();
    void add_node(double theta, double phi, double psi, int parent_id);
    
    const std::vector<TunnelNode>& get_nodes() const;
    std::vector<int> get_clean_nodes() const;
    const TunnelNode& get_node(int id) const;
    
    bool is_initialized() const;
};
```

##### Constructor: `TreeTunnel()`

Creates an empty tree tunnel structure.

##### Method: `void initialize(double theta, double phi, double psi)`

Initializes the tree with a root node.

- **Parameters**:
  - `theta`: First angle parameter for the root
  - `phi`: Second angle parameter for the root
  - `psi`: Third angle parameter for the root

##### Method: `void build_tree()`

Builds the complete tree structure by expanding nodes with variations.

##### Method: `void add_node(double theta, double phi, double psi, int parent_id)`

Adds a new node to the tree.

- **Parameters**:
  - `theta`: First angle parameter
  - `phi`: Second angle parameter
  - `psi`: Third angle parameter
  - `parent_id`: ID of the parent node

##### Method: `const std::vector<TunnelNode>& get_nodes() const`

Gets all nodes in the tree.

- **Returns**: Vector of all nodes

##### Method: `std::vector<int> get_clean_nodes() const`

Gets IDs of all nodes classified as "Clean".

- **Returns**: Vector of node IDs

##### Method: `const TunnelNode& get_node(int id) const`

Gets a specific node by ID.

- **Parameters**:
  - `id`: Node ID
- **Returns**: Reference to the node
- **Throws**: `std::out_of_range` if ID is invalid

##### Method: `bool is_initialized() const`

Checks if the tree has been initialized.

- **Returns**: `true` if initialized, `false` otherwise

## Hardware Interface

### Namespace: `airomega`

#### `QuantumGate` Struct

```cpp
struct QuantumGate {
    std::string type;
    std::vector<int> qubits;
    std::vector<double> params;
    
    QuantumGate(const std::string& type, 
                const std::vector<int>& qubits,
                const std::vector<double>& params = {});
};
```

##### Constructor: `QuantumGate(const std::string& type, const std::vector<int>& qubits, const std::vector<double>& params = {})`

Creates a quantum gate definition.

- **Parameters**:
  - `type`: Gate type (e.g., "H", "X", "CNOT", "RZ")
  - `qubits`: List of qubits the gate acts on
  - `params`: Optional parameters for parameterized gates

#### `QuantumCircuit` Class

```cpp
class QuantumCircuit {
public:
    QuantumCircuit(int num_qubits, int num_classical_bits = 0);
    
    QuantumCircuit& add_gate(const QuantumGate& gate);
    
    // Single-qubit gates
    QuantumCircuit& h(int qubit);
    QuantumCircuit& x(int qubit);
    QuantumCircuit& y(int qubit);
    QuantumCircuit& z(int qubit);
    
    // Rotations
    QuantumCircuit& rx(double angle, int qubit);
    QuantumCircuit& ry(double angle, int qubit);
    QuantumCircuit& rz(double angle, int qubit);
    
    // Two-qubit gates
    QuantumCircuit& cnot(int control, int target);
    
    // Measurement
    QuantumCircuit& measure(int qubit, int bit);
    
    // Accessors
    int get_num_qubits() const;
    int get_num_bits() const;
    const std::vector<QuantumGate>& get_gates() const;
    
    // String representation
    std::string to_string() const;
};
```

##### Constructor: `QuantumCircuit(int num_qubits, int num_classical_bits = 0)`

Creates a quantum circuit with the specified number of qubits and classical bits.

- **Parameters**:
  - `num_qubits`: Number of qubits in the circuit
  - `num_classical_bits`: Number of classical bits for measurements

##### Method: `QuantumCircuit& add_gate(const QuantumGate& gate)`

Adds a gate to the circuit.

- **Parameters**:
  - `gate`: Gate to add
- **Returns**: Reference to this circuit (for chaining)
- **Throws**: `std::out_of_range` if qubit indices are invalid

##### Gate Methods

Methods like `h()`, `x()`, `rx()`, etc. add specific gates to the circuit.
- Each returns a reference to the circuit for method chaining
- Throws `std::out_of_range` if qubit indices are invalid

##### Method: `measure(int qubit, int bit)`

Adds a measurement operation from a qubit to a classical bit.

- **Parameters**:
  - `qubit`: Qubit to measure
  - `bit`: Classical bit to store the result
- **Returns**: Reference to this circuit
- **Throws**: `std::out_of_range` if indices are invalid

##### Accessor Methods

- `get_num_qubits()`: Returns the number of qubits
- `get_num_bits()`: Returns the number of classical bits
- `get_gates()`: Returns the list of gates in the circuit

##### Method: `std::string to_string() const`

Returns a string representation of the circuit.

- **Returns**: Human-readable description of the circuit

#### `QuantumHardware` Abstract Class

```cpp
class QuantumHardware {
public:
    virtual ~QuantumHardware() = default;
    
    virtual std::string get_name() const = 0;
    virtual int get_num_qubits() const = 0;
    virtual bool is_simulator() const = 0;
    virtual std::map<std::string, std::string> get_hardware_info() const = 0;
    
    virtual std::map<std::string, int> execute_circuit(
        const QuantumCircuit& circuit, int shots) = 0;
};
```

##### Method: `virtual std::string get_name() const = 0`

Gets the name of the quantum hardware.

- **Returns**: Hardware name

##### Method: `virtual int get_num_qubits() const = 0`

Gets the number of qubits available on the hardware.

- **Returns**: Number of qubits

##### Method: `virtual bool is_simulator() const = 0`

Checks if this is a simulator or real hardware.

- **Returns**: `true` for simulator, `false` for real hardware

##### Method: `virtual std::map<std::string, std::string> get_hardware_info() const = 0`

Gets hardware properties.

- **Returns**: Map of property names to values

##### Method: `virtual std::map<std::string, int> execute_circuit(const QuantumCircuit& circuit, int shots) = 0`

Executes a quantum circuit on the hardware.

- **Parameters**:
  - `circuit`: Circuit to execute
  - `shots`: Number of times to run the circuit
- **Returns**: Map of measurement outcomes to counts

#### `QuantumSimulator` Class

```cpp
class QuantumSimulator : public QuantumHardware {
public:
    QuantumSimulator(int num_qubits = 5, const std::string& name = "AirOmega Simulator");
    
    // Implements QuantumHardware interface
    std::string get_name() const override;
    int get_num_qubits() const override;
    bool is_simulator() const override;
    std::map<std::string, std::string> get_hardware_info() const override;
    std::map<std::string, int> execute_circuit(
        const QuantumCircuit& circuit, int shots) override;
};
```

##### Constructor: `QuantumSimulator(int num_qubits = 5, const std::string& name = "AirOmega Simulator")`

Creates a quantum simulator.

- **Parameters**:
  - `num_qubits`: Maximum number of qubits supported (default: 5)
  - `name`: Name for the simulator (default: "AirOmega Simulator")

#### `IBMQuantumHardware` Class

```cpp
class IBMQuantumHardware : public QuantumHardware {
public:
    IBMQuantumHardware(const std::string& api_token, 
                        const std::string& backend_name = "ibmq_qasm_simulator");
    
    // Connection management
    bool initialize_connection();
    bool is_connected() const;
    
    // Implements QuantumHardware interface
    std::string get_name() const override;
    int get_num_qubits() const override;
    bool is_simulator() const override;
    std::map<std::string, std::string> get_hardware_info() const override;
    std::map<std::string, int> execute_circuit(
        const QuantumCircuit& circuit, int shots) override;
    
    // QASM conversion
    std::string to_qasm(const QuantumCircuit& circuit);
    std::map<std::string, int> execute_qasm(
        const std::string& qasm, int shots);
};
```

##### Constructor: `IBMQuantumHardware(const std::string& api_token, const std::string& backend_name = "ibmq_qasm_simulator")`

Creates a connection to IBM Quantum hardware.

- **Parameters**:
  - `api_token`: IBM Quantum API token
  - `backend_name`: Name of the backend to use (default: "ibmq_qasm_simulator")

##### Method: `bool initialize_connection()`

Initializes the connection to IBM Quantum.

- **Returns**: `true` if connection was successful, `false` otherwise

##### Method: `bool is_connected() const`

Checks if the connection is established.

- **Returns**: `true` if connected, `false` otherwise

##### Method: `std::string to_qasm(const QuantumCircuit& circuit)`

Converts a circuit to OpenQASM format.

- **Parameters**:
  - `circuit`: Circuit to convert
- **Returns**: OpenQASM string

##### Method: `std::map<std::string, int> execute_qasm(const std::string& qasm, int shots)`

Executes OpenQASM code on the hardware.

- **Parameters**:
  - `qasm`: OpenQASM code
  - `shots`: Number of times to run the circuit
- **Returns**: Map of measurement outcomes to counts

#### `QuantumHardwareFactory` Class

```cpp
class QuantumHardwareFactory {
public:
    static std::unique_ptr<QuantumHardware> create_simulator(int num_qubits = 5);
    
    static std::unique_ptr<QuantumHardware> create_ibm_quantum(
        const std::string& api_token, const std::string& backend_name = "ibmq_qasm_simulator");
};
```

##### Method: `static std::unique_ptr<QuantumHardware> create_simulator(int num_qubits = 5)`

Creates a quantum simulator.

- **Parameters**:
  - `num_qubits`: Maximum number of qubits supported (default: 5)
- **Returns**: Unique pointer to a simulator

##### Method: `static std::unique_ptr<QuantumHardware> create_ibm_quantum(const std::string& api_token, const std::string& backend_name = "ibmq_qasm_simulator")`

Creates a connection to IBM Quantum hardware.

- **Parameters**:
  - `api_token`: IBM Quantum API token
  - `backend_name`: Name of the backend to use
- **Returns**: Unique pointer to an IBM Quantum connection

## Hardware Testing

### Namespace: `airomega`

#### `TestResults` Struct

```cpp
struct TestResults {
    std::map<std::string, double> summary;
    std::map<std::string, std::vector<std::map<std::string, double>>> data;
    
    std::string to_string() const;
};
```

##### Method: `std::string to_string() const`

Generates a string representation of the test results.

- **Returns**: Human-readable summary of the results

#### `AirOmegaHardwareTester` Class

```cpp
class AirOmegaHardwareTester {
public:
    AirOmegaHardwareTester(std::shared_ptr<QuantumHardware> hardware);
    
    std::shared_ptr<QuantumHardware> get_hardware() const;
    
    // Test methods
    TestResults test_gate_fidelity(int num_trials = 20, int shots = 1024);
    TestResults test_decoherence_resistance(int max_depth = 10, int shots = 1024);
    TestResults test_entangled_stability(int num_trials = 20, int shots = 1024);
    TestResults test_noise_resilience(int num_trials = 20, int shots = 1024);
    
    // Run all tests
    std::map<std::string, TestResults> run_all_tests();
    
    // Utility methods
    double calculate_fidelity(
        const std::map<std::string, int>& counts, const std::string& expected_state);
    double calculate_correlation(
        const std::map<std::string, int>& counts);
    
    // Results handling
    bool save_results(const TestResults& results, 
                      const std::string& filename, 
                      const std::string& format = "json");
    void visualize_results(const TestResults& results, 
                          const std::string& test_name, 
                          const std::string& save_path = "");
};
```

##### Constructor: `AirOmegaHardwareTester(std::shared_ptr<QuantumHardware> hardware)`

Creates a hardware tester with the specified quantum hardware.

- **Parameters**:
  - `hardware`: Quantum hardware to test
- **Throws**: `std::invalid_argument` if hardware is null

##### Method: `std::shared_ptr<QuantumHardware> get_hardware() const`

Gets the quantum hardware being tested.

- **Returns**: Shared pointer to the quantum hardware

##### Test Methods

Four main test methods evaluate different aspects of quantum hardware performance:
- `test_gate_fidelity`: Tests the fidelity of quantum gates
- `test_decoherence_resistance`: Tests resistance to decoherence over circuit depth
- `test_entangled_stability`: Tests stability of entangled states
- `test_noise_resilience`: Tests resilience to noise
  
Each method takes parameters for the number of trials and shots, and returns a `TestResults` structure.

##### Method: `std::map<std::string, TestResults> run_all_tests()`

Runs all four tests and returns their results.

- **Returns**: Map of test names to results

##### Utility Methods

- `calculate_fidelity`: Calculates the fidelity of a measurement result
- `calculate_correlation`: Calculates the correlation in entangled states

##### Results Handling Methods

- `save_results`: Saves test results to a file in the specified format
- `visualize_results`: Creates a visualization of the test results

## Circuit Generation

### Namespace: `airomega`

#### `AirOmegaCircuitGenerator` Class

```cpp
class AirOmegaCircuitGenerator {
public:
    static QuantumCircuit create_single_qubit_circuit(
        double theta, double phi, double psi, bool with_measurement = true);
    
    static QuantumCircuit create_entangled_circuit(
        double theta1, double phi1, double psi1,
        double theta2, double phi2, double psi2,
        bool with_measurement = true);
};
```

##### Method: `static QuantumCircuit create_single_qubit_circuit(double theta, double phi, double psi, bool with_measurement = true)`

Creates a single-qubit AirOmega circuit.

- **Parameters**:
  - `theta`, `phi`, `psi`: Angle parameters
  - `with_measurement`: Whether to include measurement
- **Returns**: Quantum circuit implementing the specified AirOmega state

##### Method: `static QuantumCircuit create_entangled_circuit(double theta1, double phi1, double psi1, double theta2, double phi2, double psi2, bool with_measurement = true)`

Creates a two-qubit entangled AirOmega circuit.

- **Parameters**:
  - `theta1`, `phi1`, `psi1`: Angle parameters for first qubit
  - `theta2`, `phi2`, `psi2`: Angle parameters for second qubit
  - `with_measurement`: Whether to include measurements
- **Returns**: Quantum circuit implementing the specified entangled state

#### Global Function

```cpp
void demonstrate_hardware_testing(
    bool use_real_hardware = false, 
    const std::string& api_token = "", 
    const std::string& backend_name = "ibmq_qasm_simulator");
```

##### Function: `void demonstrate_hardware_testing(bool use_real_hardware = false, const std::string& api_token = "", const std::string& backend_name = "ibmq_qasm_simulator")`

Demonstrates the AirOmega hardware testing capabilities.

- **Parameters**:
  - `use_real_hardware`: Whether to use real quantum hardware
  - `api_token`: IBM Quantum API token (required if `use_real_hardware` is true)
  - `backend_name`: Name of the backend to use
