#-----------------------------------------------------------------------
# CPM configuration
#-----------------------------------------------------------------------
set(CPM_MODULE_NAME cpm_mxnet)

if ((DEFINED CPM_DIR) AND (DEFINED CPM_UNIQUE_ID) AND (DEFINED CPM_TARGET_NAME))
  set(CPM_LIB_TARGET_NAME ${CPM_TARGET_NAME})
  set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CPM_DIR})
  include(CPM)
else()
  set(CPM_DIR "${CMAKE_CURRENT_BINARY_DIR}/cpm-packages" CACHE TYPE STRING)
  find_package(Git)
  if(NOT GIT_FOUND)
    message(FATAL_ERROR "CPM requires Git.")
  endif()
  if (NOT EXISTS ${CPM_DIR}/CPM.cmake)
    execute_process(
      COMMAND "${GIT_EXECUTABLE}" clone https://github.com/iauns/cpm ${CPM_DIR}
      RESULT_VARIABLE error_code
      OUTPUT_VARIABLE head_sha)
    if(error_code)
      message(FATAL_ERROR "CPM failed to get the hash for HEAD")
    endif()
  endif()
  include(${CPM_DIR}/CPM.cmake)
endif()

# All externals *must* define this.
CPM_ForceOnlyOneModuleVersion()

CPM_InitModule(${CPM_MODULE_NAME})

#------------------------------------------------------------------------------
# Caffe
#------------------------------------------------------------------------------

if(NOT DEFINED MXNET_COMMIT)
    set(MXNET_COMMIT "6ff10478eb7f6ec007e0a3a6c2705c03a71ad15a")
endif()

if(NOT DEFINED MXNET_GIT_REPO)
    set(MXNET_GIT_REPO "https://github.com/dmlc/mxnet.git")
endif()

set(REPO_SOURCE_DIR "${CMAKE_CURRENT_BINARY_DIR}/3rdParty/mxnet")

CPM_EnsureRepoIsCurrent(
  TARGET_DIR "${REPO_SOURCE_DIR}"
  GIT_REPOSITORY "${MXNET_GIT_REPO}"
  GIT_TAG "${MXNET_COMMIT}"
  USE_CACHING TRUE
)
CPM_ExportAdditionalIncludeDir("${REPO_SOURCE_DIR}/include")

add_subdirectory("${REPO_SOURCE_DIR}" "${CMAKE_CURRENT_BINARY_DIR}")