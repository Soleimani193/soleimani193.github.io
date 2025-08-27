## GKR Protocol: High-Level Description

Reference: [MIT Lecture on GKR Protocol](https://www.youtube.com/watch?v=db1xAyO4YgM&list=PLUl4u3cNGP61EZllk7zwgvPbI4kbnKhWz&index=4)

The GKR (Goldwasser-Kalai-Rothblum) protocol is a powerful interactive proof system for verifying computations efficiently. Below is a high-level overview of its key steps:

1. **Arithmetization of the Circuit**  
   Replace all AND/OR gates in the circuit with addition and multiplication operations.

2. **Layered Circuit Structure**  
   - The circuit is organized into layers, where each gate in a layer connects to gates in the previous layer.  
   - If this is not the case, dummy gates are added to ensure a layered structure.  

3. **Uniform Layer Size**  
   - Assume each layer has \( s = 2^k \) gates. If not, dummy gates are added to pad the layer.

4. **Prover's Workflow**  
   - The prover starts at the output layer and works backward toward the input layer, step by step (from layer \( 0 \) to layer \( d \)).  
   - For each layer, the prover computes the output of each gate and calculates the **multilinear extension** of the layer.  

     - For gates indexed \( 1, \dots, s \), the value of \( j \)-th gate is represented as \( f(\text{k-bit binary representation of } j) = y_j \).  
     - The multilinear extension of \( f \) is then computed.
  
   - The multilinear extension of layer \( i \) is related to that of layer \( i+1 \) based on the circuit's structure that is known to the verifier.  
This relationship can be expressed as a sum over hypercubes, and the **sum-check protocol** is applied to verify the relation.

---

## What is a Multilinear Extension?

A multilinear extension is a way to extend a function defined on binary inputs to a polynomial that works over a larger field. Here's an example:

1. **Binary Function**  
   Imagine a function \( f(x, y) \) where \( x, y \in \{0, 1\} \).  
   There are only **4 possible inputs**:  
   \( (0, 0), (0, 1), (1, 0), (1, 1) \).  
   You can think of \( f \) as a **table of values** for these points.

2. **Goal of the Extension**  
   The goal is to turn this table-defined function into a **polynomial** that works on any inputs \( x, y \in F \) (a whole field, not just \( 0/1 \)).

---

This document provides a concise introduction to the GKR protocol and its foundational concepts. For a deeper dive, refer to the linked lecture or additional resources.