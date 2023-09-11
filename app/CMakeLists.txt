set(APP_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/main.cc")
set(LIBRARY_INCLUDES "./" "${CMAKE_BINARY_DIR}/configured_files/include")

find_package(Protobuf REQUIRED)

add_executable(${EXECUTABLE_NAME} ${APP_SOURCES})
target_include_directories(${EXECUTABLE_NAME} PUBLIC ${LIBRARY_INCLUDES})
target_link_libraries(
    ${EXECUTABLE_NAME}
    PRIVATE playhouse_connector
            protobuf::libprotobuf
    )

if(${ENABLE_WARNINGS})
    target_set_warnings(
        TARGET
        ${EXECUTABLE_NAME}
        ENABLE
        ${ENABLE_WARNINGS}
        AS_ERRORS
        ${ENABLE_WARNINGS_AS_ERRORS})
endif()

if(${ENABLE_LTO})
    target_enable_lto(
        TARGET
        ${EXECUTABLE_NAME}
        ENABLE
        ON)
endif()

if(${ENABLE_CLANG_TIDY})
    add_clang_tidy_to_target(${EXECUTABLE_NAME})
endif()
