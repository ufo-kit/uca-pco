# --- Common ------------------------------------------------------------------

cmake_minimum_required(VERSION 2.6)
project(ucapco C)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")

find_package(PkgConfig)
include(PkgConfigVars)

add_definitions("-std=c99 -Wall")
add_definitions(-DG_LOG_DOMAIN="Uca-Pco")

pkg_check_modules(UCA libuca>=2.1.0 REQUIRED)
pkg_check_variable(libuca plugindir)


# --- Plugin specific ---------------------------------------------------------

find_package(PCO)
find_package(FgLib5)
find_package(ClSerSis)

include(Glib2MkEnums)

include_directories(${PCO_INCLUDE_DIRS}
    ${clsersis_INCLUDE_DIR}
    ${FgLib5_INCLUDE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_BINARY_DIR}
    ${UCA_INCLUDE_DIRS})

glib2_mkenums(uca-pco-enums
    uca-pco-enums
    uca-pco-camera.h)

add_library(ucapco SHARED
    uca-pco-camera.c
    uca-pco-enums.c)

link_directories(${UCA_LIBRARY_DIRS})

target_link_libraries(ucapco
    ${UCA_LIBRARIES}
    ${PCO_LIBRARIES}
    ${clsersis_LIBRARY}
    ${FgLib5_LIBRARY})

install(TARGETS ucapco
    LIBRARY DESTINATION ${LIBUCA_PLUGINDIR})

# --- Packaging specific ---------------------------------------------------------

set(VERSION 1.2.2)
set(RELEASE 1)

set(CPACK_GENERATOR "RPM")
set(CPACK_PACKAGE_NAME "uca-plugin-pco")
set(CPACK_PACKAGE_VERSION ${VERSION})
set(CPACK_PACKAGE_RELEASE ${RELEASE})
set(CPACK_RPM_PACKAGE_RELEASE ${RELEASE})
set(CPACK_PACKAGE_VENDOR "ANKA Computing Group")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Uca plugin for the pco detector family.")
set(CPACK_PACKAGE_DESCRIPTION_FILE "${CMAKE_CURRENT_SOURCE_DIR}/package_description.txt")

set(CPACK_PACKAGING_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX})
set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-${CPACK_PACKAGE_RELEASE}.${CMAKE_SYSTEM_PROCESSOR}")

set(CPACK_RPM_PACKAGE_REQUIRES "libuca >= 2.1, libpco >= 1.0, siso-rt5 >= 5.2")

set(CPACK_RPM_PACKAGE_GROUP "Hardware/Camera")
set(CPACK_RPM_CHANGELOG_FILE "${CMAKE_CURRENT_SOURCE_DIR}/package_changelog.txt")

include(CPack)


