include(CTest)

set(INC_DIR "${PROJECT_SOURCE_DIR}/include")
set(GEN_DIR "${CMAKE_CURRENT_BINARY_DIR}/generated")
set(FBS_DIR "${PROJECT_SOURCE_DIR}/test/monster_test")

include_directories("${GEN_DIR}" "${INC_DIR}")

file(GLOB FBS_FS ${FBS_DIR}/*.fbs)

add_custom_command (
    OUTPUT ${GEN_DIR}/monster_test.h
    COMMAND ${CMAKE_COMMAND} -E make_directory "${GEN_DIR}"
    # We could also use the recursive -r option, but this tests adding files manually to the output file.
    COMMAND flatcc_cli -cwv --reader -o "${GEN_DIR}" "--outfile=monster_test.h" "${FBS_DIR}/attributes.fbs" "${FBS_DIR}/include_test2.fbs" "${FBS_DIR}/include_test1.fbs" "${FBS_DIR}/monster_test.fbs"
    DEPENDS flatcc_cli "${FBS_FS}"
    COMMENT "Generate monster_test_concat fbs headers"
)

include_directories("${GEN_DIR}" "${INC_DIR}")
add_executable(monster_test_concat monster_test_concat.c ${GEN_DIR}/monster_test.h)
target_link_libraries(monster_test_concat flatccrt)

add_test(monster_test_concat monster_test_concat${CMAKE_EXECUTABLE_SUFFIX})
