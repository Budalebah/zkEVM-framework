find_package(valijson REQUIRED)
find_package(Boost COMPONENTS REQUIRED json)

add_library(zkEVMJsonHelpers STATIC json_helpers.cpp)
target_link_libraries(zkEVMJsonHelpers PUBLIC ${Boost_LIBRARIES} PRIVATE valijson)
target_include_directories(zkEVMJsonHelpers PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
target_compile_features(zkEVMJsonHelpers PUBLIC cxx_std_23)
# It seems like exceptions is the only way to catch errors in valijson
target_compile_definitions(zkEVMJsonHelpers PRIVATE VALIJSON_USE_EXCEPTIONS)
