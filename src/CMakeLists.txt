# ----------------------------------------------------------------------------
#   Fast Bag of Words library
# ----------------------------------------------------------------------------

set(FBOW_LIB ${EXTRALIBNAME}fbow)
set(FBOW_LIB ${FBOW_LIB} PARENT_SCOPE) # elevate this variable to the parent scope too (still needs to be defined in this scope first) to be recognized in the utils and tests

set(SOURCES fbow.cpp vocabulary_creator.cpp)
set(HEADERS fbow.h fbow_exports.h vocabulary_creator.h cpu.h)

add_library(${FBOW_LIB} ${SOURCES} ${HEADERS})

set_target_properties(${FBOW_LIB} PROPERTIES
    DEFINE_SYMBOL FBOW_DLL_EXPORTS
    VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_SOVERSION}
    CLEAN_DIRECT_OUTPUT 1 # allow creating static and shared libs without conflicts
    OUTPUT_NAME "${FBOW_LIB}${PROJECT_DLLVERSION}" # avoid conflicts between library and binary target names
)

target_link_libraries(${FBOW_LIB}
    PUBLIC ${OpenCV_LIBS} ${OpenMP_CXX_LIBRARIES})

install(TARGETS ${FBOW_LIB}
    RUNTIME DESTINATION bin COMPONENT main # Install the dll file in bin directory
    LIBRARY DESTINATION lib PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE COMPONENT main
    ARCHIVE DESTINATION lib COMPONENT main # Install the dll.a file in lib directory
)

if(FBOW_DEVINSTALL)
    install(FILES ${HEADERS}
        DESTINATION include/fbow
        COMPONENT main
    )
endif()

# TODO: remove all below, but check install scripts

#set(sources
#    fbow.cpp
#    vocabulary_creator.cpp
#)

#set(headers
#    fbow.h
#    fbow_exports.h
#    vocabulary_creator.h
#)

#add_library(fbow ${sources} ${headers})

#set_target_properties(fbow PROPERTIES # create *nix style library versions + symbolic links
#    DEFINE_SYMBOL FBOW_DSO_EXPORTS
#    VERSION ${PROJECT_VERSION}
#    SOVERSION ${PROJECT_SOVERSION}
#    CXX_VISIBILITY_PRESET hidden # Make all the symbols hidden, so we have to explicitly define an API
#    CLEAN_DIRECT_OUTPUT 1 # allow creating static and shared libs without conflicts
#    OUTPUT_NAME "fbow${PROJECT_DLLVERSION}" # avoid conflicts between library and binary target names
#)

#include(GenerateExportHeader)
#generate_export_header(fbow)

#target_link_libraries(fbow PUBLIC opencv_core)
#target_link_libraries(fbow PRIVATE opencv_features2d)

#target_include_directories(fbow PUBLIC
#    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
#    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
#    $<INSTALL_INTERFACE:include>
#)

#install(TARGETS fbow EXPORT fbowConfig
#    RUNTIME DESTINATION bin
#    LIBRARY DESTINATION lib PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE
#    ARCHIVE DESTINATION lib
#)


#install(EXPORT arucoConfig DESTINATION "share/aruco/cmake")
