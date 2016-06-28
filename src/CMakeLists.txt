set (bdinfo_sources
        bdinfo.c
        chapters.h
        chapters.c
        iso-639-2.h
        mempool.h
        mempool.c
        util.h
        util.c
)

# Extra source file for clipnames
if (enable_clipnames)
    set (bdinfo_sources
         ${bdinfo_sources}
         mpls.c
    )

    set(GET_CLIPNAMES TRUE)
endif()

if (require_languages)
    set(REQUIRE_LANGS TRUE)
endif()

configure_file(config.h.in config.h)

include_directories(${CMAKE_CURRENT_BINARY_DIR}
                    ${3rdParty_include_dirs}
)

add_executable (${PROJECT_NAME} ${bdinfo_sources})
target_link_libraries (${PROJECT_NAME} ${3rdParty_libraries})

if (NOT system_libbluray)
    add_dependencies(${PROJECT_NAME} static_libbluray)
endif()

install(TARGETS ${PROJECT_NAME} DESTINATION bin)
