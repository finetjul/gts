cmake_minimum_required(VERSION 3.1)

add_executable(predicates_init src/predicates_init.c)

execute_process(
  COMMAND predicates_init.exe
  OUTPUT_FILE predicates_init.h
  OUTPUT_VARIABLE OUTVAR)

MESSAGE("${OUTVAR}")

set(kit gts)
project(${kit})

set(srcs
  src/bbtree.c
  src/boolean.c
  src/cdt.c
  src/container.c
  src/curvature.c
  src/edge.c
  src/eheap.c
  src/face.c
  src/fifo.c
  src/graph.c
  src/heap.c
  src/hsurface.c
  src/iso.c
  src/isotetra.c
  src/kdtree.c
  src/matrix.c
  src/misc.c
  src/named.c
  src/object.c
  src/oocs.c
  src/partition.c
  src/pgraph.c
  src/point.c
  src/predicates.c
  src/psurface.c
  src/refine.c
  src/segment.c
  src/split.c
  src/stripe.c
  src/surface.c
  src/triangle.c
  src/tribox3.c
  src/vertex.c
  src/vopt.c
  )

configure_file(src/config.h.win32 config.h)

SET(CMAKE_INCLUDE_CURRENT_DIR ON)
include_directories(
  ${CMAKE_CURRENT_SOURCE_DIR}/src
  )

find_library(glib_LIBRARY NAMES glib libglib glib-2.0 libglib-2.0
             HINTS ${glib_BINARY_DIR})

get_filename_component(glib_LIBRARY_DIR ${glib_LIBRARY} DIRECTORY)
get_filename_component(glib_LIBRARY_NAME ${glib_LIBRARY} NAME)
string(REGEX REPLACE "\\.[^.]*$" "" glib_LIBRARY_NAME ${glib_LIBRARY_NAME})

find_path(glib_H NAMES glib.h
          HINTS ${glib_LIBRARY_DIR}/../include)
get_filename_component(glib_INCLUDE_DIR ${glib_H} DIRECTORY)

set(glib_CONFIG_H)
find_path(glib_CONFIG_H NAMES glibconfig.h
          HINTS ${glib_LIBRARY_DIR}/${glib_LIBRARY_NAME}/include)

get_filename_component(glibconfig_INCLUDE_DIR ${glib_CONFIG_H} DIRECTORY)
include_directories(${glib_INCLUDE_DIR} ${glib_CONFIG_H})

add_definitions(/DGTS_COMPILATION)

add_library(${kit} SHARED ${srcs})
target_link_libraries(${kit} ${glib_LIBRARY})

if(MSVC)
  SET_TARGET_PROPERTIES(${kit} PROPERTIES LINK_FLAGS "/DEF:\"${CMAKE_CURRENT_SOURCE_DIR}/src/gts.def\"")
endif()

INSTALL(TARGETS ${kit}
  RUNTIME DESTINATION bin COMPONENT RuntimeLibraries
  LIBRARY DESTINATION lib COMPONENT RuntimeLibraries
  ARCHIVE DESTINATION lib COMPONENT Development
  )
INSTALL(FILES
  src/gts.h
  src/gtsconfig.h
  DESTINATION include COMPONENT Development
  )
