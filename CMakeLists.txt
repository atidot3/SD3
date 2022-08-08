set(LIBRARY_NAME SD3)

add_definitions(-DDT_POLYREF64)
add_definitions(-D__ASSERT_MACROS_DEFINE_VERSIONS_WITHOUT_UNDERSCORES=0)

# Find all the input files
FILE(GLOB_RECURSE SD3_LIB_SRCS RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}" "*.h" "*.cpp")

if(NOT BUILD_SCRIPTDEV)
  # exclude script folder for faster build time (used for dev mode)
  set (EXCLUDE_DIR "ScriptDev/scripts/")
  foreach (TMP_PATH ${SD3_LIB_SRCS})
      string (FIND ${TMP_PATH} ${EXCLUDE_DIR} EXCLUDE_DIR_FOUND)
      if (NOT ${EXCLUDE_DIR_FOUND} EQUAL -1)
          list(REMOVE_ITEM SD3_LIB_SRCS ${TMP_PATH})
      endif ()
  endforeach()
endif()

# Create visual studio groups using folder structure
foreach(source ${SD3_LIB_SRCS})
    get_filename_component(source_path "${source}" PATH)
    string(REPLACE "/" "\\" source_path_msvc "${source_path}")
    source_group("${source_path_msvc}" FILES "${source}")
endforeach()

set(PCH_BASE_FILENAME "pch")
# exclude pchdef files
set (EXCLUDE_FILE "${PCH_BASE_FILENAME}")
foreach (TMP_PATH ${LIBRARY_SRCS})
    string (FIND ${TMP_PATH} ${EXCLUDE_FILE} EXCLUDE_FILE_FOUND)
    if (NOT ${EXCLUDE_FILE_FOUND} EQUAL -1)
        list(REMOVE_ITEM LIBRARY_SRCS ${TMP_PATH})
    endif ()
endforeach()

# set pch file names
set(PCH_FILE_HEADER "${PCH_BASE_FILENAME}.h")
set(PCH_FILE_SOURCE "${PCH_BASE_FILENAME}.cpp")

add_library(${LIBRARY_NAME} STATIC ${SD3_LIB_SRCS})

target_include_directories(${LIBRARY_NAME}
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${CMAKE_CURRENT_SOURCE_DIR}/base
        ${CMAKE_CURRENT_SOURCE_DIR}/include
)

target_link_libraries(${LIBRARY_NAME}
	PUBLIC game
	PRIVATE	g3dlite shared detour
)

# Generate PCH
if(PCH)
    target_precompile_headers(${LIBRARY_NAME} PRIVATE ${PCH_FILE_HEADER})
endif()
