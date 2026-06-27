# TestDemo

Qt CMake standard project template.

## Directory Layout

- `src/`: project source code
- `third/`: third-party libraries
- `temp/<Config>/<Arch>/`: CMake build tree and intermediate files
- `build/<Config>/<Arch>/`: final runtime/library/debug outputs

## Build

Configure and build with CMake presets:

```powershell
cmake --preset debug-x64
cmake --build --preset debug-x64
```

Other presets:

- `debug-x86`
- `debug-x64`
- `release-x86`
- `release-x64`

