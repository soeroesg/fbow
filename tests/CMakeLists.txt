# ----------------------------------------------------------------------------
#   Tests
# ----------------------------------------------------------------------------

add_executable(fbow_test_dbow2VSfbow 
    test_dbow2VSfbow.cpp
    dbow2/ScoringObject.cpp
    dbow2/BowVector.h
    dbow2/FClass.h
    dbow2/FeatureVector.h
    dbow2/FORB.h
    dbow2/ScoringObject.h
    dbow2/TemplatedVocabulary.h)
target_link_libraries(fbow_test_dbow2VSfbow
    PRIVATE ${FBOW_LIB} ${OpenCV_LIBS})
target_include_directories(fbow_test_dbow2VSfbow
    PRIVATE ${PROJECT_SOURCE_DIR}/src)

add_executable(fbow_test_cpu_x86
    test_cpu_x86.cpp)
target_link_libraries(fbow_test_cpu_x86
    PRIVATE ${FBOW_LIB} ${OpenCV_LIBS})
target_include_directories(fbow_test_cpu_x86
    PRIVATE ${PROJECT_SOURCE_DIR}/src)

add_executable(fbow_test_flann
    test_flann.cpp)
target_link_libraries(fbow_test_flann
    PRIVATE ${FBOW_LIB} ${OpenCV_LIBS})
target_include_directories(fbow_test_flann
    PRIVATE ${PROJECT_SOURCE_DIR}/src)

install(TARGETS
    fbow_test_cpu_x86
    fbow_test_dbow2VSfbow
    fbow_test_flann
    RUNTIME DESTINATION bin)

