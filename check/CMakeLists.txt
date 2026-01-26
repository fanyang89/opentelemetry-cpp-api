# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

cmake_minimum_required(VERSION 3.16)

project(opentelemetry_cpp_api_check LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

find_package(opentelemetry-cpp CONFIG REQUIRED COMPONENTS api)

set(check_source "${CMAKE_CURRENT_BINARY_DIR}/check_api.cc")
file(WRITE "${check_source}"
"// Copyright The OpenTelemetry Authors\n"
"// SPDX-License-Identifier: Apache-2.0\n\n"
"#include <opentelemetry/trace/provider.h>\n\n"
"int main()\n"
"{\n"
"  auto tracer = opentelemetry::trace::Provider::GetTracerProvider()->GetTracer(\n"
"      \"check\");\n"
"  (void)tracer;\n"
"  return 0;\n"
"}\n")

add_executable(opentelemetry_cpp_api_check "${check_source}")
target_link_libraries(opentelemetry_cpp_api_check PRIVATE opentelemetry-cpp::api)
