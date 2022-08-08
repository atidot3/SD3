set(LIBRARY_NAME SD3)

add_definitions(-DDT_POLYREF64)
add_definitions(-D__ASSERT_MACROS_DEFINE_VERSIONS_WITHOUT_UNDERSCORES=0)

#Add PCH Files
set(PCH_BASE_FILENAME "${CMAKE_CURRENT_SOURCE_DIR}/pch.h")
if(PCH)
    LIST(APPEND (PCH_BASE_FILENAME "${CMAKE_CURRENT_SOURCE_DIR}/pch.cpp"))
endif()

# Define the scriptdev3 library
# set(SD3_LIB_SRCS ${PCH_BASE_FILENAME})

# Find all the input files
FILE(GLOB_RECURSE SD3_LIB_SRCS RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}" "*.h" "*.cpp")

# Create visual studio groups using folder structure
foreach(source ${SD3_LIB_SRCS})
    get_filename_component(source_path "${source}" PATH)
    string(REPLACE "/" "\\" source_path_msvc "${source_path}")
    source_group("${source_path_msvc}" FILES "${source}")
endforeach()

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
