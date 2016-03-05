cmake_minimum_required(VERSION 2.6.0)
#cmake_minimum_required(VERSION 2.8.8)  # need the object library

# set up pods
set(POD_NAME spotless)
include(cmake/pods.cmake)

file(RELATIVE_PATH relpath ${CMAKE_INSTALL_PREFIX}/matlab ${CMAKE_CURRENT_SOURCE_DIR})

# setup matlab pods-compliance
include(cmake/matlab_pods.cmake)
pods_configure_matlab_paths()

find_program(matlab matlab)
if (NOT matlab )
  message(FATAL_ERROR "Could not find matlab executable")
endif()

# workflow below modeled after example at https://cmake.org/Wiki/CustomCommandCustomTargetInstall

add_custom_command(OUTPUT "${CMAKE_BINARY_DIR}/matlab/addpath_spotless.m" "${CMAKE_BINARY_DIR}/matlab/rmpath_spotless.m"
			  COMMAND ${matlab} -wait -nosplash -nodesktop -nodisplay -r "\"cd('${CMAKE_CURRENT_SOURCE_DIR}');spot_pods_install('${CMAKE_BINARY_DIR}/matlab','${relpath}');exit\""
			  DEPENDS
			    "${CMAKE_CURRENT_SOURCE_DIR}/spotless/spot_install.m"
			    "${CMAKE_CURRENT_SOURCE_DIR}/spot_pods_install.m"
        )
add_custom_target(pathfiles ALL DEPENDS "${CMAKE_BINARY_DIR}/matlab/addpath_spotless.m" "${CMAKE_BINARY_DIR}/matlab/rmpath_spotless.m")

install(FILES "${CMAKE_BINARY_DIR}/matlab/addpath_spotless.m" "${CMAKE_BINARY_DIR}/matlab/rmpath_spotless.m"
          DESTINATION ${CMAKE_INSTALL_PREFIX}/matlab)
