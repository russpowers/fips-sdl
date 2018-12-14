cmake_minimum_required(VERSION 3.5)

file(REMOVE SDL/include/SDL_config.h)

if (FIPS_EMSCRIPTEN)
set(EMSC_LINKER_FLAGS "${EMSC_LINKER_FLAGS} -s USE_SDL=2" PARENT_SCOPE)
include_directories(SDL/include)
else()

option(SDL_STATIC "" OFF)
option(SDL_SHARED "" OFF)
option(SDL_STATIC_PIC "" ON)

# This file conflicts with the generated file, and it's just a dummy


include_directories("${FIPS_PROJECT_BUILD_DIR}/fips-sdl_SDL/SDL/include")
add_subdirectory(SDL)

endif()