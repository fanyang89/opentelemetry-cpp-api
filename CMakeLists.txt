# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

cmake_minimum_required(VERSION 3.16)

set(OPENTELEMETRY_VERSION_NUMBER "1.25.0")
set(OPENTELEMETRY_VERSION_SUFFIX "-dev")
set(OPENTELEMETRY_VERSION
    "${OPENTELEMETRY_VERSION_NUMBER}${OPENTELEMETRY_VERSION_SUFFIX}")

set(OPENTELEMETRY_ABI_VERSION_DEFAULT "1")

project(
  opentelemetry-cpp
  VERSION "${OPENTELEMETRY_VERSION_NUMBER}"
  LANGUAGES CXX)

option(WITH_ABI_VERSION_1 "ABI version 1" ON)
option(WITH_ABI_VERSION_2 "EXPERIMENTAL: ABI version 2 preview" OFF)

if(WITH_ABI_VERSION_1 AND WITH_ABI_VERSION_2)
  message(
    FATAL_ERROR "Set either WITH_ABI_VERSION_1 or WITH_ABI_VERSION_2, not both")
endif()

if(WITH_ABI_VERSION_2)
  set(OPENTELEMETRY_ABI_VERSION_NO "2")
elseif(WITH_ABI_VERSION_1)
  set(OPENTELEMETRY_ABI_VERSION_NO "1")
else()
  set(OPENTELEMETRY_ABI_VERSION_NO "${OPENTELEMETRY_ABI_VERSION_DEFAULT}")
endif()

option(WITH_NO_DEPRECATED_CODE "Do not include deprecated code" OFF)

set(WITH_STL
    "OFF"
    CACHE STRING "Which version of the Standard Library for C++ to use")

option(WITH_GSL
       "Whether to use Guidelines Support Library for C++ latest features" OFF)

option(WITH_NO_GETENV "Whether the platform supports environment variables" OFF)

option(WITH_ASYNC_EXPORT_PREVIEW "Whether to enable async export" OFF)
option(WITH_METRICS_EXEMPLAR_PREVIEW
       "Whether to enable exemplar within metrics" OFF)
option(WITH_THREAD_INSTRUMENTATION_PREVIEW
       "Whether to enable thread instrumentation" OFF)
option(WITH_OTLP_RETRY_PREVIEW "Whether to enable experimental retry" OFF)
option(WITH_OTLP_GRPC_SSL_MTLS_PREVIEW
       "Whether to enable mTLS support for gRPC" OFF)
option(WITH_OTLP_GRPC_CREDENTIAL_PREVIEW
       "Whether to enable gRPC credentials option" OFF)
option(WITH_OTLP_HTTP_COMPRESSION
       "Whether to include gzip compression for OTLP HTTP" OFF)

if(WIN32)
  option(WITH_ETW "Whether to include ETW compile definitions" OFF)
else()
  if(DEFINED(WITH_ETW))
    message(FATAL_ERROR "WITH_ETW is only supported on Windows")
  endif()
endif()

set(OPENTELEMETRY_INSTALL_default ON)
if(NOT CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)
  set(OPENTELEMETRY_INSTALL_default OFF)
endif()
option(OPENTELEMETRY_INSTALL "Whether to install opentelemetry targets"
       ${OPENTELEMETRY_INSTALL_default})

include("${opentelemetry-cpp_SOURCE_DIR}/cmake/tools.cmake")
include(GNUInstallDirs)
include("${opentelemetry-cpp_SOURCE_DIR}/cmake/otel-install-functions.cmake")

if(WITH_GSL)
  include("${opentelemetry-cpp_SOURCE_DIR}/cmake/ms-gsl.cmake")
endif()

add_subdirectory(api)
include(CMakePackageConfigHelpers)

if(OPENTELEMETRY_INSTALL)
  otel_install_cmake_config()
  otel_install_components()
  otel_install_thirdparty_definitions()
endif()

set(OPENTELEMETRY_ABI_VERSION_NO
    "${OPENTELEMETRY_ABI_VERSION_NO}"
    CACHE STRING "opentelemetry-cpp ABI version" FORCE)
set(OPENTELEMETRY_VERSION
    "${OPENTELEMETRY_VERSION}"
    CACHE STRING "opentelemetry-cpp version" FORCE)
