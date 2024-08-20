cmake_minimum_required(VERSION 3.20)

project(DMGraphCtrl CXX)

set(CMAKE_CXX_STANDARD 14)

# set output directory
message(STATUS "CMAKE_SOURCE_DIR: ${CMAKE_SOURCE_DIR}")
message(STATUS "PROJECT_SOURCE_DIR: ${PROJECT_SOURCE_DIR}")

if(CMAKE_SOURCE_DIR STREQUAL PROJECT_SOURCE_DIR)
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
    set(CMAKE_PDB_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
endif()

# process CMAKE_BUILD_TYPE
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug CACHE STRING "Choose the type of build." FORCE)
    set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()

set(CMAKE_MODULE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /DEF:${CMAKE_CURRENT_SOURCE_DIR}/DmGraph/DMGraph.def")

set(DMG_SRC
    "DmGraph/about.h"
    "DmGraph/AnnoPropPage.cpp"
    "DmGraph/AnnoPropPage.h"
    "DmGraph/ColPickerBtn.cpp"
    "DmGraph/ColPickerBtn.h"
    "DmGraph/CursorPropPage.cpp"
    "DmGraph/CursorPropPage.h"
    "DmGraph/DMGraph.cpp"
    "DmGraph/DMGraph.def"
    "DmGraph/DMGraph.idl"
    "DmGraph/DMGraphCtrl.cpp"
    "DmGraph/DMGraphCtrl.h"
    "DmGraph/DMGraphPropPage.cpp"
    "DmGraph/DMGraphPropPage.h"
    "DmGraph/DMGraphVer.h"
    "DmGraph/DrawFct.cpp"
    "DmGraph/DrawFct.h"
    "DmGraph/ElementPoint.h"
    "DmGraph/ElementPropPage.h"
    "DmGraph/ElementPropPage.cpp"
    "DmGraph/FormatPropPage.cpp"
    "DmGraph/FormatPropPage.h"
    "DmGraph/GraphAnno.cpp"
    "DmGraph/GraphAnno.h"
    "DmGraph/GraphAxis.cpp"
    "DmGraph/GraphAxis.h"
    "DmGraph/GraphCollection.cpp"
    "DmGraph/GraphCollection.h"
    "DmGraph/GraphCursor.cpp"
    "DmGraph/GraphCursor.h"
    "DmGraph/GraphElement.cpp"
    "DmGraph/GraphElement.h"
    "DmGraph/GraphItem.h"
    "DmGraph/GraphItems.cpp"
    "DmGraph/GraphItems.h"
    "DmGraph/PropPageBaseWnd.cpp"
    "DmGraph/PropPageBaseWnd.h"
    "DmGraph/RectTracker.cpp"
    "DmGraph/RectTracker.h"
    "DmGraph/resource.h"
    "DmGraph/StdAfx.cpp"
    "DmGraph/StdAfx.h"
    "DmGraph/DMGraph.idl"
    "DmGraph/DMGraph.def"
    "DmGraph/DMGraph.rc"
)

set(DMG_RES
    "DmGraph/DmGraph.bmp"
    "DmGraph/Frame.bmp"
    "DmGraph/DmGraph.rc2"
    "DmGraph/GraphCtrl.ico"
    "DmGraph/AnnoPropPage.rgs"
    "DmGraph/DMGraphCtrl.rgs"
    "DmGraph/ElementPropPage.rgs"
    "DmGraph/CursorPropPage.rgs"
    "DmGraph/DMGraphPropPage.rgs"
    "DmGraph/FormatPropPage.rgs"
)

add_library(DMGraph MODULE ${DMG_SRC})
target_link_libraries(DMGraph PRIVATE comctl32) # comctl32
target_include_directories(DMGraph PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/DmGraph)
target_compile_definitions(DMGraph PRIVATE _WINDOWS _USRDLL _WINDLL _UNICODE)

set_target_properties(DMGraph PROPERTIES SUFFIX ".dll")
set_source_files_properties(DmGraph/DMGraph.def PROPERTIES HEADER_FILE_ONLY TRUE)
source_group("Resource Files" FILES ${RES})

set(MIDL_OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/DmGraph/DMGraph.tlb)
set(MIDL_FILE ${CMAKE_CURRENT_LIST_DIR}/DmGraph/DMGraph.idl)
set(MIDI_OUTPUT_HEADER ${CMAKE_CURRENT_SOURCE_DIR}/DmGraph/DMGraph.h)
set(MIDI_OUTPUT_IID ${CMAKE_CURRENT_SOURCE_DIR}/DmGraph/DMGraph_i.c)
add_custom_command(
    OUTPUT ${MIDL_OUTPUT}
    COMMAND midl ${MIDL_FILE} /tlb ${MIDL_OUTPUT} /h ${MIDI_OUTPUT_HEADER} /iid ${MIDI_OUTPUT_IID}
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
    DEPENDS ${MIDL_FILE}
    MAIN_DEPENDENCY ${MIDL_FILE}
    VERBATIM
)

set_target_properties(DMGraph PROPERTIES LINK_FLAGS "/SUBSYSTEM:WINDOWS")

# add custom command to generate tlb file
add_custom_target(DMGraphTlb DEPENDS ${MIDL_OUTPUT})

# copy library to bin directory
# not equal means this is the root project
if(NOT CMAKE_SOURCE_DIR STREQUAL PROJECT_SOURCE_DIR)
    add_custom_command(TARGET DMGraph POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${CMAKE_BINARY_DIR}/lib/${CMAKE_BUILD_TYPE}/dmgraph.dll ${CMAKE_BINARY_DIR}/bin/${CMAKE_BUILD_TYPE}/dmgraph.dll)
endif()

# ##############################################################################
# build DMGraphTest
set(TestSrc
    "DmGraphTest/DmGraphTest.cpp"
    "DmGraphTest/DmGraphTest.rc"
    "DmGraphTest/stdafx.cpp"
    "DmGraphTest/stdafx.h"
    "DmGraphTest/resource.h"
    "DmGraphTest/DMGraphTestVer.h"
    "DmGraphTest/GraphDataDlg.cpp"
    "DmGraphTest/GraphDataDlg.h"
    "DmGraphTest/MainWnd.cpp"
    "DmGraphTest/MainWnd.h"
    "DmGraphTest/PointData.cpp"
    "DmGraphTest/PointData.h"
    "DmGraphTest/ToolBar.h"
)

set(TestRes
    "DmGraphTest/DmGraphTest.rc2"
    "DmGraphTest/Hand.cur"
    "DmGraphTest/main.bmp"
    "DmGraphTest/main.ico"
)

add_executable(DMGraphTest WIN32 ${TestSrc} ${TestRes})
add_dependencies(DMGraphTest DMGraph)
target_sources(DMGraphTest PRIVATE ${TestRes})
source_group("Resource" FILES ${TestRes})
set_target_properties(DMGraphTest PROPERTIES LINK_FLAGS "/SUBSYSTEM:WINDOWS")
target_link_libraries(DMGraphTest PRIVATE ole32 uuid oleaut32 comctl32)