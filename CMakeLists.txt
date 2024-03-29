cmake_minimum_required(VERSION 3.11 FATAL_ERROR)
project(goldfish VERSION 0.0.1 LANGUAGES CXX)

if("${CMAKE_BUILD_TYPE}" STREQUAL "Release")
	add_definitions(-DSPDLOG_ACTIVE_LEVEL=SPDLOG_LEVEL_INFO)
else()
	add_definitions(-DSPDLOG_ACTIVE_LEVEL=SPDLOG_LEVEL_DEBUG)
endif()

include(GNUInstallDirs)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake")

IF(APPLE)
    # Fix linking on 10.14+. See https://stackoverflow.com/questions/54068035
    LINK_DIRECTORIES(/usr/local/lib)
ENDIF()

add_executable(goldfish
	src/goldfish.cpp
)

target_include_directories(goldfish
	PUBLIC
		${CMAKE_CURRENT_SOURCE_DIR}/include
	PRIVATE
		${CMAKE_CURRENT_SOURCE_DIR}/src
)
target_compile_options(goldfish PRIVATE -Werror -Wall -Wextra -pedantic-errors)
target_compile_features(goldfish PUBLIC cxx_std_17)

set_target_properties(goldfish PROPERTIES
	OUTPUT_NAME "goldfish"
)

# MarlinPubsub
include("${CMAKE_CURRENT_LIST_DIR}/cmake/marlin-pubsub.cmake")
target_link_libraries(goldfish PUBLIC Marlin::pubsub)

# MarlinBeacon
include("${CMAKE_CURRENT_LIST_DIR}/cmake/marlin-beacon.cmake")
target_link_libraries(goldfish PUBLIC Marlin::beacon)

# MarlinNet
include("${CMAKE_CURRENT_LIST_DIR}/cmake/marlin-net.cmake")
target_link_libraries(goldfish PUBLIC Marlin::net)

# Spdlog
include("${CMAKE_CURRENT_LIST_DIR}/cmake/spdlog.cmake")
target_link_libraries(goldfish PUBLIC spdlog::spdlog)
