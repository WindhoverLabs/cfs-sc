include(CTest)
find_program( MEMORYCHECK_COMMAND valgrind )
find_package(Threads)
set( MEMORYCHECK_COMMAND_OPTIONS "--trace-children=yes --leak-check=full" )

# Set include directories
include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}
	${CFE_INC_DIRS}
	${OSAL_INC_DIRS}
	${UTF_INCS}
	${APP_INC_DIRS})

add_definitions(-DCFE_LINUX)

set(CMAKE_C_FLAGS "-g -O0 -Wformat=0 -Wno-pointer-to-int-cast -Wno-int-to-pointer-cast -fprofile-arcs -ftest-coverage -m32")

# Add the executables
add_executable(SC-UT 
		${CMAKE_CURRENT_SOURCE_DIR}/utf_test_sc.c
		${APP_SRC}
		${UTF_SRC})
target_link_libraries(SC-UT -fprofile-arcs gcov ${CMAKE_THREAD_LIBS_INIT})

# We need to both run the test and compare the as-run with the expected output
# with a diff command. 
add_test(NAME sc-ut
    COMMAND ${CMAKE_COMMAND}
    	-DTEST_PROG=$<TARGET_FILE:SC-UT>
    	-DEXPECTED_OUTPUT=${CMAKE_CURRENT_SOURCE_DIR}/utf_test_sc.out
    	-DACTUAL_OUTPUT=${CMAKE_CURRENT_BINARY_DIR}/utf_test_sc.out
    -P ${CMAKE_CURRENT_SOURCE_DIR}/runtest.cmake)

# Copy input files to build directory
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/utf_test_cmds_sc.in ${CMAKE_CURRENT_BINARY_DIR} COPYONLY)