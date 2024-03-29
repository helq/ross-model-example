# This is to allow tests to include header files without ugly "../../.."
#set(MODEL_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}" PARENT_SCOPE)

# Setting up ROSS and additional utilities (Git stuff is used in
# configuration header file, below)
include_directories(
  "${ROSS_SOURCE_DIR}"
  "${ROSS_BINARY_DIR}"
)
# Uncomment in case of using RIO
#include_directories(${RIO_SOURCE_DIR} ${RIO_BINARY_DIR})
list(APPEND CMAKE_MODULE_PATH "${ROSS_SOURCE_DIR}/cmake/")
include(GetGitRevisionDescription)
get_git_head_revision(GIT_RESPEC_MODEL GIT_SHA1_MODEL)

# Building configuration header file
configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/model_config.h.in
  ${CMAKE_CURRENT_BINARY_DIR}/model_config.h
  @ONLY
)
set_source_files_properties(
  ${CMAKE_CURRENT_BINARY_DIR}/model_config.h
  PROPERTIES GENERATED TRUE
)
include_directories(${CMAKE_CURRENT_BINARY_DIR})

add_library(model_lib
  driver.c
  mapping.c
  utils.c
)

# Compiling ROSS model model
add_executable(modelbin model.main.c)
target_link_libraries(modelbin
  PRIVATE
    model_lib
    m
    ROSS
#   RIO # uncomment if using RIO
)

# Including variables that indicate where the usr/bin is.
# It defines ${CMAKE_INSTALL_FULL_BINDIR}
include(GNUInstallDirs)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/modelbin
  DESTINATION ${CMAKE_INSTALL_FULL_BINDIR}
  PERMISSIONS
    OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE
    WORLD_READ WORLD_EXECUTE
)
