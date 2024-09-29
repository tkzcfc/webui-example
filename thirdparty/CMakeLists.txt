project(thirdparty)

option(GUI_WITH_UNZIP "Build with internal unzip support" ON)


function(configure_target_outdir target)
    set_target_properties(${target} PROPERTIES
        ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
        LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
        RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"
        FOLDER "ThirdParty"
    )
endfunction()

add_library(thirdparty STATIC empty.cpp)

add_subdirectory(zlib)
target_link_libraries(thirdparty
    dep_zlib
)


# unzip depend on zlib
if(GUI_WITH_UNZIP)
    add_subdirectory(unzip)
    target_link_libraries(thirdparty unzip)
    configure_target_outdir(unzip)
endif(GUI_WITH_UNZIP)

add_subdirectory(webui)
target_link_libraries(thirdparty
    dep_webui
)

# put "thirdparty" into thirdparty folder, too
configure_target_outdir(thirdparty)
