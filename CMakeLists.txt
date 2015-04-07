include(CheckFunctionExists)
include(CheckLibraryExists)
include(CheckIncludeFiles)
include(CheckIncludeFile)
include(CheckSymbolExists)
include(CheckCSourceRuns)
include(CheckCCompilerFlag)
include(CheckTypeSize)
include(CheckStructHasMember)
include(CMakeDependentOption)
include(FindPkgConfig)

fips_begin_lib(sdl)
	file(GLOB SOURCE_FILES
		SDL/src/video/dummy/*.c
		SDL/src/joystick/dummy/*.c
		SDL/src/haptic/dummy/*.c
		SDL/src/loadso/dummy/*.c
		SDL/src/filesystem/dummy/*.c
		SDL/src/timer/dummy/*.c
		SDL/src/main/dummy/*.c
		SDL/src/audio/dummy/*.c

		SDL/src/*.c
		SDL/src/atomic/*.c
		SDL/src/audio/*.c
		SDL/src/cpuinfo/*.c
		SDL/src/dynapi/*.c
		SDL/src/events/*.c
		SDL/src/file/*.c
		SDL/src/libm/*.c
		SDL/src/render/*.c
		SDL/src/render/*/*.c
		SDL/src/stdlib/*.c
		SDL/src/joystick/*.c
		SDL/src/thread/*.c
		SDL/src/timer/*.c
		SDL/src/video/*.c
		SDL/src/haptic/*.c
		SDL/src/power/*.c
		SDL/src/audio/disk/*.c
		SDL/src/thread/generic/*.c
	)

	if (FIPS_EMSCRIPTEN)
		list(APPEND SOURCE_FILES 
			SDL/src/audio/emscripten/*.c
			SDL/src/filesystem/emscripten/*.c
			SDL/src/joystick/emscripten/*.c
			SDL/src/power/emscripten/*.c
			SDL/src/video/emscripten/*.c
		)
	)

	if (FIPS_WINDOWS)
		list(APPEND SOURCE_FILES 
			SDL/src/core/windows/*.c
			SDL/src/audio/winmm/*.c
			SDL/src/audio/directsound/*.c
			SDL/src/audio/xaudio2/*.c
			SDL/src/video/windows/*.c
			SDL/src/power/windows/SDL_syspower.c
			SDL/src/filesystem/windows/*.c
			SDL/src/thread/windows/SDL_sysmutex.c
			SDL/src/thread/windows/SDL_syssem.c
			SDL/src/thread/windows/SDL_systhread.c
			SDL/src/thread/windows/SDL_systls.c
			SDL/src/thread/generic/SDL_syscond.c
			SDL/src/timer/windows/*.c
			SDL/src/loadso/windows/*.c
			SDL/src/core/windows/*.c
			SDL/src/joystick/windows/*.c
			SDL/src/haptic/windows/*.c
			SDL/src/main/windows/*.c
		)

		fips_libs(user32 gdi32 winmm imm32 ole32 oleaut32 version uuid)
	endif()

	if (FIPS_ANDROID)
	endif()

	if (FIPS_LINUX)
		include_directories(linux)
		add_definitions(-DUSING_GENERATED_CONFIG_H)
		list(APPEND SOURCE_FILES 
			SDL/src/core/linux/*.c
			SDL/src/haptic/linux/*.c
			SDL/src/joystick/linux/*.c
			SDL/src/power/linux/*.c
			SDL/src/filesystem/unix/*.c
			SDL/src/timer/unix/*.c
		)
	endif()

	if (FIPS_MACOS)
		list(APPEND SOURCE_FILES 
			SDL/src/file/cocoa/*.m
			SDL/src/audio/coreaudio/*.c
			SDL/src/joystick/darwin/*.c
			SDL/src/haptic/darwin/*.c
			SDL/src/power/macosx/*.c
			SDL/src/timer/unix/*.c
			SDL/src/filesystem/cocoa/*.m
		)
	endif()

	include_directories(SDL/include SDL/src)
	fips_files(${SOURCE_FILES})
fips_end_lib()
