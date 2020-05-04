cmake_minimum_required(VERSION 2.8.5)
project(QuaZip)

# CMP0042: Explicitly acknowledge MACOSX_RPATH
# (introduced in CMake 2.8.12, enabled by default in CMake 3.0,
# and producing a warning when unset since 3.7.1)
cmake_policy(SET CMP0042 NEW)

set(QUAZIP_LIB_VERSION 1.0.0)
set(QUAZIP_LIB_SOVERSION 1)

include(GNUInstallDirs)

find_package(Qt5 COMPONENTS Core REQUIRED)

set(CMAKE_CXX_STANDARD 11)

#------------------------------------------------------------------------------
# Submodules
# from https://cliutils.gitlab.io/modern-cmake/chapters/projects/submodule.html
#------------------------------------------------------------------------------
find_package(Git QUIET)

if(GIT_FOUND AND EXISTS "${PROJECT_SOURCE_DIR}/.git")
	option(GIT_SUBMODULE "Check submodules during build" ON)
	if(GIT_SUBMODULE)
		message(STATUS "Submodule update")
		execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
			WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
			RESULT_VARIABLE GIT_SUBMOD_RESULT)
		if(NOT GIT_SUBMOD_RESULT EQUAL "0")
			message(FATAL_ERROR "git submodule update --init failed with ${GIT_SUBMOD_RESULT}, please checkout submodules")
		endif()
	endif()
endif()

if(NOT EXISTS "${PROJECT_SOURCE_DIR}/zlib/CMakeLists.txt")
    message(FATAL_ERROR "The submodules were not downloaded! GIT_SUBMODULE was turned off or failed. Please update submodules and try again.")
endif()
#------------------------------------------------------------------------------
add_subdirectory(zlib)
include_directories(zlib)
#------------------------------------------------------------------------------
add_subdirectory(quazip)

if(UNIX AND NOT APPLE)
    configure_file(quazip.pc.cmakein quazip.pc @ONLY)
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/quazip.pc DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig)
endif()

install(FILES QuaZipConfig.cmake
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/QuaZip${QUAZIP_LIB_VERSION_SUFFIX}
        RENAME QuaZip${QUAZIP_LIB_VERSION_SUFFIX}Config.cmake)
