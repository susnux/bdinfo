cmake_minimum_required(VERSION 2.6)
cmake_policy(SET CMP0048 NEW)

project(bdinfo VERSION 1.3)

option(system_libbluray  "Use system libbluray"          ON)
option(enable_clipnames  "Enable clipnames"              OFF)
option(require_languages "Require languages as argument" OFF)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c99")

if(enable_clipnames)
    set(system_libbluray OFF)
endif()

set(3rdParty_include_dirs "")

if(system_libbluray)
    find_package(PkgConfig)
    pkg_check_modules(LIBBLURAY REQUIRED libbluray)
    set(3rdParty_libraries ${LIBBLURAY_LIBRARIES})
else()
    add_subdirectory(3rdParty)
endif()

add_subdirectory(src)

if (NOT require_languages)
    add_custom_target(create_manpage ALL
                      cp "${CMAKE_CURRENT_SOURCE_DIR}/${PROJECT_NAME}.man" ${PROJECT_NAME}.1
    )
else()
    add_custom_target(create_manpage ALL
                              cp "${CMAKE_CURRENT_SOURCE_DIR}/${PROJECT_NAME}.man" .
                      COMMAND patch ${PROJECT_NAME}.man "${CMAKE_CURRENT_SOURCE_DIR}/bdinfo.man-required.patch"
                      COMMAND mv ${PROJECT_NAME}.man ${PROJECT_NAME}.1
    )
endif()

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}.1" DESTINATION share/man/man1)
