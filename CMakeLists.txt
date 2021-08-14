cmake_minimum_required(VERSION 3.10)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Get CMAKE_PREFIX_PATH from env if possible
if(NOT CMAKE_PREFIX_PATH)
  set(CMAKE_PREFIX_PATH $ENV{CMAKE_PREFIX_PATH})
  # Convert from Unix path sep
  string(REPLACE ":" ";" CMAKE_PREFIX_PATH "${CMAKE_PREFIX_PATH}")
endif()

set(filename pthreads-w32-2-9-1-release.tar.gz)
if(NOT EXISTS src)
  message("Downlading pthreads...")
  file(DOWNLOAD
    https://mirrors.kernel.org/sourceware/pthreads-win32/${filename}
    ${filename}
    SHOW_PROGRESS
    )
  message("Extracting...")
  execute_process(
    COMMAND ${CMAKE_COMMAND} -E tar "xf" ${filename} -- *.c *.h *COPYING*
    )
  execute_process(
    COMMAND ${CMAKE_COMMAND} -E rm ${filename}
    )
  execute_process(
    COMMAND ${CMAKE_COMMAND} -E rename pthreads-w32-2-9-1-release src
    )
  execute_process(
    COMMAND ${CMAKE_COMMAND} -E make_directory include
    )
  execute_process(
    COMMAND ${CMAKE_COMMAND} -E rename src/pthread.h include/pthread.h
    )
  execute_process(
    COMMAND ${CMAKE_COMMAND} -E rename src/sched.h include/sched.h
    )
  execute_process(
    COMMAND ${CMAKE_COMMAND} -E rename src/semaphore.h include/semaphore.h
    )
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
  src/attr.c
  src/barrier.c
  src/cancel.c
  src/cleanup.c
  src/condvar.c
  src/create.c
  src/dll.c
  src/exit.c
  src/fork.c
  src/global.c
  src/misc.c
  src/mutex.c
  src/private.c
  src/rwlock.c
  src/sched.c
  src/semaphore.c
  src/spin.c
  src/sync.c
  src/tsd.c
  src/nonportable.c
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
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
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
install(DIRECTORY include/ DESTINATION include)
install(EXPORT ${PROJECT_NAME}Config DESTINATION lib/cmake/${PROJECT_NAME})
export(TARGETS ${PROJECT_NAME} FILE ${PROJECT_NAME}Config.cmake)
################################################################################
