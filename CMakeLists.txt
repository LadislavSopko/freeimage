####### FREEIMAGE
PROJECT(FreeImage)
## Minimum required version of CMake to configure the project
cmake_minimum_required (VERSION 2.8)

## Enforced CMake policy
cmake_policy (VERSION 2.8)


set (TRERDP_ROOT "${CMAKE_INSTALL_PREFIX}")
set (FREEIMAGE_DIR "${CMAKE_CURRENT_SOURCE_DIR}")
set (ZLIB_ROOT "${TRERDP_ROOT}")
include( cmake/FindPNG.cmake )


#########################################################
#
#       string macros 
#
########################################################

# Replace slashes by backslashes in a string.
macro (slashes_to_backslashes output input)
    string (REGEX REPLACE "/" "\\\\" ${output} ${input})
endmacro ()

# Filter a list of items using a regular expression.
macro (filter_list output_list input_list regex)
    foreach (item ${input_list})
        if (${item} MATCHES ${regex})
            list (APPEND ${output_list} ${item})
        endif ()
    endforeach ()
endmacro ()

# Convert a semicolon-separated list to a whitespace-separated string.
macro (convert_list_to_string output_string input_list)
    foreach (item ${input_list})
        if (DEFINED ${output_string})
            set (${output_string} "${${output_string}} ${item}")
        else ()
            set (${output_string} "${item}")
        endif ()
    endforeach ()
endmacro ()

# Assign a whitespace-separated string to a variable, given a list.
macro (set_to_string output_variable first_arg)
    set (arg_list ${first_arg} ${ARGN})
    convert_list_to_string (${output_variable} "${arg_list}")
endmacro ()


if (CMAKE_SIZEOF_VOID_P EQUAL 8)
	
	# All configurations.
	set (preprocessor_definitions_common
		WIN32
		_WIN64
		_CRT_SECURE_NO_WARNINGS
		_CRT_NONSTDC_NO_DEPRECATE
		_SCL_SECURE_NO_WARNINGS
		FREEIMAGE_EXPORTS
		OPJ_STATIC
		USE_JPIP
		LIBRAW_BUILDLIB
		MNG_BUILD_SO
		FIP_EXPORTS
		NO_JASPER
		LIBRAW_NOTHREADS
	)
	
else()
	# All configurations.
	set (preprocessor_definitions_common
		WIN32
		_USE_32BIT_TIME_T
		_CRT_SECURE_NO_WARNINGS
		_CRT_NONSTDC_NO_DEPRECATE
		_SCL_SECURE_NO_WARNINGS
		FREEIMAGE_EXPORTS
		OPJ_STATIC
		USE_JPIP
		LIBRAW_BUILDLIB
		MNG_BUILD_SO
		FIP_EXPORTS
		NO_JASPER
		LIBRAW_NOTHREADS
		
	)

endif()



# Debug configuration.
set (preprocessor_definitions_debug
)

# Release configuration.
set (preprocessor_definitions_release
)




#--------------------------------------------------------------------------------------------------
# Compilation/linking flags.
#--------------------------------------------------------------------------------------------------

# All configurations.
set (compiler_flags_common
    /MP                                     # Multi-processor Compilation
    /GF                                     # Enable String Pooling

)
if (CMAKE_SIZEOF_VOID_P EQUAL 8)
    set (compiler_flags_common
        ${compiler_flags_common}
		/W4
		/WX-
		/Zp8
		
    )
else ()
    set (compiler_flags_common
        ${compiler_flags_common}
        /W3                                 # Treat Warnings 3
		/WX-
		/Zp4
    )
endif ()

set (exe_linker_flags_common
    /WX										# Treat Warnings as errors
)
set (shared_lib_linker_flags_common
    ${exe_linker_flags_common}
    /OPT:NOREF
)

# Debug configuration.
if (CMAKE_SIZEOF_VOID_P EQUAL 8)
    set (compiler_flags_debug
        /Zi		# set Debug Information Format to Program Database
		-wd4820 -wd4668 -wd4121 -wd4100 -wd4640 -wd4127 -wd4710 -wd4548 -wd4480 -wd4505 -wd4201 -wd4512 -wd4189 -wd4706 
    )
else ()
    set (compiler_flags_debug
        /ZI                                 # set Debug Information Format to Program Database for Edit & Continue
		-wd4820 -wd4668 -wd4121 -wd4100 -wd4640 -wd4127 -wd4710 -wd4548 -wd4480 -wd4505 -wd4201 -wd4512 -wd4189 -wd4706 
    )
endif ()

set (compiler_flags_debug
    ${compiler_flags_debug}
    /MDd                                    # set Runtime Library to Multi-threaded Debug DLL
)

# Release configuration.
set (compiler_flags_release
    /Zi                                     # set Debug Information Format to Program Database
    /Ox                                     # Full Optimization
    /Ob2                                    # set Inline Function Expansion to Any Suitable
    /Oi                                     # Enable Intrinsic Functions
    /Ot                                     # Favor Fast Code
    /Oy                                     # Omit Frame Pointers
    /MD                                     # set Runtime Library to Multi-threaded DLL
	/EHsc
)
if (CMAKE_SIZEOF_VOID_P EQUAL 4)
    set (compiler_flags_release
        ${compiler_flags_release}
        /fp:fast                            # set Floating Point Model to Fast, in 32-bit builds only because of a bug in VS 2010:
    )                                       # http://connect.microsoft.com/VisualStudio/feedback/details/518015/nan-comparison-under-the-64-bit-compiler-is-incorrect
endif ()


# Release configuration for warnings.
if (CMAKE_SIZEOF_VOID_P EQUAL 8)
    set (compiler_flags_release
        ${compiler_flags_release}
        -wd4820 -wd4668 -wd4121 -wd4100 -wd4640 -wd4127 -wd4710 -wd4548 -wd4480 -wd4505 -wd4201 -wd4512 -wd4189 -wd4706 
    )
else ()
    set (compiler_flags_release
        ${compiler_flags_release}                               # set Debug Information Format to Program Database for Edit & Continue
		-wd4820 -wd4668 -wd4121 -wd4100 -wd4640 -wd4127 -wd4710 -wd4548 -wd4480 -wd4505 -wd4201 -wd4512 -wd4189 -wd4706 
    )
endif ()


set (exe_linker_flags_release
)
set (shared_lib_linker_flags_release
    ${exe_linker_flags_release}
)

# Ship configuration.
set (compiler_flags_ship
    /GL                                     # Enable link-time code generation
)
set (exe_linker_flags_ship
    /LTCG                                   # Use Link Time Code Generation
    /INCREMENTAL:NO                         # Disable Incremental Linking
)
set (shared_lib_linker_flags_ship
    ${exe_linker_flags_ship}
)

# Profile configuration.
set (exe_linker_flags_profile
    /DEBUG                                  # Generate Debug Info
)
set (shared_lib_linker_flags_profile
    ${exe_linker_flags_profile}
)


# All configurations.
set (preprocessor_definitions_common
    ${preprocessor_definitions_common}
)
if (CMAKE_SIZEOF_VOID_P MATCHES 4)
    set (preprocessor_definitions_common
        ${preprocessor_definitions_common}
        ARCH32
)
elseif (CMAKE_SIZEOF_VOID_P MATCHES 8)
    set (preprocessor_definitions_common
        ${preprocessor_definitions_common}
        ARCH64
)
else ()
    message (FATAL_ERROR "Cannot determine pointer size")
endif ()

# Debug configuration.
set (preprocessor_definitions_debug
    ${preprocessor_definitions_debug}
    ${preprocessor_definitions_common}
    DEBUG
    _DEBUG
)

# Release configuration.
set (preprocessor_definitions_release
    ${preprocessor_definitions_release}
    ${preprocessor_definitions_common}
    NDEBUG
)


# A convenience macro to apply the preprocessor definitions to all configurations of a given target.
macro (apply_preprocessor_definitions target)
    set_property (TARGET ${target} APPEND PROPERTY
        #  COMPILE_DEFINITIONS_DEBUG ${preprocessor_definitions_debug}
        COMPILE_DEFINITIONS $<$<CONFIG:Debug>:${preprocessor_definitions_debug}>
    )
    set_property (TARGET ${target} APPEND PROPERTY
        # COMPILE_DEFINITIONS_RELEASE ${preprocessor_definitions_release}
        COMPILE_DEFINITIONS $<$<CONFIG:Release>:${preprocessor_definitions_release}>
    )    
endmacro ()


#--------------------------------------------------------------------------------------------------
# Compilation/linking flags.
#--------------------------------------------------------------------------------------------------

# All configurations.
set (compiler_flags_common
    ${compiler_flags_common}
)
set (exe_linker_flags_common
    ${exe_linker_flags_common}
)
set (shared_lib_linker_flags_common
    ${shared_lib_linker_flags_common}
)

# Debug configuration.
set (compiler_flags_debug
    ${compiler_flags_debug}
    ${compiler_flags_common}
)
set (exe_linker_flags_debug
    ${exe_linker_flags_debug}
    ${exe_linker_flags_common}
)
set (shared_lib_linker_flags_debug
    ${shared_lib_linker_flags_debug}
    ${shared_lib_linker_flags_common}
)

# Release configuration.
set (compiler_flags_release
    ${compiler_flags_release}
    ${compiler_flags_common}
)
set (exe_linker_flags_release
    ${exe_linker_flags_release}
    ${exe_linker_flags_common}
)
set (shared_lib_linker_flags_release
    ${shared_lib_linker_flags_release}
    ${shared_lib_linker_flags_common}
)



# Apply Debug configuration compilation/linking flags.
convert_list_to_string (CMAKE_CXX_FLAGS_DEBUG "${compiler_flags_debug}")
convert_list_to_string (CMAKE_EXE_LINKER_FLAGS_DEBUG "${exe_linker_flags_debug}")
convert_list_to_string (CMAKE_SHARED_LINKER_FLAGS_DEBUG "${shared_lib_linker_flags_debug}")

# Apply Release configuration compilation/linking flags.
convert_list_to_string (CMAKE_CXX_FLAGS_RELEASE "${compiler_flags_release}")
convert_list_to_string (CMAKE_EXE_LINKER_FLAGS_RELEASE "${exe_linker_flags_release}")
convert_list_to_string (CMAKE_SHARED_LINKER_FLAGS_RELEASE "${shared_lib_linker_flags_release}")



link_directories("${TRERDP_ROOT}/lib")



IF(CMAKE_COMPILER_IS_GNUCXX)
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fpermissive")
  LINK_LIBRARIES(Ws2_32)
ENDIF()

#IF(NOT BUNDLE_SHARED_LIBRARIES)
#ADD_DEFINITIONS("-DFREEIMAGE_LIB")
#ENDIF()

find_package(zlib)

include_directories (${ZLIB_INCLUDE_DIR})

#INCLUDE_DIRECTORIES(${LIBPNG_DIR})
#INCLUDE_DIRECTORIES(${ZLIB_DIR})
INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/)
INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/librawlite)
INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/OpenEXR/Imath)
INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/OpenEXR/IlmImf)
INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/OpenEXR/Iex)
INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/OpenEXR/IexMath)
INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/OpenEXR/half)
INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/OpenEXR/IlmThread)
INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/OpenEXR/)
#INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/LibJXR/jxrgluelib)
#INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/LibJXR/common/include)
#INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/LibJXR/image/sys)
INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Source/LibJPEG)
INCLUDE_DIRECTORIES(${FREEIMAGE_DIR}/Wrapper/FreeImagePlus/)

FILE(GLOB png_files ${LIBPNG_DIR}/*.c)
FILE(GLOB zlib_files ${ZLIB_DIR}/*.c)
FILE(GLOB deprecation_files ${FREEIMAGE_DIR}/Source/DeprecationManager/*.cpp)
FILE(GLOB_RECURSE freeimage_files ${FREEIMAGE_DIR}/Source/FreeImage/*.cpp )
FILE(GLOB_RECURSE freeimage_plus_files ${FREEIMAGE_DIR}/Wrapper/FreeImagePlus/src/*.cpp )
FILE(GLOB_RECURSE freeimage_c_files ${FREEIMAGE_DIR}/Source/FreeImage/*.c )
FILE(GLOB freeimagetoolkit_files ${FREEIMAGE_DIR}/Source/FreeImageToolkit/*.cpp )
FILE(GLOB libjpeg_files ${FREEIMAGE_DIR}/Source/LibJPEG/j*.c ${FREEIMAGE_DIR}/Source/LibJPEG/transupp.c)
FILE(GLOB libmng_files ${FREEIMAGE_DIR}/Source/LibMNG/*.c)
FILE(GLOB libopenjpeg_files ${FREEIMAGE_DIR}/Source/LibOpenJPEG/*.c)
FILE(GLOB_RECURSE libraw_files ${FREEIMAGE_DIR}/Source/LibRawLite/src/*.cpp)
LIST(APPEND libraw_files ${FREEIMAGE_DIR}/Source/LibRawLite/internal/demosaic_packs.cpp ${FREEIMAGE_DIR}/Source/LibRawLite/internal/dcraw_common.cpp ${FREEIMAGE_DIR}/Source/LibRawLite/internal/dcraw_fileio.cpp)
FILE(GLOB libtiff_files ${FREEIMAGE_DIR}/Source/LibTIFF4/*.c)
FILE(GLOB metadata_files ${FREEIMAGE_DIR}/Source/metadata/*.cpp)
FILE(GLOB_RECURSE openexr_files ${FREEIMAGE_DIR}/Source/OpenEXR/*.cpp)
#FILE(GLOB_RECURSE jxr_files ${FREEIMAGE_DIR}/Source/LibJXR/*.c)
FILE(GLOB_RECURSE webp_files ${FREEIMAGE_DIR}/Source/LibWebP/*.c)

LIST(REMOVE_ITEM zlib_files "${ZLIB_DIR}/example.c")
LIST(REMOVE_ITEM zlib_files "${ZLIB_DIR}/minigzip.c")
LIST(REMOVE_ITEM png_files "${LIBPNG_DIR}/pngtest.c")

LIST(REMOVE_ITEM libjpeg_files "${FREEIMAGE_DIR}/Source/LibJPEG/jpegtran.c")
LIST(REMOVE_ITEM libjpeg_files "${FREEIMAGE_DIR}/Source/LibJPEG/jmemdos.c")
LIST(REMOVE_ITEM libjpeg_files "${FREEIMAGE_DIR}/Source/LibJPEG/jmemmac.c")
LIST(REMOVE_ITEM libjpeg_files "${FREEIMAGE_DIR}/Source/LibJPEG/jmemansi.c")
LIST(REMOVE_ITEM libjpeg_files "${FREEIMAGE_DIR}/Source/LibJPEG/jmemnobs.c")
LIST(REMOVE_ITEM libjpeg_files "${FREEIMAGE_DIR}/Source/LibJPEG/example.c")
LIST(REMOVE_ITEM libopenjpeg_files "${FREEIMAGE_DIR}/Source/LibOpenJPEG/t1_generate_luts.c")
LIST(REMOVE_ITEM libtiff_files "${FREEIMAGE_DIR}/Source/LibTIFF4/tif_unix.c")
LIST(REMOVE_ITEM libtiff_files "${FREEIMAGE_DIR}/Source/LibTIFF4/tif_acorn.c")
LIST(REMOVE_ITEM libtiff_files "${FREEIMAGE_DIR}/Source/LibTIFF4/mkg3states.c")
LIST(REMOVE_ITEM libtiff_files "${FREEIMAGE_DIR}/Source/LibTIFF4/tif_apple.c")
LIST(REMOVE_ITEM libtiff_files "${FREEIMAGE_DIR}/Source/LibTIFF4/tif_atari.c")
LIST(REMOVE_ITEM libtiff_files "${FREEIMAGE_DIR}/Source/LibTIFF4/tif_vms.c")
LIST(REMOVE_ITEM libtiff_files "${FREEIMAGE_DIR}/Source/LibTIFF4/tif_win3.c")
LIST(REMOVE_ITEM libtiff_files "${FREEIMAGE_DIR}/Source/LibTIFF4/tif_wince.c")
LIST(REMOVE_ITEM libtiff_files "${FREEIMAGE_DIR}/Source/LibTIFF4/tif_win32.c")
LIST(REMOVE_ITEM libtiff_files "${FREEIMAGE_DIR}/Source/LibTIFF4/tif_msdos.c")
LIST(REMOVE_ITEM libtiff_files "${FREEIMAGE_DIR}/Source/LibTIFF4/mkspans.c")
LIST(REMOVE_ITEM openexr_files "${FREEIMAGE_DIR}/Source/OpenEXR/IlmThread/IlmThreadMutexWin32.cpp")
LIST(REMOVE_ITEM openexr_files "${FREEIMAGE_DIR}/Source/OpenEXR/IlmThread/IlmThreadWin32.cpp")
LIST(REMOVE_ITEM openexr_files "${FREEIMAGE_DIR}/Source/OpenEXR/IlmThread/IlmThreadSemaphoreWin32.cpp")
LIST(REMOVE_ITEM openexr_files "${FREEIMAGE_DIR}/Source/OpenEXR/Half/eLut.cpp")
LIST(REMOVE_ITEM openexr_files "${FREEIMAGE_DIR}/Source/OpenEXR/Half/toFloat.cpp")
LIST(REMOVE_ITEM openexr_files "${FREEIMAGE_DIR}/Source/OpenEXR/IlmImf/b44ExpLogTable.cpp")
LIST(REMOVE_ITEM freeimage_files "${FREEIMAGE_DIR}/Source/FreeImage/PluginJXR.cpp")



ADD_LIBRARY (FreeImage SHARED ${BUNDLE_LIB_MODIFIER} ${png_files} ${zlib_files} ${deprecation_files} 
			${freeimage_files} ${freeimage_c_files} ${freeimagetoolkit_files} ${freeimagelib_files} ${libjpeg_files}
			${libmng_files} ${libopenjpeg_files} ${libraw_files}
			${libtiff_files} ${metadata_files} ${openexr_files} 
			#${jxr_files} 
			${webp_files}
			)
			
TARGET_LINK_LIBRARIES( FreeImage
		${PNG_LIBRARIES}
)
apply_preprocessor_definitions (FreeImage)

FILE(GLOB freeimage_headers ${FREEIMAGE_DIR}/Source/*.h)
FILE(GLOB fip_headers ${FREEIMAGE_DIR}/Wrapper/FreeImagePlus/*.h)
INSTALL(FILES ${fip_headers} ${freeimage_headers} DESTINATION ${TRERDP_ROOT}/include/FreeImage)

INSTALL(TARGETS FreeImage
		ARCHIVE DESTINATION  "${TRERDP_ROOT}/lib/" CONFIGURATIONS Debug Release MinSizeRel RelWithDebInfo
		LIBRARY DESTINATION  "${TRERDP_ROOT}/lib/" CONFIGURATIONS Debug Release MinSizeRel RelWithDebInfo
		RUNTIME DESTINATION  "${TRERDP_ROOT}/bin/" CONFIGURATIONS Debug Release MinSizeRel RelWithDebInfo
		)

# FreeImagePlus
FILE(GLOB freeimagelib_files ${FREEIMAGE_DIR}/Source/FreeImageLib/*.cpp)
LIST(REMOVE_ITEM freeimage_plus_files "${FREEIMAGE_DIR}/Wrapper/FreeImagePlus/src/FreeImagePlus.cpp")

ADD_LIBRARY (FreeImagePlus SHARED ${BUNDLE_LIB_MODIFIER} ${freeimage_plus_files})
TARGET_LINK_LIBRARIES(FreeImagePlus FreeImage)
apply_preprocessor_definitions (FreeImagePlus)

INSTALL(TARGETS FreeImagePlus
		ARCHIVE DESTINATION  "${TRERDP_ROOT}/lib/" CONFIGURATIONS Debug Release MinSizeRel RelWithDebInfo
		LIBRARY DESTINATION  "${TRERDP_ROOT}/lib/" CONFIGURATIONS Debug Release MinSizeRel RelWithDebInfo
		RUNTIME DESTINATION  "${TRERDP_ROOT}/bin/" CONFIGURATIONS Debug Release MinSizeRel RelWithDebInfo
		)
		