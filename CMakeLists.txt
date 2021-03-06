########################################################
# cmake file for building Marlin example Package
# @author Jan Engels, Desy IT
CMAKE_MINIMUM_REQUIRED(VERSION 2.6 FATAL_ERROR)
########################################################


# project name
PROJECT( mymarlin )


# project version
SET( ${PROJECT_NAME}_VERSION_MAJOR 0 )
SET( ${PROJECT_NAME}_VERSION_MINOR 1 )
SET( ${PROJECT_NAME}_VERSION_PATCH 0 )



### DEPENDENCIES ############################################################

FIND_PACKAGE( ILCUTIL REQUIRED COMPONENTS ILCSOFT_CMAKE_MODULES )

# load default settings from ILCSOFT_CMAKE_MODULES
INCLUDE( ilcsoft_default_settings )


FIND_PACKAGE( Marlin 1.0 REQUIRED ) # minimum required Marlin version
INCLUDE_DIRECTORIES( ${Marlin_INCLUDE_DIRS} )
LINK_LIBRARIES( ${Marlin_LIBRARIES} )
ADD_DEFINITIONS( ${Marlin_DEFINITIONS} )

set (CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR}/cmake-module )
#set (pythia8_DIR "${PYTHIA8}")

FIND_PACKAGE( Pythia8 REQUIRED ) 
INCLUDE_DIRECTORIES( ${PYTHIA8_INCLUDE_DIR} )
LINK_LIBRARIES( ${PYTHIA8_LIBRARIES} )
#ADD_DEFINITIONS( ${ROOT_DEFINITIONS} )

FIND_PACKAGE( HepMC REQUIRED ) 
INCLUDE_DIRECTORIES( ${HEPMC_INCLUDE_DIR} )
LINK_LIBRARIES( ${HEPMC_LIBRARIES} )

FIND_PACKAGE( LHAPDF REQUIRED ) 
INCLUDE_DIRECTORIES( ${LHAPDF_INCLUDE_DIR} )
LINK_LIBRARIES( ${LHAPDF_LIBRARIES} )



MESSAGE( " pythia include dir === " ${PYTHIA8_INCLUDE_DIR})
MESSAGE( " pythia libraries   === " ${PYTHIA8_LIBRARIES})

# optional package
#FIND_PACKAGE( AIDA )
#IF( AIDA_FOUND )
#    INCLUDE_DIRECTORIES( ${AIDA_INCLUDE_DIRS} )
#    LINK_LIBRARIES( ${AIDA_LIBRARIES} )
#    ADD_DEFINITIONS( "-DMARLIN_USE_AIDA" )
#    MESSAGE( STATUS "AIDA -- found" )
#ELSE()
#    MESSAGE( STATUS "AIDA -- not found" )
#ENDIF()



### DOCUMENTATION ###########################################################

OPTION( INSTALL_DOC "Set to OFF to skip build/install Documentation" OFF )

IF( INSTALL_DOC AND EXISTS "${PROJECT_SOURCE_DIR}/doc/CMakeLists.txt" )
    ADD_SUBDIRECTORY( ./doc )
ENDIF()



### LIBRARY #################################################################

# definitions to pass to the compiler
ADD_DEFINITIONS( "-O2 -g -Wall -ansi -pedantic -W -Wshadow -m64" )
ADD_DEFINITIONS( "-Wno-long-long" )

# include directories

MACRO( ADD_FASTSIM_PKG _input_dir )
  MESSAGE( STATUS "Including package: ${_input_dir}" )
  INCLUDE_DIRECTORIES( BEFORE ${_input_dir}/include )
  AUX_SOURCE_DIRECTORY( ${_input_dir}/src FastSim_srcs )
ENDMACRO()

ADD_FASTSIM_PKG( ./pylcio )



ADD_SHARED_LIBRARY( ${PROJECT_NAME} ${library_sources} )
INSTALL_SHARED_LIBRARY( ${PROJECT_NAME} DESTINATION lib )

ADD_SUBDIRECTORY( ./run )

# display some variables and write them to cache
DISPLAY_STD_VARIABLES()

