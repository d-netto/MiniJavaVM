execute_process(COMMAND flex -o ${CMAKE_CURRENT_SOURCE_DIR}/lex.yy.c ${CMAKE_CURRENT_SOURCE_DIR}/scanner.l)
add_library(lexyy lex.yy.c)
add_library(scanner scanner.cpp)
add_library(parser parser.cpp)
add_library(semantics semantics.cpp)
add_library(bc_compiler bc_compiler.cpp)
add_library(llvm_compiler llvm_compiler.cpp)
add_library(gc gc.cpp)
add_executable(interpreter interpreter.cpp)

# taken from https://llvm.org/docs/CMake.html#embedding-llvm-in-your-project
find_package(LLVM REQUIRED CONFIG)
message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")
include_directories(${LLVM_INCLUDE_DIRS})
separate_arguments(LLVM_DEFINITIONS_LIST NATIVE_COMMAND ${LLVM_DEFINITIONS})
add_definitions(${LLVM_DEFINITIONS_LIST})
llvm_map_components_to_libnames(llvm_libs support core irreader)

target_link_libraries(interpreter gc bc_compiler llvm_compiler lexyy scanner parser semantics ${llvm_libs})
