include(CMakePrintHelpers)
cmake_minimum_required(VERSION 3.5.0)
project(reflex VERSION 0.1.0 LANGUAGES C CXX)

if(APPLE)
  execute_process(
        COMMAND brew --prefix       # get the brew prefix
        RESULT_VARIABLE NO_BREW     # if it is 0, brew is installed
        OUTPUT_VARIABLE BREW_PREFIX
        OUTPUT_STRIP_TRAILING_WHITESPACE
        
    ) 
    if(NOT NO_BREW)
      list(APPEND CMAKE_PREFIX_PATH "${BREW_PREFIX}/include")               # add the brew prefix to the cmake prefix path
      list(APPEND CMAKE_INCLUDE_PATH "${BREW_PREFIX}/include")      # add the include path to the cmake prefix path
      # list(APPEND CMAKE_PREFIX_PATH "${BREW_PREFIX}/opt/ncurses")   # add the ncurses path to the cmake prefix path
       #list(APPEND CMAKE_PREFIX_PATH "${BREW_PREFIX}/opt/llvm")      # add the llvm path to the cmake prefix path
    endif()
    
endif()

cmake_print_variables(NO_BREW BREW_PREFIX CMAKE_PREFIX_PATH CMAKE_INCLUDE_PATH)




find_library(reflex_lib reflex REQUIRED)

add_custom_target(lexer)
add_custom_command(
    TARGET lexer
    COMMAND reflex -o ${CMAKE_BINARY_DIR}/lex.yy.cpp ${PROJECT_SOURCE_DIR}/wcpp.l
    DEPENDS ${PROJECT_SOURCE_DIR}/wcpp.l
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
    COMMENT "Generating lex.yy.cpp and lex.yy.h")

add_executable(wc lex.yy.cpp)
add_dependencies(wc lexer)
target_include_directories(wc PRIVATE ${CMAKE_PREFIX_PATH})
target_link_libraries(wc ${reflex_lib})









