# premake-androidmk
Application.mk &amp; Android.mk generator for Premake5


## Android.mk API

`ndkoptim` : Optimization

Supported values :
* `default`
* `debug`
* `release`

*****

`ndkabi` : ABI targets

Supported values :
Wanted ABIs separated by spaces (e.g. `"armeabi x86 mips"`)
* `armeabi`
* `armeabi-v7a`
* `arm64-v8a`
* `x86`
* `x86_64`
* `mips`
* `mips64`

Or one of :
* `default`
* `all`

*****

`ndkplatform` : Android API version

Supported values :
* `default`
* `android-3`
* `android-4`
* `android-5`
* `android-8`
* `android-9`
* `android-12`
* `android-13`
* `android-14`
* `android-15`
* `android-16`
* `android-17`
* `android-18`
* `android-19`
* `android-21`

*****

`ndktoolchainversion` : Android toolchain version

Supported values :
* `default`
* `4.8`
* `4.9`
* `clang`
* `clang3.4`
* `clang3.5`

*****

`existingandroidmk` : Include existing Android.mk files

Supported values : List of files

*****

`existingandroidmk_staticlinks` and `existingandroidmk_sharedlinks` : Link libs from existing Android.mk files

Supported values : List of libraries


## Sample code

```lua
require "androidmk" -- Adjust path if needed

solution "MySolution"
	configurations { "Debug", "Release" }
	language "C++"

	location "android/jni" -- Generate files in android project jni folder (recommended)

	ndkabi "all"
	ndkplatform "android-12"

	filter "configurations:Release"
		ndkoptim "release"
	filter "configurations:Debug"
		ndkoptim "debug"


	project "MyProject"
		kind "SharedLib"
		includedirs "SDL2/include"
		files {
			"src/*.cpp",
			"SDL2/src/main/android/SDL_android_main.c",
		}

		links {
			"GLESv1_CM",
			"GLESv2",
			"log",
		}

		existingandroidmk {
			"SDL2/Android.mk",
		}

		existingandroidmk_staticlinks {
			"SDL2",
		}
```


It is recommended to create an Application.mk file including your generated one :

File android/jni/Application.mk :
```
include $(call my-dir)/MySolution_Application.mk
```


Generate command (in android folder) :
`ndk-build PM5_CONFIG=debug -j`

Use your own deploy command, mine is :
`ant debug`

Debug command :
`ndk-gdb-py --gnumake-flag PM5_CONFIG=debug`
