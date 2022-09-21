cmake_minimum_required(VERSION 3.5)
project(ceres_vendor)

find_package(ament_cmake REQUIRED)
include(ExternalProject)

set(VERSION 1.14.0)

# The configure step fails at catkin_package() if this directory
# doesn't exist yet. Create it at configure time!
file(MAKE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})

# Patch away the linking to the gomp OpenMP implementation, in case we use the Intel OpenMP implementation and Clang.
if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
  set(GOMP_PATCH "")
else (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
  set(GOMP_PATCH "--dry-run")
endif (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")

find_package(gflags_vendor REQUIRED)
find_package(glog_vendor REQUIRED)
find_package(suitesparse_vendor REQUIRED)
find_package(eigen_vendor REQUIRED)

ExternalProject_Add(ceres_src
  GIT_REPOSITORY https://github.91chi.fun//https://github.com/ceres-solver/ceres-solver.git
  GIT_TAG ${VERSION}
  UPDATE_COMMAND ""
  PATCH_COMMAND git apply ${CMAKE_CURRENT_SOURCE_DIR}/0001-fix-windows-suitesparse-finding-error.patch
  CONFIGURE_COMMAND cd ../ceres_src && cmake
    -DGFLAGS=ON
    -Dgflags_DIR=${gflags_DIR}
    -Dglog_DIR=${glog_DIR}
    -DEigen3_DIR=${Eigen3_DIR}
    -DBUILD_DOCUMENTATION=OFF
    -Dsuitesparse_DIR=${suitesparse_DIR}
    -DLAPACK_DIR=${LAPACK_DIR}
    -DBUILD_EXAMPLES=OFF
    -DBUILD_TESTING=OFF
    -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/ceres_install 
    -DBUILD_SHARED_LIBS=OFF
    -DCMAKE_VERBOSE_MAKEFILE=ON
    -DCMAKE_BUILD_TYPE:STRING=Release
    -DCMAKE_FIND_PACKAGE_NO_PACKAGE_REGISTRY=ON
    -DCMAKE_FIND_PACKAGE_NO_SYSTEM_PACKAGE_REGISTRY=ON
    .
  BUILD_COMMAND cd ../ceres_src && cmake --build . --config Release
  INSTALL_COMMAND cd ../ceres_src && cmake --install .
)

install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/ceres_install/
        DESTINATION ${CMAKE_INSTALL_PREFIX}/
        )

ament_package(CONFIG_EXTRAS "cmake/ceres-vendor-extras.cmake.in")
