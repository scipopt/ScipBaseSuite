cmake_minimum_required(VERSION 3.9)

# version number is taken from SCIP
project(SCIPBaseSuite)

if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-psabi")
   # require at least gcc 5
   if (CMAKE_CXX_COMPILER_VERSION VERSION_LESS 5)
     message(WARNING "GCC version not supported, should be at least 5!")
   endif()
endif()

option(AUTOBUILD "find and use dependencies on availability (ignores IPOPT, ZLIB, GMP, READLINE, flags)" OFF)
set(AUTOBUILD_MSG "\nIf you have troubles configuring, you can consider setting AUTOBUILD=ON to try and find optional packages on availability.")
option(NO_EXTERNAL_CODE "should everything be disabled except the bare minimum necessary for SCIP and SoPLEX" OFF)
option(SOPLEX "should SOPLEX be included" ON)
option(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS "Export all symbols into the DLL" OFF)
set(ZIMPL OFF)

if(NOT NO_EXTERNAL_CODE)
   option(AUTOBUILD "find and use dependencies on availability (ignores IPOPT, ZLIB, GMP, READLINE, flags)" OFF)
   option(GMP "should GMP be linked" ON)
   option(BOOST "Use Boost (required to build the binary). Disable if you only want to build libsoplex." ON)
else()
   option(GMP "should GMP be linked" OFF)
   option(ZLIB "should zlib be linked" OFF)
   option(READLINE "should readline be linked" OFF)
   option(IPOPT "should ipopt be linked" OFF)
   option(BOOST "Use Boost (required to build the binary). Disable if you only want to build libsoplex." OFF)
   option(QUADMATH "should quadmath library be used" OFF)
   option(MPFR "Use MPFR" OFF)
   set(SYM none CACHE STRING "options for symmetry computation")
endif()

if(AUTOBUILD)
    message(WARNING "You are using the automatic build option to find and use dependencies automatically. The following flags are ignored: ZLIB, GMP, READLINE.")
endif() # AUTOBUILD

include_directories(
   soplex/src
   scip/src
   )

# make 'Release' the default build type
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# path to e.g. findGMP module
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake/Modules)
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/scip/cmake/Modules)
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/soplex/cmake/Modules)

add_subdirectory(soplex)
set(SOPLEX_DIR ${CMAKE_BINARY_DIR})

add_subdirectory(scip)

set(CPACK_RESOURCE_FILE_LICENSE "${PROJECT_SOURCE_DIR}/scip/LICENSE")
set(CPACK_PACKAGE_VERSION_MAJOR "${SCIPBaseSuite_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${SCIPBaseSuite_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${SCIPBaseSuite_VERSION_PATCH}")
set(CPACK_PACKAGE_VENDOR "Zuse Institute Berlin")
set(CPACK_NSIS_MODIFY_PATH ON)
set(CPACK_PACKAGE_EXECUTABLES scip;SCIP soplex;SoPlex)
set(CPACK_PACKAGE_CONTACT "SCIP <scip@zib.de>")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Toolbox for generating and solving mixed integer linear (MIP) and nonlinear programs (MINLP) and constraint integer programs (CIP)")
set(CPACK_DEBIAN_PACKAGE_HOMEPAGE "https://scipopt.org")
# autogenerate dependency information
set(CPACK_DEBIAN_PACKAGE_SHLIBDEPS ON)
set(CPACK_DEBIAN_PACKAGE_GENERATE_SHLIBS ON)
set(CPACK_DEBIAN_ENABLE_COMPONENT_DEPENDS ON)
include(CPack)
enable_testing()

if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})
  include(FeatureSummary)
  feature_summary(WHAT ALL)
endif()

