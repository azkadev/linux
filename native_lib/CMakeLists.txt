cmake_minimum_required(VERSION 3.16)

project(linux_library VERSION 0.0.1 LANGUAGES C CXX)
set(CMAKE_CXX_STANDARD 20)
 
message(STATUS "START COMPILE PACKAGE_TEMPLATE")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")

set(linux_shared_dir "lib")
set(linux_exe_dir "bin")
set(prefix_shared_library "lib")
 

if (ANDROID)
 set(package_shared_extension "_${ANDROID_ARCH_NAME}.so")
 set(package_exe_extension "")
 set(package_name "linux_${ANDROID_ARCH_NAME}")
elseif (WIN32)
 set(package_shared_extension ".dll")
 set(package_exe_extension ".exe") 
 set(package_name "linux")
elseif (APPLE)
 set(package_shared_extension ".dylib")
 set(package_exe_extension "") 
 set(package_name "linux")
else()
 set(package_shared_extension ".so")
 set(package_exe_extension "") 
 set(package_name "linux")
endif()


add_library(linux_shared SHARED linux.cpp)
add_executable(linux_exe linux.cpp)

set_target_properties(linux_shared PROPERTIES 
  LIBRARY_OUTPUT_DIRECTORY "${linux_shared_dir}"
  PREFIX "${prefix_shared_library}"
  OUTPUT_NAME "${package_name}"
)

set_target_properties(linux_exe PROPERTIES 
  RUNTIME_OUTPUT_DIRECTORY "${linux_exe_dir}"
  OUTPUT_NAME "${package_name}"
)

if (WIN32)
 set_target_properties(linux_shared PROPERTIES
  WINDOWS_EXPORT_ALL_SYMBOLS ON
)
endif (WIN32)
 
add_custom_command(
  TARGET linux_shared POST_BUILD 
  COMMAND ${CMAKE_STRIP} --strip-debug --strip-unneeded "${linux_shared_dir}/${prefix_shared_library}linux${package_shared_extension}"
)

add_custom_command(
  TARGET linux_exe POST_BUILD 
  COMMAND ${CMAKE_STRIP} --strip-debug --strip-unneeded "${linux_exe_dir}/linux${package_exe_extension}" 
)

 