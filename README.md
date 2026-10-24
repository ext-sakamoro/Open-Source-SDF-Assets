# Open Source SDF Assets

**991 free 3D assets in Signed Distance Function format, converted from [Open Source 3D Assets](https://www.opensource3dassets.com/).**

> Don't send data. Send the law.

## What is this?

Every 3D model in this repository is stored as a **Signed Distance Function** (`.asdf.json`) — a mathematical description of shape, not a polygon mesh. SDF assets are:

- **Resolution-independent** — render at any quality, from mobile to film
- **CSG-ready** — union, subtract, intersect shapes with a single operation
- **Parametric** — modify shape parameters without re-modeling
- **Streamable** — evaluate only the region you need, skip the rest

All assets were automatically converted from GLB using [ALICE-SDF](https://github.com/ext-sakamoro/ALICE-SDF)'s `mesh_to_sdf` pipeline (capsule approximation).

## Quick Start

### Python

```python
import alice_sdf
import json

# Load an SDF asset
with open("collections/pm-momuspark/Bench_01_Art.asdf.json") as f:
    data = json.load(f)

node = alice_sdf.from_json(json.dumps(data))

# Generate mesh at any resolution
vertices, indices = alice_sdf.to_mesh(node, (-5,-5,-5), (5,5,5), resolution=128)

# Export to GLB for use in any engine
alice_sdf.export_glb(vertices, indices, "bench.glb")
```

### Rust

```rust
use alice_sdf::io::{load_asdf_json, save_asdf};
use alice_sdf::mesh::sdf_to_mesh;

let tree = load_asdf_json("collections/pm-momuspark/Bench_01_Art.asdf.json")?;
let mesh = sdf_to_mesh(&tree.root, 128, (-5.0, -5.0, -5.0), (5.0, 5.0, 5.0));
```

### JavaScript / Three.js

```javascript
// Fetch the lightweight SDF definition
const resp = await fetch("collections/pm-momuspark/Bench_01_Art.asdf.json");
const sdf = await resp.json();

// Use with ALICE-SDF WASM module or server API
const mesh = await aliceSdf.toMesh(sdf, { resolution: 64 });
scene.add(mesh);
```

## Collections

All 18 collections from [Polygonal Mind](https://www.polygonalmind.com/) (CC0 licensed):

| Collection | Assets | Description |
|-----------|--------|-------------|
| **pm-momuspark** | 72 | Park environment — benches, fences, trees, paths |
| **pm-abm** | 54 | Blockchain museum — domes, panels, teleporters |
| **pm-aero-system** | 15 | Sci-fi floating transit — platforms, rails |
| **pm-avatar-garden** | 126 | Gauguin-inspired landscape — terrain, flora |
| **pm-avatar-show** | 48 | Interview space — furniture, plants, carpets |
| **pm-ca-world** | 68 | Classical mansion — architecture, decor |
| **pm-christmas** | 40 | Seasonal decorations — trees, gifts, snowmen |
| **pm-chromatic-chaos** | 56 | Vaporwave 80s — retro furniture, neon |
| **pm-cryptoavatars-retro-booth** | 54 | 80s virtual booth — Japanese street elements |
| **pm-crystal-crossroads** | 64 | Surrealist desert ruins — Moebius-inspired |
| **pm-lunar-year** | 52 | Asian Lunar New Year — lanterns, decorations |
| **pm-medieval-fair** | 35 | Medieval festival — food booths, structures |
| **pm-tomb-chaser-1** | 55 | Egyptian pyramid — platformer assets |
| **pm-tomb-chaser-2** | 50 | Neonwave Japanese pagoda — neon lights |
| **pm-towers** | 89 | Multi-tower art gallery |
| **pm-transit** | 23 | Retro-futuristic transport station |
| **pm-trash-polka** | 30 | Abstract graffiti — bold black and red |
| **pm-xyz** | 60 | Textured & rigged creatures |
| **Total** | **991** | |

## .asdf.json Format

Each file is a JSON document with this structure:

```json
{
  "version": "0.1.0",
  "root": {
    "SmoothUnion": {
      "a": { "Capsule": { "point_a": [0, 0, 0], "point_b": [1, 0, 0], "radius": 0.05 } },
      "b": { "Capsule": { "point_a": [0, 0, 0], "point_b": [0, 1, 0], "radius": 0.05 } },
      "k": 0.1
    }
  },
  "metadata": {
    "name": "Asset Name",
    "description": "3D model from collection",
    "author": "Polygonal Mind",
    "license": "CC0",
    "source": "opensource3dassets.com",
    "collection": "pm-momuspark",
    "os3a_id": "momuspark-001"
  }
}
```

SDF nodes are composable — you can combine assets using boolean operations:

```json
{
  "Subtraction": {
    "a": { "$ref": "building.asdf.json" },
    "b": { "$ref": "window_cutout.asdf.json" }
  }
}
```

## Conversion Method

Assets were converted using the **capsule approximation** strategy:

```
GLB → import_glb_bytes() → Mesh{vertices, indices}
    → mesh_to_sdf(capsule) → SdfNode tree
    → to_json() → .asdf.json
```

Each mesh edge becomes a `Capsule` SDF primitive, joined by `Union` operations into a binary tree. This preserves geometric fidelity but produces verbose node trees for complex meshes.

### Current Conversion Statistics

- **991 assets** converted with 100% success rate
- **Conversion strategy**: Capsule approximation (edge-to-capsule)
- **Average conversion time**: ~12ms per asset

### Roadmap: Compression Tiers

| Tier | Format | Method | Expected Size |
|------|--------|--------|---------------|
| **Tier 1** (current) | `.asdf.json` | Capsule approximation | Verbose (faithful) |
| **Tier 2** (planned) | `.asdf` | Binary (bincode + CRC32) | ~5-10x smaller |
| **Tier 3** (planned) | `.asdf.json` | LLM CSG fitting | **~100-1000 bytes** |
| **Tier 4** (planned) | `.asdf` | Neural SDF fitting | **~50-500 bytes** |

Tier 3-4 will achieve the "floppy disk" goal — all 991 assets in under 1MB.

## License

### Original Assets
All 3D assets in this repository are derived from [Open Source 3D Assets](https://www.opensource3dassets.com/) by [Polygonal Mind](https://www.polygonalmind.com/), released under **CC0 1.0 Universal (Public Domain)**. No attribution required.

### SDF Conversion
The SDF conversion layer and tooling are provided by [ALICE-SDF](https://github.com/ext-sakamoro/ALICE-SDF).

### This Repository
The converted `.asdf.json` files inherit the **CC0** license from the original assets. You may use them for any purpose — commercial, non-commercial, modified or unmodified — without restriction.

## Credits

- **Original 3D Assets**: [Polygonal Mind](https://www.polygonalmind.com/) via [Open Source 3D Assets](https://www.opensource3dassets.com/) (CC0)
- **OS3A Platform**: [ToxSam](https://github.com/ToxSam/os3a-gallery)
- **SDF Conversion**: [ALICE-SDF](https://github.com/ext-sakamoro/ALICE-SDF) by Moroya Sakamoto
- **GameMakers Article**: [1,000 free GLB assets](https://gamemakers.jp/article/2026_02_17_131473/)

## ALICE Ecosystem

This repository is part of the **ALICE** (Adaptive Lightweight Intelligent Compression Engine) ecosystem:

| Project | Description | Link |
|---------|-------------|------|
| **ALICE-SDF** | SDF engine powering this conversion | [GitHub](https://github.com/ext-sakamoro/ALICE-SDF) |
| **ALICE Ecosystem** | 52-component edge-to-cloud data pipeline | [GitHub](https://github.com/ext-sakamoro/ALICE-Eco-System) |
| **AI Modeler SaaS** | Browser-based 3D modeling with ALICE-SDF backend | [GitHub](https://github.com/ext-sakamoro/AI-Modeler-SaaS) |

## Contributing

Want to add more collections? The conversion pipeline is open:

```bash
# Clone ALICE-SDF
git clone https://github.com/ext-sakamoro/ALICE-SDF.git
cd ALICE-SDF

# Start the server
.venv/bin/python -m server.main

# Trigger conversion via API
curl -X POST http://localhost:8000/api/crawler/start

# Check progress
curl http://localhost:8000/api/crawler/status
```

Or convert individual GLB files:

```python
import alice_sdf

vertices, indices = alice_sdf.import_glb("your_model.glb")
node = alice_sdf.mesh_to_sdf(vertices, indices)
sdf_json = alice_sdf.to_json(node)
alice_sdf.save_sdf(node, "your_model.asdf.json")
```
