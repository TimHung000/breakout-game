# Set the project name
project (stb)

add_library(${PROJECT_NAME} src/stb_image.cpp)

target_include_directories(${PROJECT_NAME}
    PUBLIC ${PROJECT_SOURCE_DIR}/include
)