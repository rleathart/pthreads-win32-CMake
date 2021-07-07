cmake_minimum_required(VERSION 3.10)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Get CMAKE_PREFIX_PATH from env if possible
if(NOT CMAKE_PREFIX_PATH)
  set(CMAKE_PREFIX_PATH $ENV{CMAKE_PREFIX_PATH})
  # Convert from Unix path sep
  string(REPLACE ":" ";" CMAKE_PREFIX_PATH "${CMAKE_PREFIX_PATH}")
endif()

project(pthread C)

add_compile_definitions(
  HAVE_STRUCT_TIMESPEC
  PTW32_BUILD
  )

if(NOT MSVC)
  add_compile_options(-Wno-pointer-sign)
endif()

set(SRC
  attr.c
  barrier.c
  cancel.c
  cleanup.c
  condvar.c
  create.c
  dll.c
  exit.c
  fork.c
  global.c
  misc.c
  mutex.c
  private.c
  rwlock.c
  sched.c
  semaphore.c
  spin.c
  sync.c
  tsd.c
  nonportable.c
  )

add_library(${PROJECT_NAME} ${SRC})

################################################################################
#                         INSTALLATION
# Read CMAKE_INSTALL_PREFIX from environment if not given on the command-line
if(DEFINED ENV{CMAKE_INSTALL_PREFIX} AND
    CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set(CMAKE_INSTALL_PREFIX $ENV{CMAKE_INSTALL_PREFIX} CACHE
    PATH "Installation directory" FORCE)
endif()
message("Install prefix: " ${CMAKE_INSTALL_PREFIX})

target_include_directories(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/.>
    $<INSTALL_INTERFACE:include>
    PRIVATE .)
target_compile_definitions(${PROJECT_NAME} INTERFACE
  HAVE_STRUCT_TIMESPEC
  )
install(
  TARGETS ${PROJECT_NAME} EXPORT ${PROJECT_NAME}Config
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
  )
install(FILES pthread.h sched.h semaphore.h DESTINATION include)
install(EXPORT ${PROJECT_NAME}Config DESTINATION lib/cmake/${PROJECT_NAME})
export(TARGETS ${PROJECT_NAME} FILE ${PROJECT_NAME}Config.cmake)
################################################################################
