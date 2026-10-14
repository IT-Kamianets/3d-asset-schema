# IT-Kamianets 3D Engine: Asset Schema

`3d-asset-schema` defines the engine-independent description of 3D assets used across the **IT-Kamianets 3D Engine ecosystem**.

Where `3d-scene-schema` describes *where things are*, this schema describes *what things are*: models, materials, textures, and sounds.

### Logic

A scene object references an asset by id (`"asset": "props/crate-wood"`, since scene schema 1.1.0). This schema is what that id resolves to.

It must not depend on concepts specific to Unity or Unreal Engine. Engine packages such as `3d-unity-assets` read these definitions and decide how to load them at runtime. Files are standard formats every engine and the web can read: glTF/GLB for models, PNG/JPG for textures, WAV/OGG/MP3 for sound.

This allows:

**one asset definition → Unity / Unreal / Web / AI / external tools**

### Schema v1

Files live under [`schema/v1/`](schema/v1/), written as JSON Schema (2020-12), the same as `3d-scene-schema`.

* [`asset-library.schema.json`](schema/v1/asset-library.schema.json) — root document: `schemaVersion`, `libraryId`, `units`, and a list of `assets`. A library ships assets together, e.g. everything one level needs. A project can load several; ids must be unique across them.
* [`asset.schema.json`](schema/v1/asset.schema.json) — one asset:
  * `id`: stable, lowercase, path-like (`props/crate-wood`, `materials/wood`, `sounds/footstep-stone-1`);
  * `kind`: `model`, `material`, `texture`, or `audio`;
  * `source`: the file (`uri` relative to the library file or an absolute http(s) URL, and its `format`). Required for textures and audio; a model needs a source or a placeholder;
  * `material`: for kind `material`, a metallic/roughness surface like glTF's: `baseColor`, `baseColorTexture` (a texture asset id), `metallic`, `roughness`, `emission`, `transparent`;
  * `materials`: model only, material assets replacing the model's own, in order;
  * `bounds`: model only, the space it takes, for placement and placeholders before it loads;
  * `placeholder`: a box, sphere or capsule with a color, sized to the bounds; shown while the asset loads or when it cannot be loaded;
  * `metadata`: tags and free properties, e.g. `["footstep", "surface:stone"]` on a sound.
* [`common.schema.json`](schema/v1/common.schema.json) — `Vector3`, `Bounds3D`, `Color` (sRGB 0..1), `AssetId`, `Metadata` (the same shapes as the scene schema's).

**Conventions:** meters. A model's origin is at the bottom center of what it shows (so it stands on the point it is placed at), +Y up, and its front is glTF's front (+Z in the glTF file); placed in a scene, it faces the scene object's forward.

**Versioning:** every library carries a `schemaVersion` (semver). Breaking changes get a new `schema/v{n}/` directory.

Example: [`examples/props.library.example.json`](examples/props.library.example.json) with real files in [`examples/files/`](examples/files/): a 1 m crate (`crate.glb`), a wood texture (`wood.png`), a footstep (`footstep.wav`), plus a barrel that has only a placeholder.

### Status

Schema v1. The example library validates against it; invalid assets (a material without `material`, a texture without `source`, an id with spaces) are rejected. Read by `3d-unity-assets`.

### Roadmap

* Asset definition
* Asset IDs
* Model references (GLB/glTF)
* Materials
* Textures
* Asset metadata
* Tags and categories
* Bounds and pivot conventions
* Placeholder assets
* Schema validation
* Versioning
* Level of detail
* Audio settings (loop, default volume, 3D range)
* Lights as assets
* TypeScript models
* C# models (in `3d-unity-assets`)
* Stable Asset Schema v1
