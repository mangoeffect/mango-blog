---
title: "Build filament on mac"
draft: false
date: 2023-08-22T21:57:03+08:00
categories: [ "图形" ]
tags: [ "graphics", "filament", "macos"]
slug: "filament/build-filament-on-mac"
---

前置条件，编译前需要准备好以下工具环境

- CMake 3.19 (or more recent)
- clang 14.0 (or more recent)
- ninja 1.10

官方提供了一个编译脚本，执行 build.sh -h 可查看脚本的使用说明

```bash
sh build.sh -h
Usage:
    build.sh [options] <build_type1> [<build_type2> ...] [targets]

Options:
    -h
        Print this help message.
    -a
        Generate .tgz build archives, implies -i.
    -c
        Clean build directories.
    -C
        Clean build directories and revert android/ to a freshly sync'ed state.
        All (and only) git-ignored files under android/ are deleted.
        This is sometimes needed instead of -c (which still misses some clean steps).
    -d
        Enable matdbg.
    -f
        Always invoke CMake before incremental builds.
    -g
        Disable material optimization.
    -i
        Install build output
    -m
        Compile with make instead of ninja.
    -p platform1,platform2,...
        Where platformN is [desktop|android|ios|webgl|all].
        Platform(s) to build, defaults to desktop.
        Building for iOS will automatically perform a partial desktop build.
    -q abi1,abi2,...
        Where platformN is [armeabi-v7a|arm64-v8a|x86|x86_64|all].
        ABIs to build when the platform is Android. Defaults to all.
    -u
        Run all unit tests, will trigger a debug build if needed.
    -v
        Exclude Vulkan support from the Android build.
    -s
        Add iOS simulator support to the iOS build.
    -t
        Enable SwiftShader support for Vulkan in desktop builds.
    -e
        Enable EGL on Linux support for desktop builds.
    -l
        Build arm64/x86_64 universal libraries.
        For iOS, this builds universal binaries for devices and the simulator (implies -s).
        For macOS, this builds universal binaries for both Apple silicon and Intel-based Macs.
    -w
        Build Web documents (compiles .md.html files to .html).
    -k sample1,sample2,...
        When building for Android, also build select sample APKs.
        sampleN is an Android sample, e.g., sample-gltf-viewer.
        This automatically performs a partial desktop build and install.
    -b
        Enable Address and Undefined Behavior Sanitizers (asan/ubsan) for debugging.
        This is only for the desktop build.

Build types:
    release
        Release build only
    debug
        Debug build only

Targets:
    Any target supported by the underlying build system

Examples:
    Desktop release build:
        $ ./build.sh release

    Desktop debug and release builds:
        $ ./build.sh debug release

    Clean, desktop debug build and create archive of build artifacts:
        $ ./build.sh -c -a debug

    Android release build type:
        $ ./build.sh -p android release

    Desktop and Android release builds, with installation:
        $ ./build.sh -p desktop,android -i release

    Desktop matc target, release build:
        $ ./build.sh release matc

    Build gltf_viewer:
        $ ./build.sh release gltf_viewer
```

所以简单编译 mac

```bash
./build.sh -p desktop -i release
```

编译成功后会 install 在 out 目录

```bash
....
-- Installing: /Users/Learning/filament/out/release/filament/bin/roughness-prefilter
-- Installing: /Users/Learning/filament/out/release/filament/docs/roughness-prefilter.md
-- Installing: /Users/Learning/filament/out/release/filament/bin/specular-color
-- Installing: /Users//Learning/filament/out/release/filament/docs/specular-color.md
-- Installing: /Users/Learning/filament/out/release/filament/bin/uberz
```

查看编译后的产物

```bash
.
├── LICENSE
├── README.md
├── bin
│   ├── assets
│   │   ├── fonts
│   │   │   ├── Roboto-License.txt
│   │   │   └── Roboto-Medium.ttf
│   │   └── ibl
│   │       └── lightroom_14b
│   │           ├── lightroom_14b_ibl.ktx
│   │           ├── lightroom_14b_skybox.ktx
│   │           └── sh.txt
│   ├── basisu
│   ├── cmgen
│   ├── filamesh
│   ├── glslminifier
│   ├── gltf_viewer
│   ├── matc
│   ├── material_sandbox
│   ├── matinfo
│   ├── mipgen
│   ├── normal-blending
│   ├── resgen
│   ├── roughness-prefilter
│   ├── specular-color
│   └── uberz
├── docs
│   ├── filamesh.md
│   ├── matinfo.md
│   ├── mipgen.md
│   ├── normal-blending.md
│   ├── roughness-prefilter.md
│   └── specular-color.md
├── include
│   ├── backend
│   │   ├── AcquiredImage.h
│   │   ├── BufferDescriptor.h
│   │   ├── CallbackHandler.h
│   │   ├── DriverApiForward.h
│   │   ├── DriverEnums.h
│   │   ├── Handle.h
│   │   ├── PipelineState.h
│   │   ├── PixelBufferDescriptor.h
│   │   ├── Platform.h
│   │   ├── PresentCallable.h
│   │   ├── Program.h
│   │   ├── README.md
│   │   ├── SamplerDescriptor.h
│   │   ├── TargetBufferInfo.h
│   │   └── platforms
│   │       ├── OpenGLPlatform.h
│   │       ├── PlatformCocoaGL.h
│   │       ├── PlatformCocoaTouchGL.h
│   │       ├── PlatformEGL.h
│   │       ├── PlatformEGLAndroid.h
│   │       ├── PlatformEGLHeadless.h
│   │       ├── PlatformGLX.h
│   │       ├── PlatformWGL.h
│   │       ├── PlatformWebGL.h
│   │       └── VulkanPlatform.h
│   ├── camutils
│   │   ├── Bookmark.h
│   │   ├── Manipulator.h
│   │   └── compiler.h
│   ├── filamat
│   │   ├── Enums.h
│   │   ├── IncludeCallback.h
│   │   ├── MaterialBuilder.h
│   │   └── Package.h
│   ├── filament
│   │   ├── Box.h
│   │   ├── BufferObject.h
│   │   ├── Camera.h
│   │   ├── Color.h
│   │   ├── ColorGrading.h
│   │   ├── ColorSpace.h
│   │   ├── DebugRegistry.h
│   │   ├── Engine.h
│   │   ├── Exposure.h
│   │   ├── Fence.h
│   │   ├── FilamentAPI.h
│   │   ├── Frustum.h
│   │   ├── IndexBuffer.h
│   │   ├── IndirectLight.h
│   │   ├── InstanceBuffer.h
│   │   ├── LightManager.h
│   │   ├── Material.h
│   │   ├── MaterialChunkType.h
│   │   ├── MaterialEnums.h
│   │   ├── MaterialInstance.h
│   │   ├── MorphTargetBuffer.h
│   │   ├── Options.h
│   │   ├── RenderTarget.h
│   │   ├── RenderableManager.h
│   │   ├── Renderer.h
│   │   ├── Scene.h
│   │   ├── SkinningBuffer.h
│   │   ├── Skybox.h
│   │   ├── Stream.h
│   │   ├── SwapChain.h
│   │   ├── Texture.h
│   │   ├── TextureSampler.h
│   │   ├── ToneMapper.h
│   │   ├── TransformManager.h
│   │   ├── VertexBuffer.h
│   │   ├── View.h
│   │   └── Viewport.h
│   ├── filament-iblprefilter
│   │   └── IBLPrefilterContext.h
│   ├── filameshio
│   │   └── MeshReader.h
│   ├── geometry
│   │   ├── SurfaceOrientation.h
│   │   ├── TangentSpaceMesh.h
│   │   └── Transcoder.h
│   ├── gltfio
│   │   ├── Animator.h
│   │   ├── AssetLoader.h
│   │   ├── FilamentAsset.h
│   │   ├── FilamentInstance.h
│   │   ├── MaterialProvider.h
│   │   ├── NodeManager.h
│   │   ├── ResourceLoader.h
│   │   ├── TextureProvider.h
│   │   ├── materials
│   │   │   └── uberarchive.h
│   │   └── math.h
│   ├── ibl
│   │   ├── Cubemap.h
│   │   ├── CubemapIBL.h
│   │   ├── CubemapSH.h
│   │   ├── CubemapUtils.h
│   │   ├── Image.h
│   │   └── utilities.h
│   ├── image
│   │   ├── ColorTransform.h
│   │   ├── ImageOps.h
│   │   ├── ImageSampler.h
│   │   ├── Ktx1Bundle.h
│   │   └── LinearImage.h
│   ├── ktxreader
│   │   ├── Ktx1Reader.h
│   │   └── Ktx2Reader.h
│   ├── math
│   │   ├── TMatHelpers.h
│   │   ├── TQuatHelpers.h
│   │   ├── TVecHelpers.h
│   │   ├── compiler.h
│   │   ├── fast.h
│   │   ├── half.h
│   │   ├── mat2.h
│   │   ├── mat3.h
│   │   ├── mat4.h
│   │   ├── mathfwd.h
│   │   ├── norm.h
│   │   ├── quat.h
│   │   ├── scalar.h
│   │   ├── vec2.h
│   │   ├── vec3.h
│   │   └── vec4.h
│   ├── mathio
│   │   └── ostream.h
│   ├── mikktspace
│   │   └── mikktspace.h
│   ├── tsl
│   │   ├── robin_growth_policy.h
│   │   ├── robin_hash.h
│   │   ├── robin_map.h
│   │   └── robin_set.h
│   ├── uberz
│   │   ├── ArchiveEnums.h
│   │   ├── ReadableArchive.h
│   │   └── WritableArchive.h
│   ├── utils
│   │   ├── Allocator.h
│   │   ├── BitmaskEnum.h
│   │   ├── CString.h
│   │   ├── CallStack.h
│   │   ├── Entity.h
│   │   ├── EntityInstance.h
│   │   ├── EntityManager.h
│   │   ├── FixedCapacityVector.h
│   │   ├── Invocable.h
│   │   ├── Log.h
│   │   ├── Mutex.h
│   │   ├── NameComponentManager.h
│   │   ├── Panic.h
│   │   ├── Path.h
│   │   ├── PrivateImplementation-impl.h
│   │   ├── PrivateImplementation.h
│   │   ├── SingleInstanceComponentManager.h
│   │   ├── Slice.h
│   │   ├── SpinLock.h
│   │   ├── StructureOfArrays.h
│   │   ├── algorithm.h
│   │   ├── bitset.h
│   │   ├── compiler.h
│   │   ├── compressed_pair.h
│   │   ├── debug.h
│   │   ├── generic
│   │   │   └── Mutex.h
│   │   ├── memalign.h
│   │   ├── ostream.h
│   │   └── unwindows.h
│   └── viewer
│       ├── AutomationEngine.h
│       ├── AutomationSpec.h
│       ├── RemoteServer.h
│       ├── Settings.h
│       └── ViewerGui.h
└── lib
    └── arm64
        ├── libbackend.a
        ├── libbasis_transcoder.a
        ├── libbluegl.a
        ├── libbluevk.a
        ├── libcamutils.a
        ├── libcivetweb.a
        ├── libdracodec.a
        ├── libfilabridge.a
        ├── libfilaflat.a
        ├── libfilamat.a
        ├── libfilamat_lite.a
        ├── libfilament-iblprefilter.a
        ├── libfilament.a
        ├── libfilameshio.a
        ├── libgeometry.a
        ├── libgltfio.a
        ├── libgltfio_core.a
        ├── libibl-lite.a
        ├── libibl.a
        ├── libimage.a
        ├── libktxreader.a
        ├── libmatdbg.a
        ├── libmeshoptimizer.a
        ├── libmikktspace.a
        ├── libshaders.a
        ├── libsmol-v.a
        ├── libstb.a
        ├── libuberarchive.a
        ├── libuberzlib.a
        ├── libutils.a
        ├── libviewer.a
        ├── libvkshaders.a
        └── libzstd.a

31 directories, 215 files
```
