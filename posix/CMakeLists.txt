# -------------------------------------------------------------------
add_library( coremark_port_posix INTERFACE )

target_sources(coremark_port_posix
    INTERFACE
        core_portme_posix_overrides.h
        core_portme.c
        core_portme.h
)

target_include_directories(coremark_port_posix
    INTERFACE
    .
)

target_link_libraries(coremark_port_posix
    INTERFACE
        CoreMark::api
        $<$<NOT:$<PLATFORM_ID:APPLE>>:rt> # For all platforms but Apple include librt.
)

# -------------------------------------------------------------------
if(COREMARK_PORT STREQUAL "POSIX")
    add_library( CoreMark::port ALIAS coremark_port_posix)
    # Note: See above for MacOS builds do not include librt.
endif()
