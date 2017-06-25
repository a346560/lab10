cmake_minimum_required(VERSION 3.0)

set(CMAKE_CXX_STANDARD 11)

include(cmake/HunterGate.cmake)

HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.7.0.tar.gz"
    SHA1 "e730118c7ec65126398f8d4f09daf9366791ede0"
)

project(demo)

hunter_add_package(print)

find_package(print)

add_executable(DEMO /home/user/vershinin/lab10/lab10/sources/demo.cpp)

target_link_libraries(DEMO print)

install(TARGETS DEMO RUNTIME DESTINATION bin)

