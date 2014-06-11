#The name of the project
PROJECT(Demo)
message(STATUS "==============================Project: ${PROJECT_NAME} ==================================")
include(PrintListMacro)


#include directories
set(INCLUDE_DIRS 
${COMMON_INC_DIR}
)

include_directories(${INCLUDE_DIRS})
PRINTLIST("Include Directories are:" "${INCLUDE_DIRS}")

#link directories
set(LINK_DIRS
)
link_directories(${LINK_DIRS})
PRINTLIST("Link directories are:" "${LINK_DIRS}")
set(LINKED_LIBS )
PRINTLIST("Linked libraries are:" "${LINKED_LIBS}")


set(SOURCE_FILES 
    ${COMMON_SRC}
    demo.cu
)

set(INCLUDE_FILES
    ${COMMON_INC}
) 


SET(CMAKE_DEBUG_POSTFIX "D")

set_source_files_properties( INCLUDE_FILES PROPERTIES HEADER_FILE_ONLY TRUE)

#The rule to build the executable program
CUDA_ADD_EXECUTABLE(${PROJECT_NAME} ${SOURCE_FILES} ${INCLUDE_FILES} )
target_link_libraries(${PROJECT_NAME}  ${LINKED_LIBS})

message(STATUS "=========================================================================")
