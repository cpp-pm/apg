cmake_minimum_required(VERSION 3.0)
project(apg VERSION 0.0.0)

option(BUILD_SHARED_LIBS "Build shared libraries" FALSE)

if(MSVC AND BUILD_SHARED_LIBS)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE)
endif()

# apg lib
set(APG_HDR_LIST apg/apg.h)
add_library(apg INTERFACE)

# unicode lib
set(UNICODE_SRC_LIST apg_unicode/apg_unicode.c)
set(UNICODE_HDR_LIST apg_unicode/apg_unicode.h)
if(BUILD_SHARED_LIBS)
  add_library(unicode SHARED ${UNICODE_SRC_LIST} ${UNICODE_HDR_LIST})
else()
  add_library(unicode STATIC ${UNICODE_SRC_LIST} ${UNICODE_HDR_LIST})
endif()
target_include_directories(unicode PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/apg_unicode>")

# gldb lib
# Requires OpenGL headers to build
#set(GLDB_SRC_LIST apg_gldb/apg_gldb.c)
#set(GLDB_HDR_LIST apg_gldb/apg_gldb.h)
#if(BUILD_SHARED_LIBS)
#  add_library(gldb SHARED ${GLDB_SRC_LIST} ${GLDB_HDR_LIST})
#else()
#  add_library(gldb STATIC ${GLDB_SRC_LIST} ${GLDB_HDR_LIST})
#endif()
#target_include_directories(gldb PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/apg_gldb>")

# bmp lib
set(BMP_SRC_LIST apg_bmp/apg_bmp.c)
set(BMP_HDR_LIST apg_bmp/apg_bmp.h)
if(BUILD_SHARED_LIBS)
  add_library(bmp SHARED ${BMP_SRC_LIST} ${BMP_HDR_LIST})
else()
  add_library(bmp STATIC ${BMP_SRC_LIST} ${BMP_HDR_LIST})
endif()
target_include_directories(bmp PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/apg_bmp>")

# wav lib
set(WAV_SRC_LIST apg_wav/apg_wav.c)
set(WAV_HDR_LIST apg_wav/apg_wav.h)
if(BUILD_SHARED_LIBS)
  add_library(wav SHARED ${WAV_SRC_LIST} ${WAV_HDR_LIST})
else()
  add_library(wav STATIC ${WAV_SRC_LIST} ${WAV_HDR_LIST})
endif()
target_include_directories(wav PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/apg_wav>")

# pixfont lib
set(PIXFONT_SRC_LIST apg_pixfont/apg_pixfont.c)
set(PIXFONT_HDR_LIST apg_pixfont/apg_pixfont.h)
if(BUILD_SHARED_LIBS)
  add_library(pixfont SHARED ${PIXFONT_SRC_LIST} ${PIXFONT_HDR_LIST})
else()
  add_library(pixfont STATIC ${PIXFONT_SRC_LIST} ${PIXFONT_HDR_LIST})
endif()
target_include_directories(pixfont PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/apg_pixfont>")

# maths lib
set(MATHS_HDR_LIST apg_maths/apg_maths.h)
add_library(maths INTERFACE)

# interp lib
set(INTERP_HDR_LIST apg_interp/apg_interp.h)
add_library(interp INTERFACE)

# tga lib
set(TGA_HDR_LIST apg_tga/apg_tga.h)
add_library(tga INTERFACE)

# console lib
set(CONSOLE_SRC_LIST apg_console/apg_console.c)
set(CONSOLE_HDR_LIST apg_console/apg_console.h)
if(BUILD_SHARED_LIBS)
  add_library(console SHARED ${CONSOLE_SRC_LIST} ${CONSOLE_HDR_LIST})
else()
  add_library(console STATIC ${CONSOLE_SRC_LIST} ${CONSOLE_HDR_LIST})
endif()
target_include_directories(console PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/apg_console>")
target_link_libraries(console PUBLIC pixfont)

# Introduce variables:
# * CMAKE_INSTALL_LIBDIR
# * CMAKE_INSTALL_BINDIR
# * CMAKE_INSTALL_INCLUDEDIR
include(GNUInstallDirs)

set(GENERATED_DIR "${CMAKE_CURRENT_BINARY_DIR}/generated")
set(VERSION_CONFIG "${GENERATED_DIR}/${PROJECT_NAME}ConfigVersion.cmake")
set(PROJECT_CONFIG "${GENERATED_DIR}/${PROJECT_NAME}Config.cmake")

set(CONFIG_INSTALL_DIR "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}")

set(TARGETS_EXPORT_NAME "${PROJECT_NAME}Targets")
set(NAMESPACE "${PROJECT_NAME}::")

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    "${VERSION_CONFIG}" COMPATIBILITY ExactVersion
)

configure_package_config_file(
    "cmake/Config.cmake.in"
    "${PROJECT_CONFIG}"
    INSTALL_DESTINATION "${CONFIG_INSTALL_DIR}"
)

install(
    TARGETS apg unicode bmp wav pixfont maths interp tga console
    EXPORT "${TARGETS_EXPORT_NAME}"
    INCLUDES DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}"
    RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
    LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
)

install(
    FILES ${APG_HDR_LIST} ${UNICODE_HDR_LIST} ${BMP_HDR_LIST} ${WAV_HDR_LIST} ${PIXFONT_HDR_LIST} ${MATHS_HDR_LIST} ${INTERP_HDR_LIST} ${TGA_HDR_LIST} ${CONSOLE_HDR_LIST}
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}
)

install(
    FILES "${PROJECT_CONFIG}" "${VERSION_CONFIG}"
    DESTINATION "${CONFIG_INSTALL_DIR}"
)

install(
    EXPORT "${TARGETS_EXPORT_NAME}"
    NAMESPACE "${NAMESPACE}"
    DESTINATION "${CONFIG_INSTALL_DIR}"
)
