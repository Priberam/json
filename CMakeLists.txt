cmake_minimum_required(VERSION 3.0)

# define the project
project(json VERSION 2.0.0 LANGUAGES CXX)

# define project variables
set(JSON_TARGET_NAME ${PROJECT_NAME})
set(JSON_UNITTEST_TARGET_NAME "json_unit")
set(JSON_PACKAGE_NAME ${JSON_TARGET_NAME})
set(JSON_TARGETS_FILENAME "${JSON_PACKAGE_NAME}Targets.cmake")
set(JSON_CONFIG_FILENAME "${JSON_PACKAGE_NAME}Config.cmake")
set(JSON_CONFIGVERSION_FILENAME "${JSON_PACKAGE_NAME}ConfigVersion.cmake")
set(JSON_CONFIG_DESTINATION "cmake")
set(JSON_INCLUDE_DESTINATION "include/nlohmann")

# create and configure the library target
add_library(${JSON_TARGET_NAME} INTERFACE)
target_include_directories(${JSON_TARGET_NAME} INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
  $<INSTALL_INTERFACE:${JSON_INCLUDE_DESTINATION}>)

# create and configure the unit test target
add_executable(${JSON_UNITTEST_TARGET_NAME}
    "test/catch.hpp" "test/unit.cpp")
set_target_properties(${JSON_UNITTEST_TARGET_NAME} PROPERTIES
    CXX_STANDARD 11
    CXX_STANDARD_REQUIRED ON
    COMPILE_DEFINITIONS "$<$<CXX_COMPILER_ID:MSVC>:_SCL_SECURE_NO_WARNINGS>"
    COMPILE_OPTIONS "$<$<CXX_COMPILER_ID:MSVC>:/EHsc;$<$<CONFIG:Release>:/Od>>")
target_include_directories(${JSON_UNITTEST_TARGET_NAME} PRIVATE "test")
target_link_libraries(${JSON_UNITTEST_TARGET_NAME} ${JSON_TARGET_NAME})

# generate a config and config version file for the package
include(CMakePackageConfigHelpers)
configure_package_config_file("cmake/config.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/${JSON_CONFIG_FILENAME}"
    INSTALL_DESTINATION ${JSON_CONFIG_DESTINATION})
write_basic_package_version_file("${CMAKE_CURRENT_BINARY_DIR}/${JSON_CONFIGVERSION_FILENAME}"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion)

# export the library target and store build directory in package registry
export(TARGETS ${JSON_TARGET_NAME}
	FILE "${CMAKE_CURRENT_BINARY_DIR}/${JSON_TARGETS_FILENAME}")
export(PACKAGE ${JSON_PACKAGE_NAME})

# install library target and config files
install(TARGETS ${JSON_TARGET_NAME}
	EXPORT ${JSON_PACKAGE_NAME})
install(FILES "src/json.hpp"
	DESTINATION ${JSON_INCLUDE_DESTINATION})
install(EXPORT ${JSON_PACKAGE_NAME}
	FILE ${JSON_TARGETS_FILENAME}
	DESTINATION ${JSON_CONFIG_DESTINATION})
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${JSON_CONFIG_FILENAME}"
	"${CMAKE_CURRENT_BINARY_DIR}/${JSON_CONFIGVERSION_FILENAME}"
	DESTINATION ${JSON_CONFIG_DESTINATION})
