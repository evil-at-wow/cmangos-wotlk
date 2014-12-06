# This file is part of the CMaNGOS Project. See AUTHORS file for Copyright information
#
# This file is free software; as a special exception the author gives
# unlimited permission to copy and/or distribute it, with or without
# modifications, as long as this notice is preserved.
#
# This program is distributed in the hope that it will be useful, but
# WITHOUT ANY WARRANTY, to the extent permitted by law; without even the
# implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

cmake_minimum_required (VERSION 2.6)
project (MANGOS_VMAP_ASSEMB_IO)

set(CMAKE_VERBOSE_MAKEFILE true)

# uncomment next line to disable debug mode
ADD_DEFINITIONS("-DIOMAP_DEBUG")
ADD_DEFINITIONS("-DNO_CORE_FUNCS")

ADD_DEFINITIONS("-Wall")
ADD_DEFINITIONS("-ggdb")
ADD_DEFINITIONS("-O3")

# ACE libraries
unset(ACE_INCLUDE_DIR CACHE)
unset(ACE_LIBRARIES CACHE)
unset(ACE_LIBRARIES_DIR CACHE)
unset(ACE_INCLUDE_DIR)
unset(ACE_LIBRARIES)
unset(ACE_LIBRARIES_DIR)
if(ACE_USE_EXTERNAL)
  find_package(ACE)
  if(NOT ACE_FOUND)
    message(FATAL_ERROR
      "This project requires ACE installed when ACE_USE_EXTERNAL is set. Please download the ACE Micro Release Kit from http://download.dre.vanderbilt.edu/ and install it. If this script didn't find ACE and it was correctly installed please set ACE_ROOT to the correct path."
    )
  endif()
  if(EXISTS ${ACE_INCLUDE_DIR}/ace/Stack_Trace.h)
    set(HAVE_ACE_STACK_TRACE_H ON) # config.h.cmake
  endif()
else()
  include(../../cmake/ImportACE.cmake)

  add_subdirectory(../../dep/ACE_wrappers dep/ACE_wrappers)
endif()


include_directories(
    ../../src/shared
    ../../src/game/vmap
    ../../dep/include/g3dlite
    ../../src/framework
    ${ACE_INCLUDE_DIR}
    )

add_library(g3dlite ../../dep/src/g3dlite/AABox.cpp
    ../../dep/src/g3dlite/Box.cpp
    ../../dep/src/g3dlite/Crypto.cpp
    ../../dep/src/g3dlite/format.cpp
    ../../dep/src/g3dlite/Matrix3.cpp
    ../../dep/src/g3dlite/Plane.cpp
    ../../dep/src/g3dlite/System.cpp
    ../../dep/src/g3dlite/Triangle.cpp
    ../../dep/src/g3dlite/Vector3.cpp
    ../../dep/src/g3dlite/Vector4.cpp
    ../../dep/src/g3dlite/debugAssert.cpp
    ../../dep/src/g3dlite/fileutils.cpp
    ../../dep/src/g3dlite/g3dmath.cpp
    ../../dep/src/g3dlite/g3dfnmatch.cpp
    ../../dep/src/g3dlite/prompt.cpp
    ../../dep/src/g3dlite/stringutils.cpp
    ../../dep/src/g3dlite/Any.cpp
    ../../dep/src/g3dlite/BinaryFormat.cpp
    ../../dep/src/g3dlite/BinaryInput.cpp
    ../../dep/src/g3dlite/BinaryOutput.cpp
    ../../dep/src/g3dlite/Capsule.cpp
    ../../dep/src/g3dlite/CollisionDetection.cpp
    ../../dep/src/g3dlite/CoordinateFrame.cpp
    ../../dep/src/g3dlite/Cylinder.cpp
    ../../dep/src/g3dlite/Line.cpp
    ../../dep/src/g3dlite/LineSegment.cpp
    ../../dep/src/g3dlite/Log.cpp
    ../../dep/src/g3dlite/Matrix4.cpp
    ../../dep/src/g3dlite/MemoryManager.cpp
    ../../dep/src/g3dlite/Quat.cpp
    ../../dep/src/g3dlite/Random.cpp
    ../../dep/src/g3dlite/Ray.cpp
    ../../dep/src/g3dlite/ReferenceCount.cpp
    ../../dep/src/g3dlite/Sphere.cpp
    ../../dep/src/g3dlite/TextInput.cpp
    ../../dep/src/g3dlite/TextOutput.cpp
    ../../dep/src/g3dlite/UprightFrame.cpp
    ../../dep/src/g3dlite/Vector2.cpp
    ../../dep/src/g3dlite/FileSystem.cpp
    ../../dep/src/g3dlite/uint128.cpp
    ../../dep/src/g3dlite/PhysicsFrame.cpp
    )

add_library(vmap
    ../../src/game/vmap/BIH.cpp
    ../../src/game/vmap/VMapManager2.cpp
    ../../src/game/vmap/MapTree.cpp
    ../../src/game/vmap/TileAssembler.cpp
    ../../src/game/vmap/WorldModel.cpp
    ../../src/game/vmap/ModelInstance.cpp
    )

# The sources in src/game need ACE headers, so make sure that ACE is built first!
if(NOT ACE_USE_EXTERNAL)
  add_dependencies( vmap ACE_Project )
endif()

target_link_libraries(vmap g3dlite z)

add_executable(vmap_assembler vmap_assembler.cpp)
target_link_libraries(vmap_assembler vmap ${ACE_LIBRARIES})

# add_executable(vmap_test coordinate_test.cpp)
# target_link_libraries(vmap_test vmap)
