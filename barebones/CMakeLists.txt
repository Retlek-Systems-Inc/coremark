# -------------------------------------------------------------------
add_library( coremark_port_barebones INTERFACE EXCLUDE_FROM_ALL )

target_sources(coremark_port_barebones
    INTERFACE
        core_portme.c
        core_portme.h
        cvt.c
        ee_printf.c
)

target_include_directories(coremark_port_barebones
    INTERFACE
    .
)

target_link_libraries(coremark_port_barebones
    INTERFACE
        CoreMark::api
)

# -------------------------------------------------------------------
if(COREMARK_PORT STREQUAL "BAREBONES")
    add_library( CoreMark::port ALIAS coremark_port_barebones)
endif()
