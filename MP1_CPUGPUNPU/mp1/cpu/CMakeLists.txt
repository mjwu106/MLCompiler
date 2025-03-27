cmake_minimum_required(VERSION 3.10)
project(cs521_mp1 LANGUAGES CXX)

find_package(OpenMP REQUIRED)

if (MSVC)
    # Windows (MSVC) 编译器优化
    set(OpenMP_CXX_FLAGS "/openmp:experimental")  # 启用 OpenMP SIMD 支持
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /O2 /fp:fast /arch:AVX2 ${OpenMP_CXX_FLAGS}")
else()
    # Linux/macOS (GCC/Clang) 编译器优化
    include(CheckCXXCompilerFlag)
    check_cxx_compiler_flag("-mfma" COMPILER_SUPPORTS_MFMA)
    if (COMPILER_SUPPORTS_MFMA)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -march=native -mfma -ffast-math -funroll-loops ${OpenMP_CXX_FLAGS}")
    else()
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -march=native -ffast-math -funroll-loops ${OpenMP_CXX_FLAGS}")
    endif()
endif()

add_executable(mp1_cpu gemm_cpu.cpp)

# 确保 OpenMP 连接
target_link_libraries(mp1_cpu OpenMP::OpenMP_CXX)