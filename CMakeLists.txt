#
# project: fips-soloud
#
if (NOT FIPS_IMPORT)
    cmake_minimum_required(VERSION 2.8)
    get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
    include("${FIPS_ROOT_DIR}/cmake/fips.cmake")
    fips_setup()
    fips_project(fips-soloud)
endif()

# default option values
set(soloud_winmm_default 0)
set(soloud_coreaudio_default 0)
set(soloud_openal_default 0)
set(soloud_alsa_default 0)
set(soloud_tools_default 0)
if (FIPS_WINDOWS)
    set(soloud_winmm_default 1)
endif()
if (FIPS_OSX)
    set(soloud_coreaudio_default 1)
endif()
if (FIPS_EMSCRIPTEN)
    set(soloud_openal_default 1)
elseif (FIPS_LINUX)
    set(soloud_alsa_default 1)
endif()
if (NOT FIPS_IMPORT)
    set(soloud_tools_default 1)
endif()

# backend options
option(SOLOUD_SDL "Compile soloud with SDL backend" OFF)
option(SOLOUD_SDL_STATIC "Compile soloud with static SDL backend" OFF)
option(SOLOUD_SDL2_STATIC "Compile soloud with static SDL2 backend" OFF)
option(SOLOUD_PORTAUDIO "Compile soloud with PortAudio backend" OFF)
option(SOLOUD_OPENAL "Compile soloud with OpenAL backend" ${soloud_openal_default})
option(SOLOUD_XAUDIO2 "Compile soloud with XAudio2 backend" OFF)
option(SOLOUD_WINMM "Compile soloud with WinMM backend" ${soloud_winmm_default})
option(SOLOUD_WASAPI "Compile soloud with WASAPI backend" OFF)
option(SOLOUD_ALSA "Compile soloud with ALSA backend" ${soloud_alsa_default})
option(SOLOUD_OSS "Compile soloud with OSS backend" OFF)
option(SOLOUD_COREAUDIO "Compile soloud with CoreAudio backend" ${soloud_coreaudio_default})
option(SOLOUD_NULL "Compile soloud with NULL backend" OFF)
option(SOLOUD_LIBMODPLUG "Add libmodplug support to soloud" OFF)
option(SOLOUD_PORTMIDI "Add portmidi support to soloud" OFF)
option(SOLOUD_TOOLS "Compile soloud tools" ${soloud_tools_default})

fips_include_directories(soloud/include)
fips_begin_module(soloud)
    fips_dir(soloud/src/audiosource/modplug GROUP audiosource)
    fips_files(soloud_modplug.cpp)
    fips_dir(soloud/src/audiosource/monotone GROUP audiosource)
    fips_files(soloud_monotone.cpp)
    fips_dir(soloud/src/audiosource/sfxr GROUP audiosource)
    fips_files(soloud_sfxr.cpp)
    fips_dir(soloud/src/audiosource/speech GROUP audiosource)
    fips_files(
        darray.cpp darray.h
        klatt.cpp klatt.h
        resonator.cpp resonator.h
        soloud_speech.cpp
        tts.cpp tts.h)
    fips_dir(soloud/src/audiosource/tedsid GROUP audiosource)
    fips_files(sid.cpp sid.h soloud_tedsid.cpp ted.cpp ted.h)
    fips_dir(soloud/src/audiosource/vic GROUP audiosource)
    fips_files(soloud_vic.cpp)
    fips_dir(soloud/src/audiosource/wav GROUP audiosource)
    fips_files(soloud_wav.cpp soloud_wavstream.cpp stb_vorbis.c stb_vorbis.h)
    fips_dir(soloud/src/filter GROUP filter)
    fips_files(
        soloud_bassboostfilter.cpp
        soloud_fftfilter.cpp
        soloud_biquadresonantfilter.cpp
        soloud_flangerfilter.cpp
        soloud_dcremovalfilter.cpp
        soloud_lofifilter.cpp
        soloud_echofilter.cpp)
    fips_dir(soloud/src/core GROUP core)
    fips_files(
        soloud.cpp
        soloud_audiosource.cpp
        soloud_bus.cpp
        soloud_core_3d.cpp
        soloud_core_basicops.cpp
        soloud_core_faderops.cpp
        soloud_core_filterops.cpp
        soloud_core_getters.cpp
        soloud_core_setters.cpp
        soloud_core_voicegroup.cpp
        soloud_core_voiceops.cpp
        soloud_fader.cpp
        soloud_fft.cpp
        soloud_fft_lut.cpp
        soloud_file.cpp
        soloud_filter.cpp
        soloud_thread.cpp
    )
    fips_dir(soloud/include GROUP include)
    fips_files(
        soloud.h
        soloud_audiosource.h
        soloud_bassboostfilter.h
        soloud_biquadresonantfilter.h
        soloud_bus.h
        soloud_c.h
        soloud_dcremovalfilter.h
        soloud_echofilter.h
        soloud_error.h
        soloud_fader.h
        soloud_fft.h
        soloud_fftfilter.h
        soloud_file.h
        soloud_file_hack_off.h soloud_file_hack_on.h
        soloud_filter.h
        soloud_flangerfilter.h
        soloud_internal.h
        soloud_lofifilter.h
        soloud_modplug.h
        soloud_monotone.h
        soloud_sfxr.h
        soloud_speech.h
        soloud_tedsid.h
        soloud_thread.h
        soloud_vic.h
        soloud_wav.h
        soloud_wavstream.h
    )
    if (SOLOUD_OPENAL) 
        add_definitions(-DWITH_OPENAL=1)
        fips_dir(soloud/src/backend/openal GROUP openal)
        fips_files(soloud_openal.cpp soloud_openal_dll.c)
    endif()
    if (SOLOUD_ALSA)
        add_definitions(-DWITH_OPENAL=1)
        fips_dir(soloud/src/backend/alsa GROUP alsa)
        fips_files(soloud_alsa.cpp)
        fips_libs(asound)
    endif()
    if (SOLOUD_OSS)
        add_definitions(-DWITH_OSS=1)
        fips_dir(soloud/src/backend/oss GROUP oss)
        fips_files(soloud_oss.cpp)
    endif()
    if (SOLOUD_COREAUDIO)
        add_definitions(-DWITH_COREAUDIO=1)
        fips_dir(soloud/src/backend/coreaudio GROUP coreaudio)
        fips_files(soloud_coreaudio.cpp)
        fips_frameworks_osx(AudioToolbox)
    endif()
    if (SOLOUD_PORTAUDIO)
        add_definitions(-DWITH_PORTAUDIO=1)
        fips_dir(soloud/src/backend/portaudio GROUP portaudio)
        fips_files(soloud_portaudio.cpp soloud_portaudio_dll.c)
    endif()
    if (SOLOUD_SDL)
        add_definitions(-DWITH_SDL=1)
        fips_dir(soloud/src/backend/sdl GROUP sdl)
        fips_files(soloud_sdl.cpp soloud_sdl_dll.c)
    endif()
    if (SOLOUD_SDL_STATIC)
        add_definitions(-DWITH_SDL_STATIC=1)
        fips_dir(soloud/src/backend/sdl_static GROUP sdl_static)
        fips_files(soloud_sdl_static.cpp)
    endif()
    if (SOLOUD_SDL2_STATIC)
        add_definitions(-DWITH_SDL2_STATIC=1)
        fips_dir(soloud/src/backend/sdl2_static GROUP sdl2_static)
        fips_files(soloud_sdl2_static.cpp)
    endif()
    if (SOLOUD_WASAPI)
        add_definitions(-DWITH_WASAPI=1)
        fips_dir(soloud/src/backend/wasapi GROUP wasapi)
        fips_files(soloud_wasapi.cpp)
    endif()
    if (SOLOUD_XAUDIO2)
        add_definitions(-DWITH_XAUDIO2)
        fips_dir(soloud/src/backend/xaudio2 GROUP xaudio2)
        fips_files(soloud_xaudio2.cpp)
    endif()
    if (SOLOUD_WINMM)
        add_definitions(-DWITH_WINMM)
        fips_dir(soloud/src/backend/winmm GROUP winmm)
        fips_files(soloud_winmm.cpp)
    endif()
    if (SOLOUD_NULL)
        add_definitions(-DWITH_NULL)
        fips_dir(soloud/src/backend/null GROUP null)
        fips_files(soloud_null.cpp)
    endif()
fips_end_module()
if (SOLOUD_LIBMODPLUG)
    fips_include_directories(soloud/ext/libmodplug/src/libmodplug)
    fips_begin_module(libmodplug)
        add_definitions(-DMODPLUG_STATIC -DHAVE_SETENV -DHAVE_SINF)
        fips_dir(soloud/ext/libmodplug/src GROUP .)
        fips_files(
            fastmix.cpp
            load_669.cpp
            load_abc.cpp
            load_amf.cpp
            load_ams.cpp
            load_dbm.cpp
            load_dmf.cpp
            load_dsm.cpp
            load_far.cpp
            load_it.cpp
            load_j2b.cpp
            load_mdl.cpp
            load_med.cpp
            load_mid.cpp
            load_mod.cpp
            load_mt2.cpp
            load_mtm.cpp
            load_okt.cpp
            load_pat.cpp load_pat.h
            load_psm.cpp
            load_ptm.cpp
            load_s3m.cpp
            load_stm.cpp
            load_ult.cpp
            load_umx.cpp
            load_wav.cpp
            load_xm.cpp
            mmcmp.cpp
            modplug.cpp modplug.h
            snd_dsp.cpp
            snd_flt.cpp
            snd_fx.cpp
            sndfile.cpp
            sndmix.cpp
            tables.h
        )
    fips_end_module()
endif()
if (SOLOUD_TOOLS)
    # some tools need C++ exceptions enabled
    set(FIPS_EXCEPTIONS ON CACHE BOOL "Enable C++ exceptions" FORCE)
    fips_ide_group(demos)
    fips_begin_app(simplest cmdline)
        fips_dir(soloud/demos/simplest GROUP .)
        fips_files(main.cpp)
        fips_deps(soloud)
    fips_end_app()
    fips_begin_app(welcome cmdline)
        fips_dir(soloud/demos/welcome GROUP .)
        fips_files(main.cpp)
        fips_deps(soloud)
    fips_end_app()
    fips_begin_app(nulldemo cmdline)
        fips_dir(soloud/demos/null GROUP .)
        fips_files(main.cpp)
        fips_deps(soloud)
    fips_end_app()
    fips_begin_app(enumerate cmdline)
        fips_dir(soloud/demos/enumerate GROUP .)
        fips_files(main.cpp)
        fips_deps(soloud)
    fips_end_app()
    fips_begin_app(c_test cmdline)
        fips_dir(soloud/demos/c_test GROUP .)
        fips_files(main.c)
        fips_dir(soloud/src/c_api GROUP .)
        fips_files(soloud_c.cpp)
        fips_deps(soloud)
    fips_end_app()
    # FIXME: most tools don't compile without fixes
    # fips_ide_group(tools)
    # fips_begin_app(tedsid2dump cmdline)
    #     fips_dir(soloud/src/tools/tedsid2dump GROUP .)
    #     fips_files(
    #         cbmtune.cpp cbmtune.h
    #         cpu.cpp cpu.h
    #         cpu7501asm.h
    #         main.cpp
    #         mem.h
    #         psid.h
    #         roms.h
    #         sid.cpp sid.h
    #         tedmem.cpp tedmem.h
    #         tedplay.cpp tedplay.h
    #         tedsound.cpp
    #         types.h)
    # fips_end_app()
    # fips_begin_app(resamplerlab cmdline)
    #     fips_dir(soloud/src/tools/resamplerlab GROUP .)
    #     fips_files(main.cpp stb_image_write.c stb_image_write.h)
    # fips_end_app()
    # fips_begin_app(codegen cmdline)
    #     fips_dir(soloud/src/tools/codegen GROUP .)
    #     fips_files(main.cpp)
    # fips_end_app()
    # fips_begin_app(lutgen cmdline)
    #     fips_dir(soloud/src/tools/lutgen GROUP .)
    #     fips_files(main.cpp)
    # fips_end_app()
endif()

set(args)
if (FIPS_CLANG)
    set(args "-Wno-unused-function -Wno-unused-private-field -Wno-dangling-else -Wno-missing-braces -Wno-unused-value -Wno-parentheses -Wno-sign-compare -Wno-unused-variable -Wno-deprecated-register -Wno-writable-strings -Wno-reorder -Wno-delete-non-virtual-dtor")
endif()
set_target_properties(soloud PROPERTIES COMPILE_FLAGS ${args}) 
if (SOLOUD_LIBMODPLUG)
    set_target_properties(libmodplug PROPERTIES COMPILE_FLAGS ${args})
endif()
if (SOLOUD_TOOLS)
    set_target_properties(enumerate PROPERTIES COMPILE_FLAGS ${args})
    set_target_properties(c_test PROPERTIES COMPILE_FLAGS ${args})
    #set_target_properties(tedsid2dump PROPERTIES COMPILE_FLAGS ${args})
    #set_target_properties(resamplerlab PROPERTIES COMPILE_FLAGS ${args})
endif()

if (NOT FIPS_IMPORT)
    fips_finish()
endif()

