add_library(mindexpr mindexpr.cc)

include_directories(${PROJECT_SOURCE_DIR}/build/thirdparty/glog/include)
add_subdirectory(ad)
add_subdirectory(ir)
add_subdirectory(operators)
add_subdirectory(infer)
add_subdirectory(utils)
add_subdirectory(parse)
add_subdirectory(prim)
add_subdirectory(opt)
add_subdirectory(draw)
add_subdirectory(abstract)

target_link_libraries(mindexpr
                        ad
                        parse
                        operators
                        ir
                        infer
                        abstract
                        utils
                        prim
                        opt
                        draw
                        )

set(PYTHON_MODULE_SOURCE init.cc grad.cc pipeline/pipeline.cc
    parse/parse.cc parse/parse_ast.cc)

set(C_EXPRESSION_DEPENDS ir abstract prim utils)

link_directories(${PROJECT_SOURCE_DIR}/build/thirdparty/glog/lib)

pybind11_add_module(_c_expression ${PYTHON_MODULE_SOURCE})

target_link_libraries(_c_expression PRIVATE pybind11::module glog ${C_EXPRESSION_DEPENDS})
