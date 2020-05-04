file(GLOB SRCS "*.c" "*.cpp")
file(GLOB PUBLIC_HEADERS "*.h")


# Must be added to enable export macro
ADD_DEFINITIONS(-DQUAZIP_BUILD)

qt_wrap_cpp(MOC_SRCS ${PUBLIC_HEADERS})
set(SRCS ${SRCS} ${MOC_SRCS})

add_library(${QUAZIP_LIB_TARGET_NAME} SHARED ${SRCS})
add_library(quazip_static STATIC ${SRCS})

add_dependencies(${QUAZIP_LIB_TARGET_NAME} zlib)
add_dependencies(quazip_static zlib)

# Windows uses .lib extension for both static and shared library
# *nix systems use different extensions for SHARED and STATIC library and by convention both libraries have the same name
if (NOT WIN32)
    set_target_properties(quazip_static PROPERTIES OUTPUT_NAME ${QUAZIP_LIB_VERSION_PREFIX}Quazip)
endif ()

target_include_directories(${QUAZIP_LIB_TARGET_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR})
target_include_directories(quazip_static PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR})
set_target_properties(${QUAZIP_LIB_TARGET_NAME} quazip_static PROPERTIES VERSION ${QUAZIP_LIB_VERSION} SOVERSION ${QUAZIP_LIB_SOVERSION} DEBUG_POSTFIX d)

target_link_libraries(${QUAZIP_LIB_TARGET_NAME} ${QT_QTMAIN_LIBRARY} ${QTCORE_LIBRARIES} zlib)
target_link_libraries(quazip_static ${QT_QTMAIN_LIBRARY} ${QTCORE_LIBRARIES} zlib)

install(FILES ${PUBLIC_HEADERS} DESTINATION include/${QUAZIP_LIB_VERSION_PREFIX}Quazip)
install(TARGETS ${QUAZIP_LIB_TARGET_NAME} quazip_static LIBRARY DESTINATION ${LIB_DESTINATION} ARCHIVE DESTINATION ${LIB_DESTINATION} RUNTIME DESTINATION ${LIB_DESTINATION})
