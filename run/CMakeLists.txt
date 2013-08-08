ADD_CUSTOM_TARGET( run )
INCLUDE_DIRECTORIES(include ${ROOT_INCLUDE_DIRS})

set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -g -Wall")

MACRO( ADD_EXE_CHECK _name )
  ADD_EXECUTABLE( ${_name} ${ARGN} )
  set_target_properties(${_name} PROPERTIES LINK_SEARCH_END_STATIC 1)
  set_target_properties(${_name} PROPERTIES LINK_SEARCH_START_STATIC 1)
  set(CMAKE_EXE_LINKER_FLAGS "-static-libgcc -static-libstdc++")
  TARGET_LINK_LIBRARIES( ${_name} ${PROJECT_NAME} 
    ${HEPMC_LIBRARIES} 
    ${LHAPDF_LIBRARIES} 
    ${PYTHIA8_LIBRARIES} 
    )
  INSTALL( TARGETS ${_name} DESTINATION ${PROJECT_SOURCE_DIR}/bin )
ENDMACRO()

ADD_EXE_CHECK( lcio-pythia main.cc )
