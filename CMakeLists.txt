cmake_minimum_required(VERSION 3.1)

add_executable(predicates_init src/predicates_init.c)

execute_process(
  COMMAND predicates_init
  OUTPUT_FILE predicates_init.h
  OUTPUT_VARIABLE OUTVAR)

MESSAGE("${OUTVAR}")

set(kit gts)
project(${kit})

if (NOT WIN32)
  set(CPP_HAS_WARNING 1)
  set(HAVE_DLFCN_H 1)
  set(HAVE_FLOATINGPOINT_H 1)
  set(HAVE_FPU_CONTROL_H 1)
  set(HAVE_GETOPT_H 1)
  set(HAVE_GETOPT_LONG 1)
  set(HAVE_INTTYPES_H 1)
  set(HAVE_MEMORY_H 1)
  set(HAVE_STDINT_H 1)
  set(HAVE_STDLIB_H 1)
  set(HAVE_STRINGS_H 1)
  set(HAVE_STRING_H 1)
  set(HAVE_SYS_STAT_H 1)
  set(HAVE_SYS_TYPES_H 1)
  set(HAVE_UNISTD_H 1)
  set(PACKAGE_BUGREPORT 0)
  set(PACKAGE_NAME "gts")
  set(PACKAGE_STRING "0.7.6")
  set(PACKAGE_TARNAME "gts")
  set(PACKAGE_URL "")
  set(PACKAGE_VERSION "0.7.6")
  set(STDC_HEADERS)
endif()

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

if (WIN32)
configure_file(src/config.h.win32 config.h)
else()
configure_file(config.h.in config.h @ONLY)
endif()

SET(CMAKE_INCLUDE_CURRENT_DIR ON)
include_directories(
  ${CMAKE_CURRENT_SOURCE_DIR}/src
  )

find_package(PkgConfig REQUIRED)
pkg_check_modules(GLIB2 glib-2.0 IMPORTED_TARGET)

include_directories(${GLIB2_INCLUDE_DIRS})

if (NOT WIN32 AND BUILD_SHARED_LIBS)
add_definitions(/DGTS_COMPILATION)
endif()

add_library(${kit} SHARED ${srcs})
target_include_directories(${kit} PUBLIC ${GLIB2_INCLUDE_DIRS})
target_link_libraries(${kit} PkgConfig::GLIB2)

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

set(prefix ${CMAKE_INSTALL_PREFIX})
set(exec_prefix ${CMAKE_INSTALL_PREFIX})
set(libdir ${CMAKE_INSTALL_PREFIX}/lib)
set(includedir ${CMAKE_INSTALL_PREFIX}/include)

configure_file("${CMAKE_CURRENT_SOURCE_DIR}/gts.pc.in"  "${CMAKE_CURRENT_BINARY_DIR}/gts.pc" @ONLY)
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/gts.pc" DESTINATION lib/pkgconfig)
