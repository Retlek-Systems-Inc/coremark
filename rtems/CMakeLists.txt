# -------------------------------------------------------------------
add_library( coremark_port_rtems INTERFACE )

target_sources(coremark_port_rtems
    INTERFACE
        init.c
        $<$TARGET_PROPERTY:coremark_port_posix,INTERFACE_SOURCES>
)

target_include_directories(coremark_port_rtems
    INTERFACE
        $<$TARGET_PROPERTY:coremark_port_posix,INTERFACE_INCLUDE_DIRECTORIES>
)

target_link_libraries(coremark_port_rtems
    INTERFACE
        CoreMark::api
        # Note no LIBRT for this port either - so not including the link libraries.
)

# -------------------------------------------------------------------
if(COREMARK_PORT STREQUAL "RTEMS")
    add_library( CoreMark::port ALIAS coremark_port_rtems)
endif()
