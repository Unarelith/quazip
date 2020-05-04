file(GLOB SRCS "*.c" "*.cpp")
file(GLOB PUBLIC_HEADERS "*.h")

# Must be added to enable export macro
ADD_DEFINITIONS(-DQUAZIP_BUILD)

qt5_wrap_cpp(MOC_SRCS ${PUBLIC_HEADERS})
set(SRCS ${SRCS} ${MOC_SRCS})

if (QUAZIP_STATIC)
	add_library(Quazip STATIC ${SRCS})
else()
	add_library(Quazip SHARED ${SRCS})
endif()

add_dependencies(Quazip zlib)

# Windows uses .lib extension for both static and shared library
# *nix systems use different extensions for SHARED and STATIC library and by convention both libraries have the same name
if (NOT WIN32 AND QUAZIP_STATIC)
    set_target_properties(Quazip PROPERTIES OUTPUT_NAME Quazip)
endif ()

target_include_directories(Quazip PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR})
set_target_properties(Quazip PROPERTIES VERSION ${QUAZIP_LIB_VERSION} SOVERSION ${QUAZIP_LIB_SOVERSION} DEBUG_POSTFIX d)

target_link_libraries(Quazip zlib)

QT5_USE_MODULES(Quazip Core)

install(FILES ${PUBLIC_HEADERS} DESTINATION include/Quazip)
install(TARGETS Quazip LIBRARY DESTINATION ${LIB_DESTINATION} ARCHIVE DESTINATION ${LIB_DESTINATION} RUNTIME DESTINATION ${LIB_DESTINATION})

