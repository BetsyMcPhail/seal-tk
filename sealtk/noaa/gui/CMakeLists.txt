# This file is part of SEAL-TK, and is distributed under the OSI-approved BSD
# 3-Clause License. See top-level LICENSE file or
# https://github.com/Kitware/seal-tk/blob/master/LICENSE for details.

include(SEALTKUtils)

sealtk_add_library(sealtk::noaa_gui
  SOURCES
    About.cpp
    Player.cpp
    Resources.cpp
    Window.cpp
    SEALTKBranding.qrc

  HEADERS
    About.hpp
    Player.hpp
    Resources.hpp
    Window.hpp

  PUBLIC_LINK_LIBRARIES
    sealtk::gui
    sealtk::noaa_core
    qtExtensions
    Qt5::Widgets

  EXPORT_HEADER Export.h
  )
