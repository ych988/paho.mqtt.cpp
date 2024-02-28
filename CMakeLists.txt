# CMakeLists.txt
#
# Top-level CMake file for the Paho C++ library.
#
#*******************************************************************************
# This is part of the Paho MQTT C++ client library.
#
# Copyright (c) 2016-2017, Guilherme Maciel Ferreira
# Copyright (c) 2017-2023, Frank Pagliughi
#
# All rights reserved. This program and the accompanying materials
# are made available under the terms of the Eclipse Public License v2.0
# and Eclipse Distribution License v1.0 which accompany this distribution.
# 
# The Eclipse Public License is available at
#   http://www.eclipse.org/legal/epl-v20.html
# and the Eclipse Distribution License is available at
#   http://www.eclipse.org/org/documents/edl-v10.php.
# 
# Contributors:
#   Guilherme Maciel Ferreira - initial version
#   Frank Pagliughi
#*******************************************************************************/

cmake_minimum_required(VERSION 3.5)

project(PahoMqttCpp VERSION "1.4.0.1")

## --- Build options ---

if(WIN32)
  option(PAHO_BUILD_STATIC "Build static library" TRUE)
  option(PAHO_BUILD_SHARED "Build shared library (DLL)" FALSE)
  option(PAHO_WITH_SSL "Build SSL-enabled library" FALSE)
else()
  option(PAHO_BUILD_STATIC "Build static library" FALSE)
  option(PAHO_BUILD_SHARED "Build shared library" TRUE)
  option(PAHO_WITH_SSL "Build SSL-enabled library" FALSE)
  option(PAHO_BUILD_DEB_PACKAGE "Build debian package" FALSE)
endif()

if(NOT PAHO_BUILD_SHARED AND NOT PAHO_BUILD_STATIC)
  message(FATAL_ERROR "You must set either PAHO_BUILD_SHARED, PAHO_BUILD_STATIC, or both")
endif()

if(UNIX)
  LINK_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/../3rdpart/edgex/3rdpart/lib_unix)
elseif(WIN32)
  LINK_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/../3rdpart/edgex/3rdpart/lib)
endif ()

if(PAHO_WITH_SSL)
  #find_package(OpenSSL REQUIRED)
  #set(PAHO_MQTT_C_LIB paho-mqtt3as)
  message(STATUS "MQTT加密版本未实现")
else()
  #set(PAHO_MQTT_C_LIB eclipse-paho-mqtt-c::paho-mqtt3a)
  if(UNIX)
    set (PAHO_MQTT_C_LIB paho-mqtt3a)
  elseif(WIN32)
    set (PAHO_MQTT_C_LIB libpaho-mqtt3a.dll.a)
  endif ()
endif()

## --- C++11 build flags ---

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Generate position-independent code (-fPIC on UNIX)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

if(WIN32)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
    set(LIBS_SYSTEM ws2_32)
endif()

## --- Build directories ---

# For the paho_mqtt_c module
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/../../3rdpart/edgex/3rdpart/include/paho)

add_subdirectory(src)