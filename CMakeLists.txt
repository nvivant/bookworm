# Set the name of the application
project (bookworm)

# the oldest stable cmake version we support
cmake_minimum_required (VERSION 2.6)

# tell cmake where its modules can be found in our project directory
list (APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)

# Set all the data directories
set (DATADIR "${CMAKE_INSTALL_PREFIX}/share")
set (PKGDATADIR "${DATADIR}/bookworm")
set (ICON_DIR ${PKGDATADIR}/icons/hicolor)
set (SCRIPTS_DIR ${PKGDATADIR}/scripts)

# Set the application executable name and version info
set (EXEC_NAME "bookworm")
set (RELEASE_NAME "bookworm v0.9.1")
set (VERSION "0.9.1")
set (VERSION_INFO "Bookworm - An eBook Reader")

# Translation files
set (GETTEXT_PACKAGE "${CMAKE_PROJECT_NAME}")
add_definitions ("-DGETTEXT_PACKAGE=\"${GETTEXT_PACKAGE}\"")
add_subdirectory (po)

# Replace parameters in source code
configure_file("${CMAKE_CURRENT_SOURCE_DIR}/src/constants.vala.in" "${CMAKE_SOURCE_DIR}/src/constants.vala" @ONLY)

# define variable for sources
set (SOURCE_LIST
    src/main.vala
    src/bookworm.vala
    src/utils.vala
    src/constants.vala
    src/ePubReader.vala
    src/book.vala
    src/database.vala
    src/settings.vala
    src/bookinfo.vala
    src/headerbar.vala
    src/dialog.vala
    src/window.vala
    src/prefpopover.vala
    src/library.vala
    src/pdfReader.vala
    src/contentHandler.vala
    src/comicsReader.vala
    src/backgroundTasks.vala
    src/mobiReader.vala
)

include (GSettings)
add_schema ("schemas/com.github.babluboy.bookworm.gschema.xml")

# we're about to use pkgconfig to make sure dependencies are installed so let's find pkgconfig first
find_package(PkgConfig)

# now let's actually check for the required dependencies
pkg_check_modules(DEPS REQUIRED
                  gtk+-3.0
                  gee-0.8
                  granite
                  webkit2gtk-4.0>=2.16.0
                  sqlite3>=3.5.9
                  poppler-glib
                 )

add_definitions(${DEPS_CFLAGS})
link_libraries(${DEPS_LIBRARIES} -lm)
link_directories(${DEPS_LIBRARY_DIRS})

# make sure we have vala
find_package(Vala REQUIRED)
# make sure we use vala
include(ValaVersion)
# make sure it's the desired version of vala
ensure_vala_version("0.26" MINIMUM)

# files we want to compile
include(ValaPrecompile)

set (BASIC_VALAC_OPTIONS
   -g
   --thread
   --target-glib=2.38
)

set (GLOBAL_VALAC_OPTIONS
  ${BASIC_VALAC_OPTIONS}
)

vala_precompile(VALA_C ${EXEC_NAME}
    src/main.vala
    src/bookworm.vala
    src/utils.vala
    src/constants.vala
    src/ePubReader.vala
    src/book.vala
    src/database.vala
    src/settings.vala
    src/bookinfo.vala
    src/headerbar.vala
    src/dialog.vala
    src/window.vala
    src/prefpopover.vala
    src/library.vala
    src/pdfReader.vala
    src/contentHandler.vala
    src/comicsReader.vala
    src/backgroundTasks.vala
    src/mobiReader.vala

# tell what libraries to use when compiling
PACKAGES
    gtk+-3.0
    gee-0.8
    granite
    webkit2gtk-4.0>=2.16.0
    sqlite3
    poppler-glib
)

# tell cmake what to call the executable we just made
add_executable(${EXEC_NAME} ${VALA_C})

# set linking libraries
install (TARGETS ${EXEC_NAME} RUNTIME DESTINATION bin)

# install bookworm app icons in generic folder - /usr/share/icons/hicolor
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm.png DESTINATION share/icons/hicolor/16x16/apps)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/24/bookworm.png DESTINATION share/icons/hicolor/24x24/apps)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/32/bookworm.png DESTINATION share/icons/hicolor/32x32/apps)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/48/bookworm.png DESTINATION share/icons/hicolor/48x48/apps)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/64/bookworm.png DESTINATION share/icons/hicolor/64x64/apps)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/128/bookworm.png DESTINATION share/icons/hicolor/128x128/apps)

# install bookworm fall back icons in application specific folder - /usr/share/bookworm/icons
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-go-next.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-go-previous.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-object-select-symbolic.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-list-add.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-list-remove.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-view-grid-symbolic.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-view-list-symbolic.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-line-height-more.png DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-line-height-less.png DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-width-more.png DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-width-less.png DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm_rating_1.png DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm_rating_2.png DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm_rating_3.png DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm_rating_4.png DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm_rating_5.png DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-help-about-symbolic.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-help-about.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-selection-option.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-selection-checked.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-insert-image.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-help-info-symbolic.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-format-text-larger-symbolic.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/16/bookworm-format-text-smaller-symbolic.svg DESTINATION ${ICON_DIR}/16x16/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/24/bookworm-bookmark-inactive.png DESTINATION ${ICON_DIR}/24x24/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/24/bookworm-bookmark-active.png DESTINATION ${ICON_DIR}/24x24/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/24/bookworm-selection-checked.svg DESTINATION ${ICON_DIR}/24x24/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/24/bookworm-selection-option.svg DESTINATION ${ICON_DIR}/24x24/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/24/bookworm-open-menu.svg DESTINATION ${ICON_DIR}/24x24/actions/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/256/bookworm-placeholder-cover.png DESTINATION ${ICON_DIR}/256x256/apps/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/256/bookworm-default-cover-1.png DESTINATION ${ICON_DIR}/256x256/apps/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/256/bookworm-default-cover-2.png DESTINATION ${ICON_DIR}/256x256/apps/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/256/bookworm-default-cover-3.png DESTINATION ${ICON_DIR}/256x256/apps/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/256/bookworm-default-cover-4.png DESTINATION ${ICON_DIR}/256x256/apps/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/icons/256/bookworm-default-cover-5.png DESTINATION ${ICON_DIR}/256x256/apps/)

# install app specific desktop, appdata and css files
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/com.github.babluboy.bookworm.desktop DESTINATION ${DATADIR}/applications)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/com.github.babluboy.bookworm.appdata.xml DESTINATION ${DATADIR}/metainfo/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/com.github.babluboy.bookworm.app.css DESTINATION ${DATADIR}/bookworm)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/com.github.babluboy.bookworm.contract DESTINATION share/contractor)

# install bookworm scripts in application specific folder - /usr/share/bookworm/scripts
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/com.github.babluboy.bookworm.monitor.sh PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/tasks)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/com.github.babluboy.bookworm.search.sh PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/tasks)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/com.github.babluboy.bookworm.htmlscripts.txt PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/tasks)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/mobi_lib/mobi_dict.py PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/mobi_lib)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/mobi_lib/mobi_html.py PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/mobi_lib)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/mobi_lib/mobi_index.py PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/mobi_lib)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/mobi_lib/mobi_k8proc.py PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/mobi_lib)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/mobi_lib/mobi_ncx.py PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/mobi_lib)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/mobi_lib/mobi_opf.py PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/mobi_lib)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/mobi_lib/mobi_split.py PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/mobi_lib)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/mobi_lib/mobi_uncompress.py PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/mobi_lib)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/mobi_lib/mobi_unpack.py PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/mobi_lib)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/scripts/mobi_lib/mobi_utils.py PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ WORLD_READ WORLD_EXECUTE DESTINATION ${SCRIPTS_DIR}/mobi_lib)
