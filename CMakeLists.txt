# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# THE MESOS PROJECT.
####################
if (NOT WIN32)
  cmake_minimum_required(VERSION 2.8.10)
else (NOT WIN32)
  cmake_minimum_required(VERSION 3.6.0)
endif (NOT WIN32)

project(Mesos)
set(MESOS_MAJOR_VERSION 1)
set(MESOS_MINOR_VERSION 3)
set(MESOS_PATCH_VERSION 0)
set(PACKAGE_VERSION
  ${MESOS_MAJOR_VERSION}.${MESOS_MINOR_VERSION}.${MESOS_PATCH_VERSION})

set(MESOS_PACKAGE_VERSION ${PACKAGE_VERSION})
set(MESOS_PACKAGE_SOVERSION 0)


# CMAKE MODULE SETUP.
#####################
# Paths that are searched when `include(...)` is called.
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/3rdparty/cmake)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/3rdparty/libprocess/cmake)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/3rdparty/stout/cmake)
list(
  APPEND
  CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/3rdparty/libprocess/cmake/macros)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/src/cmake)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/src/examples/cmake)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/src/master/cmake)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/src/slave/cmake)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/src/tests/cmake)

# Macros.
include(Common)
include(External)
include(PatchCommand)
include(Versions)
include(VsBuildCommand)

# Configuration.
include(MesosConfigure)


# SUBDIRECTORIES.
#################
add_subdirectory(3rdparty)
add_subdirectory(src)


# TESTS.
########
add_custom_target(
  check ${STOUT_TESTS_TARGET}
  COMMAND ${PROCESS_TESTS_TARGET}
  COMMAND ${MESOS_TESTS_TARGET}
  DEPENDS ${STOUT_TESTS_TARGET} ${PROCESS_TESTS_TARGET} ${MESOS_TESTS_TARGET}
  )

add_custom_target(
  tests
  DEPENDS ${STOUT_TESTS_TARGET} ${PROCESS_TESTS_TARGET} ${MESOS_TESTS_TARGET}
  )

# PACKAGING.
############
# List of sources we will ignore when generating a source package.
# We assume there are no artifacts from an autotools build.
#
# NOTE: An in-source build is not supported. The build directory
# must be different than the source directory.
set(CPACK_SOURCE_IGNORE_FILES
  .clang-format;
  .gitignore;
  .reviewboardrc;
  bootstrap.bat;
  bootstrap;
  configure.ac;
  Doxyfile;
  mesos.pc.in;
  /.git/;
  /docs/;
  /m4/;
  /mpi/;
  /site/;
  /support/;
  ${CMAKE_BINARY_DIR};
  )

# Convert the ignore list to a string (as required by CPack).
set(CPACK_SOURCE_IGNORE_FILES "${CPACK_SOURCE_IGNORE_FILES}")

# Set the basename of any packages to 'mesos-<version>'.
set(CPACK_PACKAGE_FILE_NAME "mesos-${MESOS_PACKAGE_VERSION}")
set(CPACK_SOURCE_PACKAGE_FILE_NAME ${CPACK_PACKAGE_FILE_NAME})

# By default, all forms of binary packages are disabled.
option(CPACK_BINARY_BUNDLE       "Enable to build OSX bundles"              OFF)
option(CPACK_BINARY_DEB          "Enable to build Debian packages"          OFF)
option(CPACK_BINARY_DRAGNDROP    "Enable to build OSX Drag'n'Drop packages" OFF)
option(CPACK_BINARY_IFW          "Enable to build IFW packages"             OFF)
option(CPACK_BINARY_NSIS         "Enable to build NSIS packages"            OFF)
option(CPACK_BINARY_OSXX11       "Enable to build OSX X11 packages"         OFF)
option(CPACK_BINARY_PACKAGEMAKER "Enable to build PackageMaker packages"    OFF)
option(CPACK_BINARY_RPM          "Enable to build RPM packages"             OFF)
option(CPACK_BINARY_STGZ         "Enable to build STGZ packages"            OFF)
option(CPACK_BINARY_TBZ2         "Enable to build TBZ2 packages"            OFF)
option(CPACK_BINARY_TGZ          "Enable to build TGZ packages"             OFF)
option(CPACK_BINARY_TXZ          "Enable to build TXZ packages"             OFF)

# Output a gzip'd tarball for the `package_source` target.
set(CPACK_SOURCE_TGZ ON)

# By default, other forms of source packages are disabled.
option(CPACK_SOURCE_TBZ2 "Enable to build TBZ2 source packages" OFF)
option(CPACK_SOURCE_TXZ  "Enable to build TXZ source packages"  OFF)
option(CPACK_SOURCE_TZ   "Enable to build TZ source packages"   OFF)
option(CPACK_SOURCE_ZIP  "Enable to build ZIP source packages"  OFF)

include(CPack)
