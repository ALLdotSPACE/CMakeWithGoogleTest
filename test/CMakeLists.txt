find_package(Threads REQUIRED)

# Always download GoogleTest
include(ExternalProject)
ExternalProject_Add(gtest
    URL https://github.com/google/googletest/archive/release-1.10.0.zip
    PREFIX ${CMAKE_CURRENT_BINARY_DIR}/gtest
    INSTALL_COMMAND ""

    # pass the super-project compiler and flags to the external project to avoid
    # issues with name-mangling - thanks to https://stackoverflow.com/a/41916106/232452
    CMAKE_ARGS -DCMAKE_C_COMPILER=${CMAKE_C_COMPILER} -DCMAKE_C_FLAGS=${CMAKE_C_FLAGS}
    	       -DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER} -DCMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}
)
ExternalProject_Get_Property(gtest source_dir binary_dir)

# Set up GoogleTest
add_library(libgtest IMPORTED STATIC GLOBAL)
add_library(libgtest_main IMPORTED STATIC GLOBAL)
set_target_properties(libgtest PROPERTIES
    IMPORTED_LOCATION ${binary_dir}/lib/libgtest.a
)
set_target_properties(libgtest_main PROPERTIES
    IMPORTED_LOCATION ${binary_dir}/lib/libgtest_main.a
)
add_dependencies(libgtest gtest)
add_dependencies(libgtest_main gtest)
include_directories(${source_dir}/googletest/include)

# Set up GoogleMock
add_library(libgmock IMPORTED STATIC GLOBAL)
set_target_properties(libgmock PROPERTIES
    IMPORTED_LOCATION ${binary_dir}/lib/libgmock.a
)
add_dependencies(libgmock gtest)
include_directories(${source_dir}/googlemock/include)

# Then include test directories
add_subdirectory(TestExample)
