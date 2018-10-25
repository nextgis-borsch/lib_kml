################################################################################
# Project:  Lib KML
# Purpose:  CMake build scripts
# Author:   Dmitry Baryshnikov, dmitry.baryshnikov@nexgis.com
################################################################################
# Copyright (C) 2018, NextGIS <info@nextgis.com>
#
# Permission is hereby granted, free of charge, to any person obtaining a
# copy of this software and associated documentation files (the "Software"),
# to deal in the Software without restriction, including without limitation
# the rights to use, copy, modify, merge, publish, distribute, sublicense,
# and/or sell copies of the Software, and to permit persons to whom the
# Software is furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included
# in all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS
# OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL
# THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
# FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
# DEALINGS IN THE SOFTWARE.
################################################################################

set(LIBKML_DATA_DIR  ${CMAKE_SOURCE_DIR}/testdata CACHE "Directory containing test data" PATH)
add_definitions("-DDATADIR=\"${LIBKML_DATA_DIR}\"")

include_directories(
    ${PROJECT_SOURCE_DIR}/src
    ${PROJECT_SOURCE_DIR}/src/kml/base/contrib
    ${CMAKE_CURRENT_SOURCE_DIR}
)

set(WITH_GTest ON CACHE BOOL "Google test on")
set(WITH_GTest_EXTERNAL ON CACHE BOOL "Google test external on")
find_anyproject(GTest REQUIRED)

function(build_test)
  cmake_parse_arguments(TEST  "" "GROUP;NAME" "DEPENDS" ${ARGN} )
  add_executable(${TEST_GROUP}_${TEST_NAME}_test ${TEST_NAME}_test.cc)
  add_dependencies(${TEST_GROUP}_${TEST_NAME}_test ${TEST_DEPENDS})
  set(TEST_LINKS)
  foreach(TEST_D ${TEST_DEPENDS})
    get_target_property(LINK_PROP ${TEST_D} LINK_INTERFACE_LIBRARIES)
    if(LINK_PROP)
      list(APPEND TEST_LINKS ${LINK_PROP})
    endif()
  endforeach()
  target_link_libraries(${TEST_GROUP}_${TEST_NAME}_test ${TEST_LINKS} ${TEST_DEPENDS} ${GTEST_LIBRARY})
  set_target_properties(${TEST_GROUP}_${TEST_NAME}_test PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
  add_test(${TEST_GROUP}_${TEST_NAME} ${CMAKE_BINARY_DIR}/bin/${TEST_GROUP}_${TEST_NAME}_test)
endfunction(build_test)

add_subdirectory(kml)
