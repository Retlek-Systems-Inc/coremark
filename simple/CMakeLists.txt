# -------------------------------------------------------------------
add_library( coremark_port_simple STATIC EXCLUDE_FROM_ALL )

target_sources(coremark_port_simple
    PRIVATE
        core_portme.c
        core_portme.h
)

target_include_directories(coremark_port_simple
    PUBLIC
        .
)

target_link_libraries(coremark_port_simple
    INTERFACE
        CoreMark::api
        # Note no LIBRT for this port either - so not including the link libraries.
)

# -------------------------------------------------------------------
if(COREMARK_PORT STREQUAL "SIMPLE")
    add_library( CoreMark::port ALIAS coremark_port_simple)
endif()
