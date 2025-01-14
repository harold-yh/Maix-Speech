################# Add include #################
list(APPEND ADD_INCLUDE "include"
    )
# list(APPEND ADD_PRIVATE_INCLUDE "include_private")
###############################################

############## Add source files ###############
# list(APPEND ADD_SRCS  "src/lib1.c"
#     )
# aux_source_directory(src ADD_SRCS)  # collect all source file in src dir, will set var ADD_SRCS
append_srcs_dir(ADD_SRCS "src")     # append source file in src dir to var ADD_SRCS
# list(REMOVE_ITEM COMPONENT_SRCS "src/test.c")
###############################################

###### Add required/dependent components ######
list(APPEND ADD_REQUIREMENTS asound pthread m)
###############################################

if(CONFIG_TOOLCHAIN_PATH)
    message( "===== cross compile =====")
endif()

############ Add static libs ##################
if(CONFIG_ARCH_X86)
    list(APPEND ADD_STATIC_LIB "lib/x86/libms_asr_x86.a")
elseif(CONFIG_ARCH_R329)
    list(APPEND ADD_LINK_SEARCH_PATH "${CONFIG_TOOLCHAIN_PATH}/../lib")
    list(APPEND ADD_STATIC_LIB "lib/r329/libms_asr_r329.a")
    list(APPEND ADD_DYNAMIC_LIB "lib/r329/libaipudrv.so")
    list(APPEND ADD_DEFINITIONS -march=armv8-a)
elseif(CONFIG_ARCH_V83X)
    list(APPEND ADD_LINK_SEARCH_PATH "lib/v83x")
    list(APPEND ADD_STATIC_LIB "lib/v83x/libms_asr_v83x.a")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wno-unused-variable -fPIC -c -s -ffunction-sections -fdata-sections -march=armv7-a  -mtune=cortex-a7" PARENT_SCOPE)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wno-sign-compare -Wno-unused-variable -fPIC -fexceptions -s -ffunction-sections -fdata-sections -fpermissive -march=armv7-a  -mtune=cortex-a7" PARENT_SCOPE)
    list(APPEND ADD_DEFINITIONS -D__ARM__ -D__ARMV7__ -DCONF_KERNEL_IOMMU -DCONF_KERNEL_VERSION_4_9)
elseif(CONFIG_ARCH_ARMV7)
    list(APPEND ADD_LINK_SEARCH_PATH "lib/armv7")
    list(APPEND ADD_STATIC_LIB "lib/armv7/libms_asr_armv7.a")
    list(APPEND ADD_DEFINITIONS -march=armv7-a)
    # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fpermissive" PARENT_SCOPE)
elseif(CONFIG_ARCH_AARCH64)
    list(APPEND ADD_LINK_SEARCH_PATH "lib/aarch64" "${CONFIG_TOOLCHAIN_PATH}/../lib")
    list(APPEND ADD_STATIC_LIB "lib/aarch64/libms_asr_aarch64.a")
    list(APPEND ADD_DEFINITIONS -march=armv8-a)
    # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fpermissive" PARENT_SCOPE)
elseif(CONFIG_ARCH_ARMV7MUSL)
    list(APPEND ADD_LINK_SEARCH_PATH "lib/armv7musl" "${CONFIG_TOOLCHAIN_PATH}/../lib")
    list(APPEND ADD_STATIC_LIB "lib/armv7musl/libms_asr_armv7musl.a")
    list(APPEND ADD_DEFINITIONS -march=armv7-a -g)
    # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fpermissive" PARENT_SCOPE)
elseif(CONFIG_ARCH_RISCV64)
    list(APPEND ADD_LINK_SEARCH_PATH "lib/riscv64" "${CONFIG_TOOLCHAIN_PATH}/../lib")
    list(APPEND ADD_STATIC_LIB "lib/riscv64/libms_asr_riscv64.a")
    # list(APPEND ADD_DEFINITIONS -march=rv64gcvxtheadc)
    # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fpermissive" PARENT_SCOPE)
endif()
###############################################

############ Add dynamic libs ##################
# list(APPEND ADD_DYNAMIC_LIB "lib/r329.so")
###############################################

#### Add compile option for this component ####
#### Just for this component, won't affect other 
#### modules, including component that depend 
#### on this component
# list(APPEND ADD_DEFINITIONS_PRIVATE -DAAAAA=1)

#### Add compile option for this component
#### and components denpend on this component
# list(APPEND ADD_DEFINITIONS -fno-builtin)
###############################################

############ Add static libs ##################
#### Update parent's variables like CMAKE_C_LINK_FLAGS
# set(CMAKE_C_LINK_FLAGS "${CMAKE_C_LINK_FLAGS} -Wl,--start-group libmaix/libtest.a -ltest2 -Wl,--end-group" PARENT_SCOPE)
###############################################

# register component, DYNAMIC or SHARED flags will make component compiled to dynamic(shared) lib
register_component()

