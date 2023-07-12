set(TARGET_NAME DensifyPointCloud)

# for qt
# set(CMAKE_AUTOUIC_SEARCH_PATHS ${CMAKE_CURRENT_SOURCE_DIR})

find_package(OpenMVS REQUIRED HINTS "D:/ThirdParty/OpenMVS/CMake/OpenMVS")
find_package(Boost COMPONENTS filesystem system thread serialization zlib REQUIRED)
find_package(Eigen3 REQUIRED HINTS "D:/ThirdParty/Eigen3/share/eigen3/cmake")
find_package(OpenCV REQUIRED HINTS "D:/ThirdParty/opencv480/opencv/build")
find_package(CUDA REQUIRED)
enable_language(CUDA)
include(CheckLanguage)
check_language(CUDA)

file(GLOB_RECURSE CPP_HEAD_FILES CONFIGURE_DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/*.h)
file(GLOB_RECURSE CPP_SOUCE_FILES CONFIGURE_DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}" FILES ${CPP_HEAD_FILES} ${CPP_SOUCE_FILES})

add_executable(${TARGET_NAME} ${CPP_HEAD_FILES} ${CPP_SOUCE_FILES})

set_target_properties(${TARGET_NAME} PROPERTIES CXX_STANDARD 17 OUTPUT_NAME ${TARGET_NAME})
set_target_properties(${TARGET_NAME} PROPERTIES FOLDER Apps)

target_compile_options(${TARGET_NAME} PUBLIC "$<$<COMPILE_LANG_AND_ID:CXX,MSVC>:/WX->")

target_link_libraries(${TARGET_NAME} 
PRIVATE 
${PROJECT_NAME}::Primitives 
OpenMVS::Common 
OpenMVS::Math 
OpenMVS::IO 
OpenMVS::MVS
Boost::system
Boost::filesystem
# Boost::serialization
Boost::zlib
Boost::dynamic_linking
Boost::thread
Eigen3::Eigen
${OpenCV_LIBS}
${CUDA_LIBRARIES}
)

target_include_directories(
  ${TARGET_NAME} 
  PUBLIC 
  ${CMAKE_CURRENT_SOURCE_DIR}/include
  ${OpenCV_INCLUDE_DIRS}
  ${OpenMVS_INCLUDE_DIRS}
  ${CUDA_INCLUDE_DIRS}
)

target_compile_definitions(
${TARGET_NAME} 
PUBLIC 
"_SUPPORT_CPP11"
)