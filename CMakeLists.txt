# extern/classical/CCBS/CMakeLists.txt
cmake_minimum_required(VERSION 3.16)
project(ccbs LANGUAGES CXX)

option(CCBS_BUILD_DRIVER "Build the CCBS CLI driver" ON)

# Collect sources (core = everything except main.cpp)
file(GLOB CCBS_CORE_SRC CONFIGURE_DEPENDS
		*.cpp
		src/*.cpp
		simplex/*.cpp
)
list(REMOVE_ITEM CCBS_CORE_SRC
		${CMAKE_CURRENT_SOURCE_DIR}/main.cpp
)

add_library(ccbs_core STATIC ${CCBS_CORE_SRC})
add_library(ccbs::core ALIAS ccbs_core)

target_include_directories(ccbs_core
		PUBLIC
		$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
		$<INSTALL_INTERFACE:include>
)
target_compile_features(ccbs_core PUBLIC cxx_std_17)

find_package(Boost REQUIRED)
target_link_libraries(ccbs_core PUBLIC Boost::boost)

if (CCBS_BUILD_DRIVER)
	add_executable(ccbs main.cpp)
	target_link_libraries(ccbs PRIVATE ccbs_core)
	target_compile_features(ccbs PRIVATE cxx_std_17)
endif()
