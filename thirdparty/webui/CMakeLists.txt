set(lib_name webui)
set(target_name dep_${lib_name})

project(${lib_name})

include(../cmake/ConfigThirdparty.cmake)

add_library(${target_name} STATIC empty.cpp)

set(WEBUI_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/include" CACHE STRING "webui include dir" FORCE)

if(WINDOWS)
  target_link_libraries(${target_name}
    ${platform_spec_path}/webui-2.lib
    ${platform_spec_path}/webui-2-secure.lib
    ${platform_spec_path}/webui-2-static.lib
    ${platform_spec_path}/webui-2-secure-static.lib
  )

  add_custom_command(TARGET ${target_name} POST_BUILD
      COMMAND ${CMAKE_COMMAND} -E copy_if_different
      ${platform_spec_path}/webui-2.dll
      ${platform_spec_path}/webui-2-secure.dll
      ${OutBinDir}
  )


  # 设置静态链接到 VCRuntime
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /MT")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /MT")
endif()

if(UNIX AND NOT APPLE)

  # 查找OpenSSL
  # apt install libssl-dev
  find_package(OpenSSL REQUIRED)
  
  target_link_libraries(${target_name}
    ${platform_spec_path}/libwebui-2-secure-static.a
    ${platform_spec_path}/libwebui-2-static.a
    OpenSSL::SSL
    OpenSSL::Crypto
  )

  add_custom_command(TARGET ${target_name} POST_BUILD
      COMMAND ${CMAKE_COMMAND} -E copy_if_different
      ${platform_spec_path}/webui-2.so
      ${platform_spec_path}/webui-2-secure.so
      ${OutBinDir}
  )
endif()

if(APPLE)
  target_link_libraries(${target_name}
    ${platform_spec_path}/libwebui-2-secure-static.a
    ${platform_spec_path}/libwebui-2-static.a
    ${platform_spec_path}/libwebui-2.dylib
    ${platform_spec_path}/libwebui-2-secure.dylib
  )

  add_custom_command(TARGET ${target_name} POST_BUILD
      COMMAND ${CMAKE_COMMAND} -E copy_if_different
      ${platform_spec_path}/webui-2.dylib
      ${platform_spec_path}/webui-2-secure.dylib
      ${OutBinDir}
  )
endif()


set_target_properties(${target_name} PROPERTIES
    INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/include"
)
