set(xf86gammacfg_SRCS xf86gammacfg.cpp)

kde4_add_executable(xf86gammacfg ${xf86gammacfg_SRCS})

install(TARGETS xf86gammacfg ${INSTALL_TARGETS_DEFAULT_ARGS})

