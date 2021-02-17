include_directories(${PROJECT_SOURCE_DIR}/src)
LINK_LIBRARIES(${PROJECT_NAME}) # TODO: no global linking

add_executable(fbow_create_voc_step0 fbow_create_voc_step0.cpp)
add_executable(fbow_create_voc_step0_list fbow_create_voc_step0_list.cpp)
add_executable(fbow_create_voc_step1 fbow_create_voc_step1.cpp)
#add_executable(fbow_create_voc_step1_opencv fbow_create_voc_step1_opencv.cpp)
add_executable(fbow_transform fbow_transform.cpp)
add_executable(image_matching image_matching.cpp)

# TODO: target-specific linking:
# TODO: variable for fbow_lib name
#target_link_libraries(fbow_create_vocabulary ${OpenCV_LIBS} fbow_lib)

install(TARGETS fbow_create_voc_step0 fbow_create_voc_step1 fbow_transform 
    RUNTIME DESTINATION bin)
