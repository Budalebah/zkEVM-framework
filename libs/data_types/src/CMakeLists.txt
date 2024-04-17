# SSZ++ can be compiled only with GCC 13+
if(NOT CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    message(FATAL_ERROR "Data types library can be built only with GCC 13+")
endif()
if(CMAKE_CXX_COMPILER_VERSION VERSION_LESS 13.0)
    message(FATAL_ERROR "Data types library can be built only with GCC 13+")
endif()

# SSZ++ requires C++23
set_target_properties(${LIBRARY_NAME} PROPERTIES CXX_STANDARD 23)

find_package(Hashtree REQUIRED)
find_package(sszpp REQUIRED)

set(SOURCES
    account.cpp
    account_block.cpp
    block.cpp
    block_header.cpp
    message.cpp
    state.cpp
    transaction.cpp
    transaction_receipt.cpp)

target_sources(${LIBRARY_NAME} PRIVATE ${SOURCES})

target_link_libraries(${LIBRARY_NAME} INTERFACE sszpp)
target_link_libraries(${LIBRARY_NAME} PRIVATE hashtree)