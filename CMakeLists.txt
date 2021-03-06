cmake_minimum_required(VERSION 2.6)

project(gpr)

if(CMAKE_COMPILER_IS_GNUCXX OR ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang"))
	SET(EXTRA_CXX_COMPILE_FLAGS "-std=c++11 -I./src -I./test -Werror -Wall")

	SET(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} ${EXTRA_CXX_COMPILE_FLAGS}")
endif(CMAKE_COMPILER_IS_GNUCXX OR ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang"))

INCLUDE_DIRECTORIES(./src/)

SET(GPR_HEADERS src/gcode_program.h
		src/parser.h)

SET(GPR_CPPS src/parser.cpp
	     src/gcode_program.cpp)

set(CMAKE_MACOSX_RPATH 1)

add_library(gpr STATIC ${GPR_CPPS})

SET(TEST_FILES test/parser_tests.cpp
	       test/main.cpp)

add_executable(all-tests ${TEST_FILES})
target_link_libraries(all-tests gpr)

add_executable(parse-gcode src/main.cpp)
target_link_libraries(parse-gcode gpr)

install(
  TARGETS gpr EXPORT gpr-targets 
  LIBRARY DESTINATION lib)

install(
  FILES 
    src/parser.h
    src/gcode_program.h
  DESTINATION
    include/gpr)

export(EXPORT gpr-targets
        FILE "${CMAKE_CURRENT_BINARY_DIR}/gpr/gprTargets.cmake"
        NAMESPACE gpr::
        )

install(EXPORT gpr-targets
        FILE
        gprTargets.cmake
        NAMESPACE
        gpr::
        DESTINATION
        lib/cmake/gpr
        )

configure_file(gprConfig.cmake
        "${CMAKE_CURRENT_BINARY_DIR}/gpr/gprConfig.cmake"
        COPYONLY
        )

install(
        FILES
        ${CMAKE_CURRENT_BINARY_DIR}/gpr/gprConfig.cmake
        DESTINATION lib/cmake/gpr
)

