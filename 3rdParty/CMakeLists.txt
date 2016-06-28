cmake_minimum_required(VERSION 2.6)

set (lbr_flags	--disable-shared
				--enable-static --disable-udf
				--disable-bdjava --disable-doxygen-doc
				--disable-examples --without-freetype
				--without-fontconfig
				)

add_custom_target (static_libbluray ./bootstrap
					COMMAND ./configure ${lbr_flags}
					COMMAND make -j2
					WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/libbluray"
				)

add_custom_target (patch_libbluray
					sh -c 'if ! patch -R -p2 --dry-run < ../../mpls_parse.patch\; then patch patch -p2 < ../../mpls_parse.patch\; fi'
					WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/libbluray"
				)

if (enable_clipnames)
	add_dependencies(static_libbluray patch_libbluray)
endif()

find_package(LibXml2 REQUIRED)

set (3rdParty_libraries ${3rdParty_libraries} ${LIBXML2_LIBRARIES} "${CMAKE_CURRENT_SOURCE_DIR}/libbluray/.libs/libbluray.a" -ldl PARENT_SCOPE)
set (3rdParty_include_dirs ${3rdParty_library_dirs} "${CMAKE_CURRENT_SOURCE_DIR}/libbluray/src" PARENT_SCOPE)
