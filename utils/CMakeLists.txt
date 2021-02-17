# ----------------------------------------------------------------------------
#   Utils
# ----------------------------------------------------------------------------

add_executable(fbow_create_voc_step0
    fbow_create_voc_step0.cpp)
target_link_libraries(fbow_create_voc_step0
    PRIVATE ${FBOW_LIB} ${OpenCV_LIBS})
target_include_directories(fbow_create_voc_step0
    PRIVATE ${PROJECT_SOURCE_DIR}/src)

add_executable(fbow_create_voc_step0_list
    fbow_create_voc_step0_list.cpp)
target_link_libraries(fbow_create_voc_step0_list
    PRIVATE ${FBOW_LIB} ${OpenCV_LIBS})
target_include_directories(fbow_create_voc_step0_list
    PRIVATE ${PROJECT_SOURCE_DIR}/src)

add_executable(fbow_create_voc_step1
    fbow_create_voc_step1.cpp)
target_link_libraries(fbow_create_voc_step1
    PRIVATE ${FBOW_LIB} ${OpenCV_LIBS})
target_include_directories(fbow_create_voc_step1
    PRIVATE ${PROJECT_SOURCE_DIR}/src)

add_executable(fbow_create_voc_step1_opencv
    fbow_create_voc_step1_opencv.cpp)
target_link_libraries(fbow_create_voc_step1_opencv
    PRIVATE ${FBOW_LIB} ${OpenCV_LIBS})
target_include_directories(fbow_create_voc_step1_opencv
    PRIVATE ${PROJECT_SOURCE_DIR}/src)

add_executable(fbow_transform
    fbow_transform.cpp)
target_link_libraries(fbow_transform
    PRIVATE ${FBOW_LIB} ${OpenCV_LIBS})
target_include_directories(fbow_transform
    PRIVATE ${PROJECT_SOURCE_DIR}/src)

add_executable(fbow_image_matching
    image_matching.cpp)
target_link_libraries(fbow_image_matching
    PRIVATE ${FBOW_LIB} ${OpenCV_LIBS})
target_include_directories(fbow_image_matching
    PRIVATE ${PROJECT_SOURCE_DIR}/src)


install(TARGETS
    fbow_image_matching
    fbow_create_voc_step0
    fbow_create_voc_step0_list
    fbow_create_voc_step1
    fbow_create_voc_step1_opencv
    fbow_transform
    RUNTIME DESTINATION bin
)
