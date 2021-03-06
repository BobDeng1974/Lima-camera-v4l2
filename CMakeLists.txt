###########################################################################
# This file is part of LImA, a Library for Image Acquisition
#
#  Copyright (C) : 2009-2017
#  European Synchrotron Radiation Facility
#  CS40220 38043 Grenoble Cedex 9 
#  FRANCE
# 
#  Contact: lima@esrf.fr
# 
#  This is free software; you can redistribute it and/or modify
#  it under the terms of the GNU General Public License as published by
#  the Free Software Foundation; either version 3 of the License, or
#  (at your option) any later version.
# 
#  This software is distributed in the hope that it will be useful,
#  but WITHOUT ANY WARRANTY; without even the implied warranty of
#  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#  GNU General Public License for more details.
# 
#  You should have received a copy of the GNU General Public License
#  along with this program; if not, see <http://www.gnu.org/licenses/>.
############################################################################
set(NAME "v4l2")

set(CMAKE_SHARED_LINKER_FLAGS "-Wl,--add-needed")

#SOURCEs
set(${NAME}_srcs src/V4L2Camera.cpp  src/V4L2Interface.cpp
	 src/V4L2DetInfoCtrlObj.cpp src/V4L2SyncCtrlObj.cpp
	 src/V4L2VideoCtrlObj.cpp)

add_library(lima${NAME} SHARED ${${NAME}_srcs})

#INCLUDEs
target_include_directories(lima${NAME} PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include")

#LIBRARIES
find_library(V4L2_LIB v4l2)
if("${V4L2_LIB}" STREQUAL "V4L2_LIB-NOTFOUND")
  message(FATAL_ERROR "${NAME} : libv4l2 not found, you can search it in cmake-gui or try to install it if it isn't here.")
endif()

target_link_libraries(lima${NAME} limacore)
target_link_libraries(lima${NAME} ${V4L2_LIB})

limatools_set_library_soversion(lima${NAME} "VERSION")
include(GNUInstallDirs)
install(TARGETS lima${NAME} LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})

if(LIMA_ENABLE_PYTHON)
    limatools_run_sip_for_camera(${NAME})
    install(FILES python/__init__.py DESTINATION "${PYTHON_SITE_PACKAGES_DIR}/Lima/V4l2")
endif()
