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

		SDL/src/audio/emscripten/*.c
		SDL/src/filesystem/emscripten/*.c
		SDL/src/joystick/emscripten/*.c
		SDL/src/power/emscripten/*.c
		SDL/src/video/emscripten/*.c

		SDL/src/core/windows/*.c
		SDL/src/audio/winmm/*.c
		SDL/src/audio/directsound/*.c
		SDL/src/audio/xaudio2/*.c
		SDL/src/video/windows/*.c
		SDL/src/power/windows/*.c
		SDL/src/filesystem/windows/*.c
		SDL/src/thread/windows/*.c
		SDL/src/timer/windows/*.c
		SDL/src/loadso/windows/*.c
		SDL/src/core/windows/*.c
		SDL/src/joystick/windows/*.c
		SDL/src/haptic/windows/*.c
		SDL/src/main/windows/*.c

		SDL/src/core/linux/*.c
		SDL/src/haptic/linux/*.c
		SDL/src/joystick/linux/*.c
		SDL/src/power/linux/*.c

		SDL/src/filesystem/unix/*.c
		SDL/src/timer/unix/*.c

		SDL/src/audio/coreaudio/*.c
		SDL/src/joystick/darwin/*.c
		SDL/src/haptic/darwin/*.c
		SDL/src/power/macosx/*.c
	)

	if (FIPS_WINDOWS)
		# TODO: 64bit
		fips_libs(user32 gdi32 winmm imm32 ole32 oleaut32 version uuid)
	endif()

	if (FIPS_ANDROID)
		# TODO: files
	endif()

	if (FIPS_LINUX)
		include_directories(linux)
		add_definitions(-DUSING_GENERATED_CONFIG_H)
	endif()

	if (FIPS_MACOS)
		list(APPEND SOURCE_FILES
			SDL/src/file/cocoa/SDL_rwopsbundlesupport.m
			SDL/src/filesystem/cocoa/SDL_sysfilesystem.m
		)
	endif()

	include_directories(SDL/include SDL/src)
	fips_files(${SOURCE_FILES})
fips_end_lib()
