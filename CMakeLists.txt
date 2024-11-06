cmake_minimum_required(VERSION 2.8)

project(fpvue)

set(CMAKE_CXX_STANDARD 17)

set(TOOLCHAIN_PATH /root/SDK2/RK3566_RK3568_LINUX5.10_SDK_RELEASE/buildroot/output/rockchip_rk3566/host/bin)
set(CMAKE_C_COMPILER ${TOOLCHAIN_PATH}/aarch64-buildroot-linux-gnu-gcc)
set(CMAKE_CXX_COMPILER ${TOOLCHAIN_PATH}/aarch64-buildroot-linux-gnu-g++)


#set(SYSROOT /root/SDK/buildroot/output/rockchip_rk3566/target)
#./rockchip_rk3566/host/aarch64-buildroot-linux-gnu/sysroot/usr/include/drm/
set(SYSROOT /root/SDK2/RK3566_RK3568_LINUX5.10_SDK_RELEASE/buildroot/output/rockchip_rk3566/host/aarch64-buildroot-linux-gnu/sysroot)

# /root/SDK/buildroot/output/rockchip_rk3566/host/aarch64-buildroot-linux-gnu/sysroot
# /root/SDK/buildroot/output/rockchip_rk3566/target



add_compile_options("-Wno-address-of-packed-member")
SET(CMAKE_ASM_FLAGS "${CFLAGS} -x assembler-with-cpp")

set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR aarch64)

set(CMAKE_SYSROOT ${SYSROOT})
set(CMAKE_FIND_ROOT_PATH ${SYSROOT})



include_directories(
    ${SYSROOT}/usr/include
    ${SYSROOT}/usr/include/glib-2.0
    ${SYSROOT}/usr/lib/glib-2.0/include
    ${SYSROOT}/usr/include/gstreamer-1.0
    ${SYSROOT}/usr/include/drm
    ${SYSROOT}/usr/include/cairo
)

#set(SOURCE_FILES drm.c
#	osd.c
#	rtp.c
#	mavlink.c
#        main.cpp
#        copy_util.h
#        SchedulingHelper.hpp
#        gstrtpreceiver.cpp
#        gstrtpreceiver.h
#        assembly/memcpymove-v7l.S
#        parse_x20_util.h
#)

set(SOURCE_FILES 
        drm.h
        drm.c
        osd.h
	osd.c
        rtp.h
	rtp.c
        mavlink.h
	mavlink.c
	main.cpp
        main.h
        scheduling_helper.hpp
        gstrtpreceiver.cpp
        gstrtpreceiver.h)


set(GSTREAMER_LIBRARIES 
    /root/SDK2/RK3566_RK3568_LINUX5.10_SDK_RELEASE/buildroot/output/rockchip_rk3566/target/usr/lib/libgstreamer-1.0.so
    /root/SDK2/RK3566_RK3568_LINUX5.10_SDK_RELEASE/buildroot/output/rockchip_rk3566/target/usr/lib/libglib-2.0.so
    /root/SDK2/RK3566_RK3568_LINUX5.10_SDK_RELEASE/buildroot/output/rockchip_rk3566/target/usr/lib/libgobject-2.0.so
    /root/SDK2/RK3566_RK3568_LINUX5.10_SDK_RELEASE/buildroot/output/rockchip_rk3566/target/usr/lib/libgstapp-1.0.so
#     /root/SDK2/RK3566_RK3568_LINUX5.10_SDK_RELEASE/buildroot/output/rockchip_rk3566/target/usr/lib/libdrm-cursor.so
)

add_executable(${PROJECT_NAME} ${SOURCE_FILES})

target_link_libraries(${PROJECT_NAME} rockchip_mpp pthread drm m cairo )
target_link_libraries(${PROJECT_NAME} ${GSTREAMER_LIBRARIES})

if(CMAKE_BUILD_TYPE MATCHES "Debug")
  set(
    CMAKE_C_FLAGS
    "${CMAKE_C_FLAGS} -Werror -fsanitize=undefined -fsanitize=address"
  )
  target_link_options(${PROJECT_NAME}
    BEFORE PUBLIC -fsanitize=undefined PUBLIC -fsanitize=address
  )
endif()

include(GNUInstallDirs)
install(TARGETS ${PROJECT_NAME}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

