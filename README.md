# 🧠 RAG vs. CAG vs. Hybrid — Interactive 3D Architecture Explorer

An interactive WebGL / Three.js 3D architecture simulator that visualizes, benchmarks, and compares **Retrieval-Augmented Generation (RAG)**, **Cache-Augmented Generation (CAG)**, and **Hybrid Tiered (CAG + RAG)** LLM inference pipelines in real time.

Built to demonstrate how preloading static knowledge into an LLM's extended **Key-Value (KV) Cache** eliminates multi-hop runtime retrieval overhead, cutting Time-To-First-Token (TTFT) latency by up to **9x** (`~62ms` vs `~580ms`) while illustrating when dynamic vector search remains essential.

---

## ✨ Key Features

* **4 Interactive 3D View Modes**
  * **Side-by-Side Comparison (`Compare Both`)**: Simultaneous 3D visualization of the 6-stage RAG multi-hop pipeline against the 3-stage CAG direct KV-cache express path.
  * **RAG Pipeline View**: Isolated inspection of query embedding (`+45ms`), 3D Vector DB ANN cluster search (`+190ms`), Cross-Encoder Top-K Reranking (`+140ms`), and LLM Full Context Prefill.
  * **CAG Pipeline View**: Isolated inspection of direct ingress into the LLM Core surrounded by a preloaded orbital KV-Cache Ring (`+18ms` zero runtime retrieval).
  * **Hybrid Tiered View (`CAG + RAG`)**: Demonstrates production-grade semantic routing where `80%` of hot/static queries hit the Tier-0 KV Ring and `20%` of live/volatile queries fall back to the Tier-1 Live Vector DB.
* **Real-Time Particle Telemetry & Latency Race**: Launch test query pulses across pipelines to watch packet traversal speeds, Top-K vector node activations, and live millisecond TTFT race counters.
* **Dynamic Workload & VRAM Calculator**: Interactive sliders for **Corpus Size (Tokens)** and **Data Volatility (Update Frequency)** dynamically compute KV-Cache VRAM usage, latency breakdowns, and an automated architecture recommendation.
* **Raycasting 3D Node Inspector**: Click directly on any 3D geometry in the viewport (or select quick-inspect pills) to view component telemetry, architectural roles, pros, and primary bottlenecks.

---

## 🏗️ Architectural Breakdown Modeled in the Scene

| Pipeline Mode | Stages Modeled in 3D | Simulated TTFT | Ideal Workload Profile |
| :--- | :--- | :--- | :--- |
| **RAG (Multi-Hop)** | `User Query` → `Embedding Model (+45ms)` → `Vector DB Cluster (+190ms)` → `Cross-Encoder Reranker (+140ms)` → `LLM Full Prefill` → `Response` | `~580ms` | Massive corpora (`>1M` tokens) or high-volatility live data |
| **CAG (Direct KV)** | `User Query` → `LLM Core + Preloaded KV Ring (+18ms)` → `Instant Stream` | `~62ms` | Bounded corpora (`<500K` tokens) with static/slow-changing docs |
| **Hybrid (Tiered)** | `User Query` → `Semantic Router` → `Tier-0 KV Ring (80% Hot)` OR `Tier-1 Live Vector DB (20% Cold)` → `Unified Output` | `~145ms` Weighted | Enterprise production mixing core manuals with live feeds |

### 3D Geometries & Visual Language
* **Ingress / Egress Nodes (`SphereGeometry`)**: Cyan, amber, emerald, and violet spheres representing query ingress and token stream egress.
* **Embedding Model (`IcosahedronGeometry`)**: Faceted golden polyhedron representing dense vector transformation (`+45ms`).
* **Vector DB Cluster (`3x3x3 BoxGeometry Lattice + Wireframe Bounds`)**: 27-node 3D matrix where specific Top-K semantic match cubes illuminate in cyan during ANN similarity search (`+190ms`).
* **Cross-Encoder Reranker (`BoxGeometry`)**: Rose-tinted filter block representing Top-K passage scoring and pruning (`+140ms`).
* **RAG LLM Core (`IcosahedronGeometry + Wireframe Cage`)**: Amber core illustrating heavy runtime context prefill computation.
* **CAG LLM Core + Preloaded KV Ring (`SphereGeometry + Dual TorusGeometry Rings`)**: Cyan core encircled by dual tilted emerald orbital rings representing pre-computed Key-Value tensors pinned in GPU VRAM (`+18ms`).
* **Semantic Router (`OctahedronGeometry`)**: Purple diamond node in Hybrid mode that splits traffic between the Tier-0 KV cache path and the Tier-1 Vector DB fallback path.

---

## 📂 Project Structure

```text
RAG-vs-CAG/
├── index.html   # Complete 3D WebGL simulator (Three.js scene, HUD UI, and telemetry logic)
└── README.md    # Project documentation
```

---

## 🛠️ Tech Stack

* **3D Rendering Engine**: [Three.js](https://threejs.org/) (`r128`) — WebGLRenderer, PerspectiveCamera, Raycaster, CatmullRomCurve3, TubeGeometry, and custom procedural geometries.
* **Camera & Viewport Controls**: `THREE.OrbitControls` with smooth damping (`enableDamping`) and custom linear interpolation (`lerp`) camera transitions between architecture modes.
* **2D-to-3D Spatial HUD Labels**: Custom 3D world-to-screen projection (`Vector3.project(camera)`) rendering crisp HTML/CSS glassmorphic telemetry badges anchored above 3D meshes.
* **Styling & UI Framework**: [Tailwind CSS](https://tailwindcss.com/) (CDN) paired with custom CSS glassmorphism (`backdrop-filter: blur(16px)`), dark-space grid styling, and responsive HUD overlays.
* **Typography**: Google Fonts (`Space Grotesk` for technical headers, `Inter` for UI controls, and `JetBrains Mono` for latency and VRAM telemetry).
* **Icons**: [Lucide Icons](https://lucide.dev/) for HUD controls and telemetry badges.
* **Deployment Architecture**: Zero-build, dependency-free single-file HTML5 bundle (`index.html`) that runs directly in any modern WebGL-enabled browser.

---

## 🚀 Getting Started

Because the entire simulator is packaged as a standalone, zero-build WebGL application, you can run it immediately without Node.js or bundler setup:

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/](https://github.com/)<your-username>/RAG-vs-CAG.git
   cd RAG-vs-CAG
   ```
2. **Launch in your browser:**
   * **Option A (VS Code Live Server):** Open the folder in VS Code and click **Go Live** in the bottom-right status bar (or right-click `index.html` → **Open with Live Server**).
   * **Option B (Local HTTP Server):**
     ```bash
     # Windows (PowerShell / CMD)
     python -m http.server 8080

     # macOS / Linux
     python3 -m http.server 8080
     ```
     Then open `http://localhost:8080` in Chrome, Edge, Firefox, or Safari.
   * **Option C (Direct Open):** Double-click `index.html` in your file explorer.

---

## 🎮 Controls & Interaction Guide

* **Orbit / Rotate Scene**: Left-click + Drag anywhere on the 3D canvas.
* **Zoom In / Out**: Scroll wheel or pinch gesture.
* **Pan Camera**: Right-click + Drag.
* **Inspect 3D Component**: Click directly on any 3D node (or use the bottom-left quick-inspect pills) to load its deep-dive telemetry card.
* **Switch Architecture Mode**: Use the top-center mode switcher (`Compare Both`, `RAG Pipeline`, `CAG Pipeline`, `Hybrid (CAG+RAG)`).
* **Trigger Latency Benchmark**: Click **"Send Test Query Pulse"** in the left control panel to animate a live request race across the active pipelines.

---

## 📄 License

Distributed under the MIT License. Free to use, remix, and share for architectural demonstrations, technical blog posts, and educational content.