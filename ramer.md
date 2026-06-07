# RTNeural - Ramer Digital

**Copyright (c) 2026, RAMER DIGITAL LTDA. All rights reserved.**  
*Date: June 7, 2026*  

**License (BSD 3-Clause License)**
Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:
1. Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.
2. Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.
3. Neither the name of RAMER DIGITAL LTDA nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.

---

## Conventions & Rules

### 1. Git & Submodule Management
* **Strict Submodule Path Alignment:** Submodules must always maintain correct relative paths (`gitdir`) in their respective `.git` files. Never hardcode absolute or environment-specific paths.
* **Pristine Submodule Status:** Do not commit local modifications directly inside submodule directories unless actively tracking a change for the fork. Keep the working tree clean.
* **Syncing Submodules:** When updating submodule references or changing target repositories, run both `git submodule sync` and `git submodule update` to propagate settings.

### 2. Workspace Hygiene
* **Clean Working Directory:** Always clean untracked artifacts and build outputs before staging commits. Use `git clean -fd` within submodules to discard untracked compiler output or temporary files.
* **Target Backends:** Ensure performance benchmarking is performed when switching between Eigen and XSIMD backends to verify execution times.

### 3. Documentation
* **Language Standard:** All internal documentation, markdown files, and commit logs under Ramer Digital repositories must be written in **International English**.

### 4. Licensing & Copyright Rules
* **Preservation of Original Credits:** Never delete or alter the original LICENSE file or any copyright headers of the original authors in the inherited source files.
* **Copyright Coexistence:** All new code contributions and modifications made by RAMER DIGITAL LTDA must receive their own copyright header and follow the BSD 3-Clause License, coexisting with the original project credits.

### 5. AI Interaction Rules (Mandatory & Final)
* **No Remote Publishing:** The AI must never perform git push or publish operations to any remote host or repository.
* **Supervised Local Operations:** The AI is permitted to perform local git commits and other workspace operations only under the direct supervision of the user, requiring explicit manual confirmation and approval before any execution.

---

## Project Description
**RTNeural** is a lightweight neural network inferencing engine written in C++, specifically designed for real-time systems and real-time audio processing.

The engine enables loading pre-trained weights from popular frameworks like TensorFlow and PyTorch (using JSON files) and runs inference efficiently.

### Key Features:
* **Lightweight & Fast:** Optimized for low latency.
* **Multiple Backends:** Supports **Eigen**, **xsimd** (for SIMD optimizations), and a fallback in **C++ STL**.
* **Supported Layers:** Dense, GRU, LSTM, Conv1D, Conv2D, BatchNorm1D, BatchNorm2D.
* **Supported Activations:** tanh, ReLU, Sigmoid, SoftMax, ELu, PReLU.

---

## General Work Focus
This workspace tracks the development, optimization, and environment configurations of the RTNeural project within the RAMER DIGITAL LTDA infrastructure.

### Environment Setup:
* **Primary Repository:** `ramerdigital/RTNeural`
* **XSIMD Submodule:** Configured to point to our corporate fork at `https://github.com/ramerdigital/xsimd.git` to enable custom SIMD instruction sets and platform-specific optimizations.
* **Build System:** Native CMake configuration with dynamic backend selection support.

---

## Architecture

### Purpose
The primary purpose of RTNeural is to provide a highly optimized, low-latency, and deterministic neural network inference engine. It is specifically designed to be embedded within real-time environments, such as digital signal processing (DSP) pipelines and real-time audio plugins (VST, AU, CLAP), where garbage collection or dynamic memory allocation spikes during processing are unacceptable.

### Functioning
RTNeural operates using two primary paradigms:
1. **Compile-Time Static Models (`RTNeural::ModelT`):** The network's topology, layer sizes, and activation functions are defined as C++ template parameters. This allows the compiler to perform extreme optimizations, such as loop unrolling, vectorization, and compile-time allocation of execution buffers.
2. **Run-Time Dynamic Models:** The network is constructed dynamically from a JSON configuration file. While more flexible, it incurs slightly more overhead than the compile-time counterpart.

Regardless of the instantiation method, the execution flow follows a simple three-step cycle:
* **Resetting State:** For recurrent architectures (e.g., GRU, LSTM), internal hidden states are reset via `reset()` to ensure deterministic output.
* **Feeding Inputs:** Providing input buffers to the model.
* **Forward Propagation:** Executing the network layers sequentially via the `forward()` method to generate the output sample or block.

### Applied Technology
* **Template Metaprogramming:** Zero-overhead abstractions and static sizing of weight matrices.
* **SIMD Vectorization:** Integration with XSIMD (via the Ramer Digital fork) and Eigen backends to execute parallel mathematical operations on AVX, SSE, and ARM NEON architectures.
* **Header-Only Integration Capability:** Can be configured as a header-only library with predefined byte alignment settings (`RTNEURAL_DEFAULT_ALIGNMENT`).

---
