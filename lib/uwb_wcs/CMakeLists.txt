project(wcs VERSION ${VERSION} LANGUAGES C)

file(GLOB ${PROJECT_NAME}_SOURCES 
    src/*.c
)
file(GLOB ${PROJECT_NAME}_HEADERS 
    include/*.h
)

find_package(timescale REQUIRED)

include_directories(
    include/
    "${PROJECT_SOURCE_DIR}/../../bin/targets/syscfg/generated/include/"
    "${PROJECT_SOURCE_DIR}/../../../porting/dpl_hal/include"
)

source_group("include" FILES ${${PROJECT_NAME}_HEADERS})
source_group("lib" FILES ${${PROJECT_NAME}_SOURCES})

add_library(${PROJECT_NAME} 
    STATIC
    ${${PROJECT_NAME}_SOURCES} 
    ${${PROJECT_NAME}_HEADERS}
)

add_library(libdpl_os ALIAS dpl_os)
get_target_property(libdpl_os_INCLUDE_DIRECTORIES libdpl_os INCLUDE_DIRECTORIES)
add_library(libuwb_dw1000 ALIAS uwb_dw1000)
get_target_property(libuwb_dw1000_INCLUDE_DIRECTORIES libuwb_dw1000 INCLUDE_DIRECTORIES)
add_library(libccp ALIAS ccp)
get_target_property(libccp_INCLUDE_DIRECTORIES libccp INCLUDE_DIRECTORIES)

include(GNUInstallDirs)
target_include_directories(${PROJECT_NAME} 
    PUBLIC 
      $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/>
      $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/>
    PRIVATE
      ${CMAKE_CURRENT_SOURCE_DIR}/src
      PRIVATE ${libdpl_os_INCLUDE_DIRECTORIES}
      PRIVATE ${libuwb_dw1000_INCLUDE_DIRECTORIES}
      PRIVATE ${libccp_INCLUDE_DIRECTORIES}
)

target_link_libraries(
    ${PROJECT_NAME} 
    timescale::timescale
)

# Install library
install(DIRECTORY include/ DESTINATION include/
        FILES_MATCHING PATTERN "*.h"
)

include(../../CMakeCommon.cmake)




