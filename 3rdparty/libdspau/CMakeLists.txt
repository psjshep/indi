project(libdspau C)
cmake_minimum_required(VERSION 2.4.7)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../../cmake_modules/")
include(GNUInstallDirs)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wno-error -std=c99")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-error")
set(PACKAGE_VERSION "2.0.0")

find_package(TIFF REQUIRED)
find_package(FFTW3 REQUIRED)
find_package(Threads REQUIRED)

add_definitions(-Wall -O2 -std=gnu99)

include_directories( ${CMAKE_CURRENT_SOURCE_DIR}/include)

if (APPLE)
    ##This one is needed for homebrew
include_directories( "/usr/local/include")
    ## This one is needed for Craft
include_directories("${CMAKE_INSTALL_PREFIX}/include")
endif(APPLE)

set(dspau_lib_srcs
   ${CMAKE_CURRENT_SOURCE_DIR}/src/libdspau-align.c
   ${CMAKE_CURRENT_SOURCE_DIR}/src/libdspau-astro.c
   ${CMAKE_CURRENT_SOURCE_DIR}/src/libdspau-buffer.c
   ${CMAKE_CURRENT_SOURCE_DIR}/src/libdspau-convert.c
   ${CMAKE_CURRENT_SOURCE_DIR}/src/libdspau-interferometry.c
   ${CMAKE_CURRENT_SOURCE_DIR}/src/libdspau-fft.c
   ${CMAKE_CURRENT_SOURCE_DIR}/src/libdspau-filters.c
   ${CMAKE_CURRENT_SOURCE_DIR}/src/libdspau-stats.c
   ${CMAKE_CURRENT_SOURCE_DIR}/src/libdspau-stream.c
   ${CMAKE_CURRENT_SOURCE_DIR}/src/libdspau-time.c
)

#build the shared library
add_library(dspau SHARED ${dspau_lib_srcs})

set_target_properties(dspau PROPERTIES VERSION ${PACKAGE_VERSION} SOVERSION 2)

#need to link to some other libraries ? just add them here
target_link_libraries(dspau ${M_LIB} ${CMAKE_THREAD_LIBS_INIT} ${FFTW3_LIBRARIES})

#add an install target here
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/include/libdspau.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
install(TARGETS dspau LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
SET( links ${CMAKE_INSTALL_PREFIX}/libdspau.so)
SET( links_target ${CMAKE_INSTALL_PREFIX}/libdspau.so.${PACKAGE_VERSION})
ADD_CUSTOM_COMMAND( OUTPUT ${links}
                     COMMAND ln -s ${links_target} ${links}
                     DEPENDS install ${links_target}
                     COMMENT "")

ADD_CUSTOM_TARGET( install_links DEPENDS ${links} )
