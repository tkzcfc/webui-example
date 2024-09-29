set(lib_name webui)
set(target_name dep_${lib_name})

project(${lib_name})

include(../cmake/ConfigThirdparty.cmake)

add_library(${target_name} STATIC empty.cpp)

set(WEBUI_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/include" CACHE STRING "webui include dir" FORCE)

if(WINDOWS)
  target_link_libraries(${target_name}
    ${platform_spec_path}/webui-2-static.lib
  )

  # 设置静态链接到 VCRuntime
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /MT")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /MT")
endif()

if(UNIX AND NOT APPLE)
  
  target_link_libraries(${target_name}
    ${platform_spec_path}/libwebui-2-static.a
  )
endif()

if(APPLE)
  target_link_libraries(${target_name}
    ${platform_spec_path}/libwebui-2-static.a
  )
endif()


set_target_properties(${target_name} PROPERTIES
    INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/include"
)
