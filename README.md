# grpcAndroidServer
Build android app that run as a gRPC server. Now only gRPC C++ version can run on Android.

##  1. Build gRPC C++ version on ubuntu 18.04
### Prerequired
```sh
 $ [sudo] apt update
 $ [sudo] apt upgrade
 
 $ [sudo] apt-get install build-essential autoconf libtool pkg-config

//If you plan to build from source and run tests, install the following as well:

 $ [sudo] apt-get install libgflags-dev libgtest-dev
 $ [sudo] apt-get install clang libc++-dev
```
 ### Clone the repository v1.18.0 (01/24/2019)
```sh
 $ git clone -b $(curl -L https://grpc.io/release) https://github.com/grpc/grpc
 $ cd grpc
 Change the protobuf branch to 3.6.1 in the .gitmodules file
 $ git submodule update --init --recursive
 ```
 ### Build protobuf
 ```sh
 $ [sudo] apt-get install autoconf automake libtool curl make g++ unzip
 $ cd grpc/third_party/protobuf
 
 $ ./autogen.sh
 $ ./configure
 
 $ make
 $ make check
 $ sudo make install
 $ sudo ldconfig # refresh shared library cache.
  ```
 ### Build gRPC
 ```sh
 $ cd grpc/
 $ make
 $ sudo make install
 ```
 ##  2. Build gRPC Server as Android app
 https://github.com/grpc/grpc/tree/master/examples/android/helloworld
 ### Install SDK, NDK, CMAKE from SDK manager in Android studio
 ### Build android example
 ```sh
 $ cd examples/android/helloworld/
 $ ./gradlew assembleDebug
 ```
 ### To build x86 and armeabi-v7a app, adapt build.gradle file in app directory 
 ```sh
    splits {

      // Configures multiple APKs based on ABI.
      abi {

        // Enables building multiple APKs per ABI.
        enable true

        // By default all ABIs are included, so use reset() and include to specify that we only
        // want APKs for x86 and armeabi-v7a.
        // Resets the list of ABIs that Gradle should create APKs for to none.
        reset()

        // Specifies a list of ABIs that Gradle should create APKs for.
        include "x86", "armeabi-v7a"

        // Specifies that we do not want to also generate a universal APK that includes all ABIs.
        universalApk false
      }
    }
  ```
  ```sh
  $ cd examples/android/helloworld/
  $ ./gradlew assembleDebug
  $ cd helloworld/app/build/outputs/apk/debug
  $ ls
  ```
  ### The app-x86-debug.apk and app-armeabi-v7a-debug.apk build success
  ##  3. To test the apk on Android machine
  ### Create Android Virtual Device (Nexus 6 API 27) by Android Studio
  ### Find and start an emulator
  ```sh
  $ emulator -list-avds
  $ emulator -avd Nexus_6_API_27
  $ adb install app-x86-debug.apk
  ```
  ### Test gRPC server in Android
  ### The Hello Android will be showed in response section.
  <img src="https://user-images.githubusercontent.com/45811181/51761669-a7ca0380-2108-11e9-9b11-ea37f31984e6.png" width="300">
  ### WoW!!!
  
  ##  4. Error occure when build Android app
  ### 1 Error occurred while communicating with CMake server.
  Android Studio 3.3 have two Cmake version (3.10 and 3.6). This error occure when 3.10 is choosen. Please remove the 3.10 and install 3.6 from SDK manager. More detail discription: https://stackoverflow.com/questions/47393862/errorerror-occurred-while-communicating-with-cmake-server?rq=1
  ### 2 No toolchains found in the NDK toolchains folder for ABI with prefix: llvm
  NDK 19.0 in Android Studio 3.3 is no support MIPS CPU architecture, So there is no mips64el-linux-android and mipsel-linux-android directory in Android/sdk/ndk-bundle/toolchains directory. A workround is to use symbolic link:
  ```sh
  $ cd  Android/sdk/ndk-bundle/toolchains
  $ ln -s aarch64-linux-android-4.9 mips64el-linux-android
  $ ln -s arm-linux-androideabi-4.9 mipsel-linux-android
  ```
  More detail discription:https://stackoverflow.com/questions/35128229/error-no-toolchains-found-in-the-ndk-toolchains-folder-for-abi-with-prefix-llv
  

  
