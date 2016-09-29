cmake_minimum_required(VERSION 3.5)

project(spotless)

if(CMAKE_VERSION VERSION_LESS 3.7)
  list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules/3.7")
endif()

find_package(Matlab REQUIRED COMPONENTS MAIN_PROGRAM)

set(SPOTLESS_MEX_DIR "${CMAKE_CURRENT_SOURCE_DIR}/spotless/mex")

set(SPOTLESS_MEX_SOURCES
  "${SPOTLESS_MEX_DIR}/spot_gset.c"
  "${SPOTLESS_MEX_DIR}/spot_mex_helpers.cpp"
  "${SPOTLESS_MEX_DIR}/spot_mex_msspoly_check_canonical.cpp"
  "${SPOTLESS_MEX_DIR}/spot_mex_msspoly_make_canonical_combine_coeffs.cpp"
  "${SPOTLESS_MEX_DIR}/spot_mex_msspoly_make_canonical_combine_powers.cpp")

set(SPOTLESS_MEX_OUTPUTS
  "${SPOTLESS_MEX_DIR}/spot_gset.${Matlab_MEX_EXTENSION}"
  "${SPOTLESS_MEX_DIR}/spot_mex_msspoly_check_canonical.${Matlab_MEX_EXTENSION}"
  "${SPOTLESS_MEX_DIR}/spot_mex_msspoly_make_canonical_combine_coeffs.${Matlab_MEX_EXTENSION}"
  "${SPOTLESS_MEX_DIR}/spot_mex_msspoly_make_canonical_combine_powers.${Matlab_MEX_EXTENSION}")

set(SPOTLESS_INSTALL_LOG "${CMAKE_CURRENT_BINARY_DIR}/spotless_install.log")

add_custom_command(
  OUTPUT ${SPOTLESS_MEX_OUTPUTS}
  COMMAND "${Matlab_MAIN_PROGRAM}" -logfile "${SPOTLESS_INSTALL_LOG}" -nosplash -nodesktop -nodisplay -wait -r "spot_install;exit"
  DEPENDS ${SPOTLESS_MEX_SOURCES}
  WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/spotless"
  COMMENT "Installing spotless...")

add_custom_target(spotless_install ALL DEPENDS ${SPOTLESS_MEX_OUTPUTS})

configure_file(addpath_spotless.m.in addpath_spotless.m @ONLY)
configure_file(rmpath_spotless.m.in rmpath_spotless.m @ONLY)

install(FILES
  "${CMAKE_CURRENT_BINARY_DIR}/addpath_spotless.m"
  "${CMAKE_CURRENT_BINARY_DIR}/rmpath_spotless.m"
  DESTINATION matlab)
