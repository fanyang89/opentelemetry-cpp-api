# OpenTelemetry C++ (API-only)

This repository is a physically slimmed version of OpenTelemetry C++ that only
contains the API headers and minimal CMake packaging for installation and
`find_package`/`pkg-config` integration.

## Build and install

```bash
cmake -S . -B build -DOPENTELEMETRY_INSTALL=ON
cmake --build build
cmake --install build --prefix /your/prefix
```

## Consume from another CMake project

```cmake
find_package(opentelemetry-cpp CONFIG REQUIRED COMPONENTS api)
target_link_libraries(your_target PRIVATE opentelemetry-cpp::api)
```

## pkg-config

```bash
pkg-config --cflags opentelemetry_api
```
