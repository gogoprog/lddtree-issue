project (lddtreeissue)
cmake_minimum_required (VERSION 3.22)

add_library (LibA SHARED src/liba.cpp)
add_library (LibB SHARED src/libb.cpp)
add_executable (Main src/main.cpp)

target_link_libraries (LibA PRIVATE LibB)
target_link_libraries (Main PUBLIC LibA)

set (patch_command patchelf ${CMAKE_CURRENT_BINARY_DIR}/*.so --set-rpath 'somethingwrong/')
add_custom_command (
    TARGET LibB POST_BUILD
    COMMAND ${patch_command}
    COMMENT "Patching rpath"
    )

set (ldd_command ldd ${CMAKE_CURRENT_BINARY_DIR}/Main | grep LibB)
add_custom_command (
    TARGET Main POST_BUILD
    COMMAND ${ldd_command}
    COMMENT "Running ldd"
    )

set (lddtree_command lddtree ${CMAKE_CURRENT_BINARY_DIR}/Main | grep LibB)
add_custom_command (
    TARGET Main POST_BUILD
    COMMAND ${lddtree_command}
    COMMENT "Running lddtree"
    )

