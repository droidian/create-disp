cmake_minimum_required(VERSION 3.10)

project(create_disp)


set(CMAKE_CXX_STANDARD 23)
cmake_minimum_required(VERSION 3.10)

project(create_disp)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(TARGET_USES_REAL_HWC "Enable real HWC present/validate path" OFF)

add_compile_options(-Werror)

add_executable(create-disp
    create-disp_buffers.cpp
    create-disp_core.cpp
    create-disp_display.cpp
    create-disp_threads.cpp
    main.cpp
)

find_package(PkgConfig REQUIRED)

pkg_check_modules(LIBDRM REQUIRED libdrm)
pkg_check_modules(SYSTEMD REQUIRED libsystemd)

if(TARGET_USES_REAL_HWC)
    pkg_check_modules(SYNC REQUIRED libsync)
endif()

target_include_directories(create-disp PRIVATE
    ${LIBDRM_INCLUDE_DIRS}
    ${SYSTEMD_INCLUDE_DIRS}
    /usr/include/libdrm
    /usr/include/android
)

if(TARGET_USES_REAL_HWC)
    target_include_directories(create-disp PRIVATE
        ${SYNC_INCLUDE_DIRS}
    )

    target_compile_definitions(create-disp PRIVATE
        TARGET_USES_REAL_HWC=1
    )
endif()

link_directories(
    ${LIBDRM_LIBRARY_DIRS}
    ${SYSTEMD_LIBRARY_DIRS}
)

target_link_libraries(create-disp
    hwc2
    hybris-common
    hybris-eglplatformcommon
    hybris-platformcommon
    gralloc
    ${LIBDRM_LIBRARIES}
    ${SYSTEMD_LIBRARIES}
)

if(TARGET_USES_REAL_HWC)
    target_link_libraries(create-disp
        ${SYNC_LIBRARIES}
    )
endif()