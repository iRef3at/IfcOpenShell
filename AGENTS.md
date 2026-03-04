# IfcOpenShell — Codex Project Context

## What is this project?
IfcOpenShell is an open source (LGPL) C++ library and geometry engine for working with
Industry Foundation Classes (IFC) — the standard file format for Building Information Modelling (BIM).

## Repository layout
```
├── cmake/              ← CMakeLists.txt lives HERE (not root)
│   └── CMakeLists.txt
├── src/
│   ├── ifcparse/       ← IFC file parser (core)
│   ├── ifcgeom/        ← Geometry processing (uses OpenCASCADE)
│   ├── ifcconvert/     ← CLI converter tool
│   └── ifcwrap/        ← Python/SWIG bindings (disabled in our build)
├── win/                ← Official Windows build scripts (not used by CI)
├── nix/                ← Linux build helper scripts
├── .github/workflows/
│   ├── build-windows-x64.yml   ← Our C++ Windows build (vcpkg-based)
│   └── codex-autofix.yml       ← This auto-fix workflow
├── .github/codex/prompts/
│   └── fix-build.md            ← Prompt for build-fix tasks
└── AGENTS.md                   ← This file
```

## Build configuration (Windows x64)
- **Runner:** windows-2022 (GitHub-hosted)
- **Compiler:** MSVC / Visual Studio 17 2022, x64 architecture
- **Build type:** Release
- **Dependency manager:** vcpkg (pre-installed at C:\vcpkg)
- **CMake source dir:** `cmake/` (not root!)

### Dependencies (via vcpkg x64-windows)
| Package            | Purpose                              |
|--------------------|--------------------------------------|
| opencascade        | OCCT geometry kernel (the big one)   |
| boost-*            | system, regex, filesystem, thread... |
| eigen3             | Linear algebra                       |
| cgal               | Computational geometry               |
| nlohmann-json      | JSON support                         |
| libxml2            | IFCXML parsing                       |
| hdf5               | Geometry caching                     |

### Disabled features
- `BUILD_IFCPYTHON=OFF` — no Python/SWIG
- `COLLADA_SUPPORT=OFF` — no OpenCOLLADA
- `BUILD_EXAMPLES=OFF`

## Common build issues & fixes
- **CMake can't find OCCT:** Check `OCC_INCLUDE_DIR` / `OCC_LIBRARY_DIR` point to vcpkg installed paths
- **Boost not found:** Ensure all required boost-* sub-packages are installed via vcpkg
- **CGAL needs GMP/MPFR:** vcpkg installs these as CGAL dependencies, but paths may need explicit `-D` flags
- **HDF5 link errors:** Make sure `HDF5_SUPPORT=ON` and paths are correct
- **IFC schema version:** Default builds all schemas; can limit with `set(SCHEMA_VERSIONS "2x3" "4")` in CMakeLists.txt

## Conventions
- Prefer fixing build configuration (yml / cmake) over patching source code
- Keep changes minimal — one fix per problem
- Always test that CMake configure + build still works after changes
- Update this AGENTS.md if you discover new build patterns or issues
