# This file is part of SEAL-TK, and is distributed under the OSI-approved BSD
# 3-Clause License. See top-level LICENSE file or
# https://github.com/Kitware/seal-tk/blob/master/LICENSE for details.

cmake_minimum_required(VERSION 3.11)
project(sealtk
  VERSION 0.1
  LANGUAGES CXX
  )

include(CMakePackageConfigHelpers)
include(GNUInstallDirs)

set(sealtk_BUILD_LIBDIR "${PROJECT_BINARY_DIR}/${CMAKE_INSTALL_LIBDIR}")

list(APPEND CMAKE_MODULE_PATH
  "${PROJECT_SOURCE_DIR}/cmake"
  "${PROJECT_SOURCE_DIR}/cmake/thirdparty"
  )

find_package(Qt5 5.10 REQUIRED COMPONENTS
  Test
  Widgets
  Xml
  Concurrent
  Widgets
  Gui
  Core
  )
find_package(qtExtensions REQUIRED)
find_package(kwiver REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(Threads REQUIRED)
find_package(Git)
find_package(Sphinx)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)
set(CMAKE_CXX_VISIBILITY_PRESET hidden)
set(CMAKE_AUTOMOC 1)
set(CMAKE_AUTORCC 1)
set(CMAKE_AUTOUIC 1)

enable_testing()

if(NOT CMAKE_VERSION VERSION_LESS 3.12)
  find_package(Python3)
  if(TARGET Python3::Interpreter)
    if(TARGET Git::Git)
      set(git_args --git $<TARGET_FILE:Git::Git>)
    endif()
    add_test(
      NAME SourceCheck
      COMMAND Python3::Interpreter
      "${PROJECT_SOURCE_DIR}/utilities/source_check.py" ${git_args}
      )
  endif()
endif()

add_subdirectory(sealtk)

export(EXPORT sealtk
  NAMESPACE sealtk::
  FILE "${sealtk_BUILD_LIBDIR}/cmake/sealtk/sealtk-targets.cmake"
  )

install(EXPORT sealtk
  NAMESPACE sealtk::
  DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/sealtk"
  FILE "sealtk-targets.cmake"
  COMPONENT Development
  )

write_basic_package_version_file(
  "${sealtk_BUILD_LIBDIR}/cmake/sealtk/sealtk-config-version.cmake"
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion
  )

configure_package_config_file(
  "${PROJECT_SOURCE_DIR}/cmake/sealtk-config.cmake.in"
  "${sealtk_BUILD_LIBDIR}/cmake/sealtk/sealtk-config.cmake"
  INSTALL_DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/sealtk"
  )

install(FILES
  "${sealtk_BUILD_LIBDIR}/cmake/sealtk/sealtk-config.cmake"
  "${sealtk_BUILD_LIBDIR}/cmake/sealtk/sealtk-config-version.cmake"
  DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/sealtk"
  COMPONENT Development
  )

configure_file(
  "${PROJECT_SOURCE_DIR}/cmake/setup_KWIVER.sh.in"
  "${PROJECT_BINARY_DIR}/setup_KWIVER.sh"
  @ONLY
  )

configure_file(
  "${PROJECT_SOURCE_DIR}/cmake/setup_KWIVER.bat.in"
  "${PROJECT_BINARY_DIR}/setup_KWIVER.bat"
  @ONLY
  )

if(SPHINX_EXECUTABLE)
  add_subdirectory(doc)
endif()
