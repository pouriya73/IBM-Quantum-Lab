# IBM-Quantum-Lab


<img width="1087" alt="Qiskit_Runtime_architecture1" src="https://user-images.githubusercontent.com/13979489/180871988-b5eb53e9-d50e-4542-8e95-92ede7cbdc3f.png">




Plans for 4 new quantum processors, breakthrough technologies and leaps forward in scale, quality and speed. Jay Gambetta and team take us on a journey to 2025 and the era of the quantum-centric supercomputer.




![ibm-q-hr](https://user-images.githubusercontent.com/13979489/180872278-3e3100d7-8976-4643-a61c-af084f3be245.jpg)





### VQE
The Variational Quantum Eigensolver (VQE) is a central algorithm in many applications from e.g. quantum chemistry or optimization. This tutorial shows you how to run the VQE as a Qiskit Runtime program. We'll start off by defining the algorithm settings, such as the Hamiltonian and ansatz, and then run a VQE both locally, on your machine, and remotely, using the Qiskit Runtime.

Note: You can find tutorials on solving more comprehensive problems, such as finding the ground state of the lithium hydride molecule, using the VQE (and Qiskit Runtime) within the tutorials of Qiskit Nature.

### System Hamiltonian

Let's start by defining the operator of which we want to determine the ground state. Here we'll chose a simple diagonal Hamiltonian  𝐻̂   acting with Pauli-Z operators on the first two qubits

𝐻̂ =𝑍̂ 0⊗𝑍̂ 1.

We can construct this Hamiltonian with Qiskit's opflow module:

```python
from qiskit.opflow import Z, I

num_qubits = 4
hamiltonian = (Z ^ Z) ^ (I ^ (num_qubits - 2))
```

This Hamiltonian has a ground state energy of -1.

```python
target_energy = -1
```

### Parameterized Ansatz Circuit
Next, we choose a parameterized quantum circuit  𝑈̂ (𝜃)  to prepare the ansatz wavefunction

|𝜓(𝜃)⟩=𝑈̂ (𝜃)|0⟩.

We'll use the EfficientSU2 circuit from Qiskit's circuit library, which is a hardware efficient, heuristic ansatz with alternating rotation and entanglement layers.

```python
from qiskit.circuit.library import EfficientSU2

# the rotation gates are chosen randomly, so we set a seed for reproducibility
ansatz = EfficientSU2(num_qubits, reps=1, entanglement="linear", insert_barriers=True)
ansatz.draw("mpl", style="iqx")
```

![download](https://user-images.githubusercontent.com/13979489/180871441-08a39087-a752-40e0-a889-31b28edc2aa6.png)

### Solve with the VQE
Now that we have the problem and ansatz specified we can use the Variational Quantum Eigensolver (VQE) to solve for the minimal eigenvalue of our Hamiltonian.

The VQE requires a classical optimization routine, along with an initial point, to calculate the parameter updates.

