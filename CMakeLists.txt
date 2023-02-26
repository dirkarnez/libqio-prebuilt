# https://github.com/cycfi/q/tree/master/q_io

# include/q_io
# src

###############################################################################
#  Copyright (c) 2014-2021 Joel de Guzman. All rights reserved.
#
#  Distributed under the MIT License (https://opensource.org/licenses/MIT)
###############################################################################
cmake_minimum_required(VERSION 3.5.1)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

project(libqio)

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang"
      OR "${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -ftemplate-backtrace-limit=0")
   set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wno-deprecated-declarations")
endif()

# option(PA_BUILD_SHARED "Build shared/dynamic library" OFF)
# add_subdirectory(external/portaudio)
# add_subdirectory(external/portmidi)
# set(PORTMIDI_INCLUDE_DIR external/portmidi/pm_common/)
# set(PORTAUDIO_INCLUDE_DIR external/portaudio/include/)
# set(PORTAUDIO_LIBRARY portaudio_static)
# set(PORTMIDI_LIBRARY portmidi)

find_package(PortMidi 2.0.3 EXACT REQUIRED)
if (PortMidi_FOUND)
    message("PortMidi found")
    message("include dir: ${PORTAUDIO_INCLUDE_DIR}")
    message("link libraries: ${PORTAUDIO_LIBRARY}")
else (PortMidi_FOUND)
    message("PortMidi not found")
endif()

find_package(portaudio 19 EXACT REQUIRED)
if (portaudio_FOUND)
    message("portaudio found")
    message("include dir: ${PORTMIDI_INCLUDE_DIR}")
    message("link libraries: ${PORTMIDI_LIBRARY}")
else (portaudio_FOUND)
    message("portaudio not found")
endif()

add_library(libqio
   src/audio_file.cpp
   src/audio_device.cpp
   src/audio_stream.cpp
   src/midi_device.cpp
   src/midi_stream.cpp
)

target_link_libraries(libqio
   libq
   cycfi::infra
   PortMidi::portmidi # ${PORTMIDI_LIBRARY}
   portaudio_static # ${PORTAUDIO_LIBRARY}
)

if (APPLE)
   set(CMAKE_MACOSX_RPATH 1)
   find_library(COREAUDIO_LIBRARY CoreAudio REQUIRED)
   find_library(AUDIOTOOLBOX_LIBRARY AudioToolbox REQUIRED)
   find_library(AUDIOUNIT_LIBRARY AudioUnit REQUIRED)
   find_library(CARBON_LIBRARY Carbon REQUIRED)
   mark_as_advanced(COREAUDIO_LIBRARY AUDIOTOOLBOX_LIBRARY AUDIOUNIT_LIBRARY CARBON_LIBRARY)
endif()

target_include_directories(libqio
   PUBLIC include/
)