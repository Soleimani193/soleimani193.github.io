## GKR Protocol: High-Level Description

Reference: [MIT Lecture on GKR Protocol](https://www.youtube.com/watch?v=db1xAyO4YgM&list=PLUl4u3cNGP61EZllk7zwgvPbI4kbnKhWz&index=4)

The GKR (Goldwasser-Kalai-Rothblum) protocol is a powerful interactive proof system for verifying computations efficiently. Below is a high-level overview of its key steps:

1. **Arithmetization of the Circuit**  
   Replace all AND/OR gates in the circuit with addition and multiplication operations.

2. **Layered Circuit Structure**  
   - The circuit is organized into layers, where each gate in a layer connects to gates in the previous layer.  
   - If this is not the case, dummy gates are added to ensure a layered structure.  

3. **Uniform Layer Size**  
   - Assume each layer has $ s = 2^k $ gates. If not, dummy gates are added to pad the layer.

4. **Prover's Workflow**  
   - The prover starts at the output layer and works backward toward the input layer, step by step (from layer $ 0 $ to layer $ d $).  
   - For each layer, the prover computes the output of each gate and calculates the **multilinear extension** of the layer.  

     - For gates indexed $ 1, \dots, s $, the value of $ j $-th gate is represented as $$f(\text{k-bit binary representation of } j) = y_j .$$  
     - The multilinear extension of $ f $ is then computed.
  
   - The multilinear extension of layer $ i $ is related to that of layer $ i+1 $ based on the circuit's structure that is known to the verifier.  
This relationship can be expressed as a sum over hypercubes, and the **sum-check protocol** is applied to verify the relation.

---

## What is a Multilinear Extension?

Imagine you have a function $ f $ that only takes inputs of bits (0 or 1).  
Example: $ f(x, y) $ where $ x, y \in \{0, 1\} $.  
So there are only **4 possible inputs**:  
$ (0, 0), (0, 1), (1, 0), (1, 1) $.

You can think of $ f $ as just a **table of values** on these points.

---

### Goal of the Extension

We’d like to turn this table-defined function into a **polynomial** that works on _any_ inputs $ x, y \in F $ (a whole field, not just $ 0/1 $).

- On Boolean inputs (0 or 1), the polynomial matches the table exactly.
- On non-Boolean inputs, it gives a consistent “extension” of the function.

That polynomial is called the **multilinear extension**.

### Example

Say our function $ f $ is:

$$
f(0, 0) = 5, \; f(0, 1) = 2, \; f(1, 0) = 7, \; f(1, 1) = 3.
$$

The multilinear extension $ F(x, y) $ is the polynomial:

$$
F(x, y) = f(0, 0)(1 - x)(1 - y) + f(1, 0)x(1 - y) + f(0, 1)(1 - x)y + f(1, 1)xy
$$

Plugging in:

- $ F(0, 0) = 5 $
- $ F(1, 0) = 7 $
- $ F(0, 1) = 2 $
- $ F(1, 1) = 3 $

So it matches perfectly.  
But it also gives you values for things like $ F(0.5, 0.5) $, which are not in the original table.

---

### Multilinear Extension Formula

Given $ f: \{0, 1\}^n \to \mathbb{F} $, its multilinear extension $ F: \mathbb{F}^n \to \mathbb{F} $ is defined as:

$$
F(x_1, \dots, x_n) = \sum_{(b_1, \dots, b_n) \in \{0, 1\}^n} 
f(b_1, \dots, b_n) \cdot \prod_{i=1}^n \Big( (1 - b_i)(1 - x_i) + b_i x_i \Big).
$$

---

### Intuition

- Each Boolean tuple $ (b_1, \dots, b_n) $ contributes one term.
- The product:

$$
\Delta(x, b) := \prod_{i=1}^n \big( (1 - b_i)(1 - x_i) + b_i x_i \big)
$$

is an “indicator polynomial”:

- It equals $ 1 $ when $ x = (b_1, \dots, b_n) $.
- It equals $ 0 $ when $ x $ is any other Boolean vector.

So at Boolean points, only the matching tuple survives, giving exactly $ f(b_1, \dots, b_n) $.

---

### Relation Between Two Layers

Let $ F_i $ and $ F_{i+1} $ be the multilinear extensions of layers $ i $ and $ i+1 $, and let $ z \in \{0, 1\}^k $ be a gate in layer $ i $. Then:

$$
F_i(z) = \sum_{x_1, x_2 \in \{0, 1\}^k} \Big[ 
\text{Add}(z, x_1, x_2) \cdot \big(F_{i+1}(x_1) + F_{i+1}(x_2)\big) + 
\text{Mul}(z, x_1, x_2) \cdot F_{i+1}(x_1) F_{i+1}(x_2) 
\Big]
$$

Where:

- $ \text{Add}(z, x, y) = 1 $ if gate $ z $ is addition, $ 0 $ otherwise.
- $ \text{Mul}(z, x, y) = 1 $ if gate $ z $ is multiplication, $ 0 $ otherwise.
- The sum runs over the **Boolean hypercube** for the input gates $ x_1, x_2 $.

---

So the general multilinear extension of one layer with respect to the previous layer can be written as:

$$
F(x_1, \dots, x_n) = \sum_{(b_1, \dots, b_n) \in \{0, 1\}^n} 
f(b_1, \dots, b_n) \cdot \Delta(x, b)
$$

Replacing $ f(b_1, \dots, b_n) $ with $ F_i(z) $ , for $k =n$ from:

$$
F_i(z) = \sum_{x_1, x_2 \in \{0, 1\}^k} \Big[ 
\text{Add}(z, x_1, x_2) \cdot \big(F_{i+1}(x_1) + F_{i+1}(x_2)\big) + 
\text{Mul}(z, x_1, x_2) \cdot F_{i+1}(x_1) F_{i+1}(x_2) 
\Big]
$$

Now we have a summation over the hypercube, and we can use the **sum-check protocol** to verify the resulting formula for each layer.