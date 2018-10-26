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

if(WIN32)
    # Only cmake >= 3.4
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
endif()

## Dependencies
find_anyproject(ZLIB REQUIRED)
find_anyproject(UriParser REQUIRED CMAKE_ARGS -DSKIP_BUILD_APPS=ON)
find_anyproject(EXPAT REQUIRED CMAKE_ARGS -DBUILD_tools=OFF)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/base/contrib)

### Base ###
file(GLOB BASE_SRCS "${CMAKE_CURRENT_SOURCE_DIR}/base/*.cc"
    base/contrib/minizip/unzip.c
    base/contrib/minizip/iomem_simple.c
    base/contrib/minizip/ioapi.c
    base/contrib/minizip/zip.c
)


if(WIN32)
    add_definitions(-Dtzname=_tzname)
    list(APPEND BASE_SRCS base/contrib/strptime.c)
    list(REMOVE_ITEM BASE_SRCS "${CMAKE_CURRENT_SOURCE_DIR}/base/file_posix.cc")
    if(MINGW)
        #use unicode functions from the windows api
        add_definitions(-DUNICODE -D_UNICODE)
        #supress long-long warning for mingw
        set_source_files_properties(${CMAKE_CURRENT_SOURCE_DIR}/base/time_util.cc COMPILE_FLAGS "-Wno-long-long")
    endif()
else()
    list(REMOVE_ITEM BASE_SRCS "${CMAKE_CURRENT_SOURCE_DIR}/base/file_win32.cc")
    #supress long-long warning for linux
    set_source_files_properties(${CMAKE_CURRENT_SOURCE_DIR}/base/zip_file.cc COMPILE_FLAGS "-Wno-long-long")
endif()

set(BASE_INCS
    base/attributes.h
    base/csv_splitter.h
    base/date_time.h
    base/color32.h
    base/expat_handler.h
    base/expat_handler_ns.h
    base/expat_parser.h
    base/file.h
    base/math_util.h
    base/memory_file.h
    base/mimetypes.h
    base/net_cache.h
    base/referent.h
    base/string_util.h
    base/tempfile.h
    base/time_util.h
    base/util.h
    base/vec3.h
    base/version.h
    base/xml_element.h
    base/xml_file.h
    base/xml_namespaces.h
    base/xmlns.h
    base/zip_file.h)

### CONVENIENCE ###
file(GLOB CONVENIENCE_SRCS "${CMAKE_CURRENT_SOURCE_DIR}/convenience/*.cc" )
file(GLOB CONVENIENCE_INCS "${CMAKE_CURRENT_SOURCE_DIR}/convenience/*.h")

### DOM ###

file(GLOB DOM_SRCS "${CMAKE_CURRENT_SOURCE_DIR}/dom/*.cc" )
list(REMOVE_ITEM DOM_SRCS "${CMAKE_CURRENT_SOURCE_DIR}/dom/kml22.cc")

set(DOM_INCS
    dom/abstractlatlonbox.h
    dom/abstractview.h
    dom/atom.h
    dom/balloonstyle.h
    dom/colorstyle.h
    dom/container.h
    dom/document.h
    dom/element.h
    dom/extendeddata.h
    dom/feature.h
    dom/folder.h
    dom/geometry.h
    dom/hotspot.h
    dom/iconstyle.h
    dom/kml.h
    dom/kml22.h
    # dom/kml22.cc
    dom/kml_cast.h
    dom/kml_factory.h
    dom/kml_funcs.h
    dom/kml_ptr.h
    dom/kmldom.h
    dom/labelstyle.h
    dom/linestyle.h
    dom/link.h
    dom/liststyle.h
    dom/model.h
    dom/networklink.h
    dom/networklinkcontrol.h
    dom/object.h
    dom/overlay.h
    dom/parser.h
    dom/parser_observer.h
    dom/placemark.h
    dom/polystyle.h
    dom/region.h
    dom/schema.h
    dom/snippet.h
    dom/style.h
    dom/stylemap.h
    dom/styleselector.h
    dom/substyle.h
    dom/timeprimitive.h
    dom/gx_timeprimitive.h
    dom/gx_tour.h
    dom/vec2.h
    dom/xal.h
    dom/xsd.h
    dom/visitor.h
    dom/visitor_driver.h)

### ENGINE ###

file(GLOB ENGINE_SRCS "${CMAKE_CURRENT_SOURCE_DIR}/engine/*.cc" )

set(ENGINE_INCS
    engine/bbox.h
    engine/clone.h
    engine/engine_types.h
    engine/entity_mapper.h
    engine/feature_balloon.h
    engine/feature_view.h
    engine/feature_visitor.h
    engine/find.h
    engine/find_xml_namespaces.h
    engine/get_link_parents.h
    engine/get_links.h
    engine/href.h
    engine/id_mapper.h
    engine/kml_cache.h
    engine/kml_file.h
    engine/kml_stream.h
    engine/kml_uri.h
    engine/kmz_cache.h
    engine/kmz_file.h
    engine/link_util.h
    engine/location_util.h
    engine/merge.h
    engine/object_id_parser_observer.h
    engine/old_schema_parser_observer.h
    engine/parse_old_schema.h
    engine/schema_parser_observer.h
    engine/shared_style_parser_observer.h
    engine/style_inliner.h
    engine/style_merger.h
    engine/style_resolver.h
    engine/style_splitter.h
    engine/update.h)

### REGIONATOR ###

file(GLOB REGIONATOR_SRCS "${CMAKE_CURRENT_SOURCE_DIR}/regionator/*.cc" )
file(GLOB REGIONATOR_INCS "${CMAKE_CURRENT_SOURCE_DIR}/regionator/*.h")

### XSD ###

file(GLOB XSD_SRCS "${CMAKE_CURRENT_SOURCE_DIR}/xsd/*.cc" )
file(GLOB XSD_INCS "${CMAKE_CURRENT_SOURCE_DIR}/xsd/*.h")

set(ROOT_HEADERS dom.h engine.h)

set(CSOURCES ${BASE_SRCS} ${CONVENIENCE_SRCS} ${DOM_SRCS} ${ENGINE_SRCS} ${REGIONATOR_SRCS} ${XSD_SRCS})
set(HHEADERS ${BASE_INCS} ${CONVENIENCE_INCS} ${DOM_INCS} ${ENGINE_INCS} ${REGIONATOR_INCS} ${XSD_INCS} ${ROOT_HEADERS})

if(BUILD_SHARED_LIBS)
    add_library(${LIB_NAME} SHARED ${CSOURCES} ${HHEADERS})
    set_target_properties (${LIB_NAME} PROPERTIES
        VERSION ${VERSION}
        SOVERSION ${VERSION_MAJOR} )
elseif(OSX_FRAMEWORK)
    set_property(SOURCE ${ROOT_HEADERS} PROPERTY MACOSX_PACKAGE_LOCATION "Headers/kml")
    set_property(SOURCE ${BASE_INCS} PROPERTY MACOSX_PACKAGE_LOCATION "Headers/kml/base")
    set_property(SOURCE ${CONVENIENCE_INCS} PROPERTY MACOSX_PACKAGE_LOCATION "Headers/kml/convenience")
    set_property(SOURCE ${DOM_INCS} PROPERTY MACOSX_PACKAGE_LOCATION "Headers/kml/dom")
    set_property(SOURCE ${ENGINE_INCS} PROPERTY MACOSX_PACKAGE_LOCATION "Headers/kml/engine")
    set_property(SOURCE ${REGIONATOR_INCS} PROPERTY MACOSX_PACKAGE_LOCATION "Headers/kml/regionator")
    set_property(SOURCE ${XSD_INCS} PROPERTY MACOSX_PACKAGE_LOCATION "Headers/kml/xsd")
    add_library(${LIB_NAME} SHARED ${CSOURCES} ${HHEADERS})
    set_target_properties(${LIB_NAME} PROPERTIES
        FRAMEWORK TRUE
        FRAMEWORK_VERSION ${FRAMEWORK_VERSION}
        MACOSX_FRAMEWORK_SHORT_VERSION_STRING ${VERSION}
        MACOSX_FRAMEWORK_BUNDLE_VERSION ${VERSION}
        MACOSX_FRAMEWORK_IDENTIFIER org.libkml.libkml
        XCODE_ATTRIBUTE_INSTALL_PATH "@rpath"
        VERSION ${VERSION}
        SOVERSION ${VERSION_MAJOR} )
else()
    add_library(${LIB_NAME} STATIC ${CSOURCES} ${HHEADERS})
endif()

target_link_libraries(${LIB_NAME} PRIVATE ${EXPAT_LIBRARIES} ${URIPARSER_LIBRARIES} ${ZLIB_LIBRARIES})

set(PACKAGE_NAME ${PROJECT_NAME})
string(TOUPPER ${PACKAGE_NAME} PACKAGE_UPPER_NAME)

if(NOT SKIP_INSTALL_LIBRARIES AND NOT SKIP_INSTALL_ALL)
    install(TARGETS ${LIB_NAME}
        EXPORT ${PACKAGE_UPPER_NAME}Targets
        RUNTIME DESTINATION ${INSTALL_BIN_DIR}
        LIBRARY DESTINATION ${INSTALL_LIB_DIR}
        ARCHIVE DESTINATION ${INSTALL_LIB_DIR}
        INCLUDES DESTINATION ${INSTALL_INC_DIR}
        FRAMEWORK DESTINATION ${INSTALL_LIB_DIR}
    )
endif()

if(NOT SKIP_INSTALL_HEADERS AND NOT SKIP_INSTALL_ALL)
    install(FILES ${ROOT_HEADERS} DESTINATION ${INSTALL_INC_DIR}/kml COMPONENT Development)
    install(FILES ${BASE_INCS} DESTINATION ${INSTALL_INC_DIR}/kml/base COMPONENT Development)
    install(FILES ${CONVENIENCE_INCS} DESTINATION ${INSTALL_INC_DIR}/kml/convenience COMPONENT Development)
    install(FILES ${DOM_INCS} DESTINATION ${INSTALL_INC_DIR}/kml/dom COMPONENT Development)
    install(FILES ${ENGINE_INCS} DESTINATION ${INSTALL_INC_DIR}/kml/engine COMPONENT Development)
    install(FILES ${REGIONATOR_INCS} DESTINATION ${INSTALL_INC_DIR}/kml/regionator COMPONENT Development)
    install(FILES ${XSD_INCS} DESTINATION ${INSTALL_INC_DIR}/kml/xsd COMPONENT Development)
endif()

# Export package ===============================================================
# Add path to includes to build-tree export
target_include_directories(${LIB_NAME} INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
)

# Add all targets to the build-tree export set
export(TARGETS ${LIB_NAME}
    FILE ${PROJECT_BINARY_DIR}/${PACKAGE_UPPER_NAME}Targets.cmake)

# Create the <Package>Config.cmake file
configure_file(${PROJECT_SOURCE_DIR}/cmake/PackageConfig.cmake.in
    ${PROJECT_BINARY_DIR}/${PACKAGE_UPPER_NAME}Config.cmake @ONLY)

if(NOT SKIP_INSTALL_LIBRARIES AND NOT SKIP_INSTALL_ALL)
    # Install the <Package>Config.cmake
    install(FILES
      ${PROJECT_BINARY_DIR}/${PACKAGE_UPPER_NAME}Config.cmake
      DESTINATION ${INSTALL_CMAKECONF_DIR} COMPONENT dev)

    # Install the export set for use with the install-tree
    install(EXPORT ${PACKAGE_UPPER_NAME}Targets DESTINATION ${INSTALL_CMAKECONF_DIR} COMPONENT dev)
endif()
