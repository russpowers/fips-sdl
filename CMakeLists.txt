include(CheckIncludeFiles)
include(CheckIncludeFile)
include(CheckFunctionExists)
include(CheckLibraryExists)
find_package(Threads)

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
		list(APPEND EXTRA_LIBS user32 gdi32 winmm imm32 ole32 oleaut32 version uuid dinput8 dxguid dxerr)
	endif()

	if (FIPS_ANDROID)
		# TODO: files
	endif()

	if (FIPS_LINUX)
		add_definitions(-DHAVE_LIBC=1 -DSDL_THREAD_PTHREADS=1 -DHAVE_PTHREADS_SEM=1 -DHAVE_PTHREAD_RECURSIVE_MUTEX=1)
		check_include_file(sys/types.h HAVE_SYS_TYPES_H)
		foreach(_HEADER stdio.h stdlib.h stddef.h stdarg.h malloc.h memory.h string.h strings.h inttypes.h stdint.h ctype.h math.h iconv.h signal.h)
			string(TOUPPER "HAVE_${_HEADER}" _UPPER)
			string(REPLACE "." "_" _HAVE_H ${_UPPER})
			check_include_file("${_HEADER}" ${_HAVE_H})
			if(${_HAVE_H})
				add_definitions(-D${_HAVE_H}=1)
			endif()
		endforeach()

		foreach(_FN
				strtod malloc calloc realloc free getenv setenv putenv unsetenv
				qsort abs bcopy memset memcpy memmove memcmp strlen strlcpy strlcat
				strdup _strrev _strupr _strlwr strchr strrchr strstr itoa _ltoa
				_uitoa _ultoa strtol strtoul _i64toa _ui64toa strtoll strtoull
				atoi atof strcmp strncmp _stricmp strcasecmp _strnicmp strncasecmp
				vsscanf vsnprintf fseeko fseeko64 sigaction setjmp
				nanosleep sysconf sysctlbyname
				)
			string(TOUPPER ${_FN} _UPPER)
			set(_HAVEVAR "HAVE_${_UPPER}")
			check_function_exists("${_FN}" ${_HAVEVAR})
			if(${_HAVEVAR})
				add_definitions(-D${_HAVEVAR}=1)
			endif()
		endforeach()

		check_library_exists(m pow "" HAVE_LIBM)
		if(HAVE_LIBM)
			set(CMAKE_REQUIRED_LIBRARIES m)
			foreach(_FN
					atan atan2 ceil copysign cos cosf fabs floor log pow scalbn sin
					sinf sqrt sqrtf tan tanf acos asin
					)
				string(TOUPPER ${_FN} _UPPER)
				set(_HAVEVAR "HAVE_${_UPPER}")
				check_function_exists("${_FN}" ${_HAVEVAR})
				if(${_HAVEVAR})
					add_definitions(-D${_HAVEVAR}=1)
				endif()
			endforeach()
			set(CMAKE_REQUIRED_LIBRARIES)
			list(APPEND EXTRA_LIBS m)
		endif()

		check_library_exists(iconv iconv_open "" HAVE_LIBICONV)
		if(HAVE_LIBICONV)
			list(APPEND EXTRA_LIBS iconv)
			add_definitions(-DHAVE_ICONV=1)
		endif()

		check_include_file(alloca.h HAVE_ALLOCA_H)
		if(HAVE_ALLOCA_H)
			add_definitions(-DHAVE_ALLOCA_H=1)
		endif()
		check_function_exists(alloca HAVE_ALLOCA)
		if(HAVE_ALLOCA)
			add_definitions(-DHAVE_ALLOCA=1)
		endif()

		include_directories(linux)
		add_definitions(-DUSING_GENERATED_CONFIG_H)
		#fips_libs(${CMAKE_THREAD_LIBS_INIT})
	endif()

	if (FIPS_MACOS)
		list(APPEND SOURCE_FILES
			SDL/src/file/cocoa/SDL_rwopsbundlesupport.m
			SDL/src/filesystem/cocoa/SDL_sysfilesystem.m
		)

		find_library(COREVIDEO CoreVideo)
		list(APPEND EXTRA_LIBS ${COREVIDEO})
		find_library(COCOA_LIBRARY Cocoa)
		list(APPEND EXTRA_LIBS ${COCOA_LIBRARY})
		find_library(IOKIT IOKit)
		list(APPEND EXTRA_LIBS ${IOKIT})
		find_library(FORCEFEEDBACK ForceFeedback)
		list(APPEND EXTRA_LIBS ${FORCEFEEDBACK})
		find_library(CARBON_LIBRARY Carbon)
		list(APPEND EXTRA_LIBS ${CARBON_LIBRARY})
		find_library(COREAUDIO CoreAudio)
		list(APPEND EXTRA_LIBS ${COREAUDIO})
		find_library(AUDIOUNIT AudioUnit)
		list(APPEND EXTRA_LIBS ${AUDIOUNIT})
	endif()

	include_directories(SDL/include SDL/src)
	fips_files(${SOURCE_FILES})

	if(EXTRA_LIBS)
		list(REMOVE_DUPLICATES EXTRA_LIBS)
		fips_libs(${EXTRA_LIBS})
	endif()
fips_end_lib()
