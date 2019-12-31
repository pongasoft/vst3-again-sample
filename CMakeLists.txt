# root CMakeLists.txt for vst3-again-sample built out of source.
# to run, set VST3_SDK_ROOT to inform CMake where the VST3 SDK is, for example:
# mkdir build
# cd build
# cmake -DVST3_SDK_ROOT=/Users/randy/dev/vst3sdk -DCMAKE_BUILD_TYPE=Debug ..
#

cmake_minimum_required (VERSION 3.9)

# build VST 2 by default
option(SMTG_CREATE_VST2_VERSION "Use VST2" ON)

set(VST3_SDK_ROOT "" CACHE PATH "Location of VST3 SDK")
set(public_sdk_SOURCE_DIR ${VST3_SDK_ROOT}/public.sdk)

project(SteinbergAgainSample)

include(VST3_SDK.cmake)

if (SMTG_CREATE_VST2_VERSION)
	set(again_vst2_sources
		${VST3_SDK_ROOT}/public.sdk/source/common/memorystream.cpp
		${VST3_SDK_ROOT}/public.sdk/source/vst/hosting/eventlist.cpp
		${VST3_SDK_ROOT}/public.sdk/source/vst/hosting/hostclasses.cpp
		${VST3_SDK_ROOT}/public.sdk/source/vst/hosting/parameterchanges.cpp
		${VST3_SDK_ROOT}/public.sdk/source/vst/hosting/processdata.cpp
		${VST3_SDK_ROOT}/public.sdk/source/vst/vst2wrapper/vst2wrapper.cpp
		${VST3_SDK_ROOT}/public.sdk/source/vst/vst2wrapper/vst2wrapper.h
		${VST3_SDK_ROOT}/public.sdk/source/vst2.x/audioeffect.cpp
		${VST3_SDK_ROOT}/public.sdk/source/vst2.x/audioeffectx.cpp
		source/againentry_vst2.cpp
	)
endif()

set(again_sources
	source/again.cpp
	source/again.h
	source/againcids.h
	source/againcontroller.cpp
	source/againcontroller.h
	source/againentry.cpp
	source/againparamids.h
	source/againprocess.h
	source/againsidechain.cpp
	source/againsidechain.h
	source/againuimessagecontroller.h
	source/version.h
)

# VST2 is only defined for macOS and Windows
if(MAC OR WIN)
	set(again_sources ${again_sources} ${again_vst2_sources})
endif()

set(target again)
smtg_add_vst3plugin(${target} ${again_sources})
set_target_properties(${target} PROPERTIES ${SDK_IDE_PLUGIN_EXAMPLES_FOLDER})
target_include_directories(${target} PUBLIC ${VSTGUI_ROOT}/vstgui4)
target_link_libraries(${target} PRIVATE base sdk vstgui_support)

smtg_add_vst3_resource(${target} "resource/again.uidesc")
smtg_add_vst3_resource(${target} "resource/background.png")
smtg_add_vst3_resource(${target} "resource/slider_background.png")
smtg_add_vst3_resource(${target} "resource/slider_handle.png")
smtg_add_vst3_resource(${target} "resource/slider_handle_2.0x.png")
smtg_add_vst3_resource(${target} "resource/vu_on.png")
smtg_add_vst3_resource(${target} "resource/vu_off.png")

if(MAC)
	smtg_set_bundle(${target} INFOPLIST "${CMAKE_CURRENT_LIST_DIR}/mac/Info.plist" PREPROCESS)
elseif(WIN)
	target_sources(${target} PRIVATE resource/again.rc)
endif()

if (SMTG_CREATE_VST2_VERSION)
	message(STATUS "SMTG_CREATE_VST2_VERSION is set for ${target}. A VST 2 version of the plug-in will be created (just rename the generated file from .vst3 to .vst).")
	if(MAC)
		# fix missing VSTPluginMain symbol when also building VST 2 version
		smtg_set_exported_symbols(${target} "${CMAKE_CURRENT_LIST_DIR}/mac/macexport_vst2.exp")
	endif()
	if (WIN)
		add_definitions(-D_CRT_SECURE_NO_WARNINGS)
	endif()
endif()
