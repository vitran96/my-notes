Tech stack:
- Programming language
	- [[Java]]
	- [[C-Sharp|C#]]
	- [[C++]]
	- [[Javascript]]
	- [[Typescript]]
	- [[SQL]]
	- [[Lua]]
- Tools / Framework:
	- [[Dot NET|.NET]]
	- [[Protobuf]]
	- [[WinForm]]
	- [[WPF]]
	- [[Xml2Code]]
	- [[ReactJS]]
	- [[Material UI]]
	- [[ExpressJS]]
	- [[PostgreSQL]]
	- [[AWS]]

Client:
- [[Porsche]]
- The [[VW]] group

Main product:
- OTX ([[Open Test eXchange system]])
- OTX Framework (OTX IDE)
- OTX Runtime
- OTL (OTX Language)
- OTP (OTX Player)

## Setup environment

- Standard
    - Install VS2017+
    - Setup Nuget, update Nuget online source if needed
    - Install IntelliJ
    - Install JDK 8 - preferred 32bit
    - Install VW MCD 11+ (maybe 14?)
    - Install cmake (or use the one in VS2017+ package)
    - Install Boost
    - Install Xerces + XQilla
    - Install SimFrog
    - Install ThunderBird and setup Emotive email
    - Install Skype
    - Setup default APPS DLLs for Otx-Mapping test cases (For testing)
    - Set up MVCI server
    - Get PDX for testing
    - Clone OTS trunk
    - Clone DiagM trunk
    - Clone tester UnitTest
- Build Setup
    - Install WixProj
- Build Obfuscated
    - Install .Net Reactor
- Generate Document
    - Install GraphViz
    - Install Doxygen
- Dealing with OTX database
    - Install BaseX
- Dealing with VW SP Wrapper for MCD
    - Install MCD with SP dll (Eg: ver 13.99)
- Work with OTX-SU
    - Clone OTX-SU from bitbucket
- RT 7/DiagM on Linux
    - Prepare Ubuntu VM/WSL/Ubuntu machine
    - Install Xerces XQilla
    - Install MCD
    - Install Boost

## VW MCD installer params

```bash
INSTALLLOCATION = C:\\Program Files (x86)\\OpenTestSystem\\VwMcdServer\\
CONFIGDIR = C:\\ProgramData\\OpenTestSystem\\VW-MCD\\config\\
PROJECTDIR = C:\\ProgramData\\OpenTestSystem\\VW-MCD\\Projects\\
EmotiveData = C:\\ProgramData\\OpenTestSystem\\
EmotiveGmbH = C:\\Program Files (x86)\\OpenTestSystem\\
```

## MVCI installer params

```bash
DsaRedistDirectory.B74744B5_55D1_40C4_B98D_7D7C9E48BE9D = C:\\Program Files (x86)\\PRODIS.MCD.Redist\\
ProgramFilesFolder.B74744B5_55D1_40C4_B98D_7D7C9E48BE9D = C:\\Program Files (x86)\\
DsaFolder.B74744B5_55D1_40C4_B98D_7D7C9E48BE9D = C:\\Program Files (x86)\\Dsa\\
Projects.B74744B5_55D1_40C4_B98D_7D7C9E48BE9D = C:\\Program Files (x86)\\Dsa\\Projects\\
ProjectsDemo.B74744B5_55D1_40C4_B98D_7D7C9E48BE9D = C:\\Program Files (x86)\\Dsa\\Projects\\Demo\\
Converter.B74744B5_55D1_40C4_B98D_7D7C9E48BE9D = C:\\Program Files (x86)\\Dsa\\Converter\\
ODX_Files.B74744B5_55D1_40C4_B98D_7D7C9E48BE9D = C:\\Program Files (x86)\\Dsa\\Converter\\ODX-Files\\
ODXDemo.B74744B5_55D1_40C4_B98D_7D7C9E48BE9D = C:\\Program Files (x86)\\Dsa\\Converter\\ODX-Files\\Demo\\
DsaFolder.FEBDED63_DE5D_4DD5_8793_C1BD19DC4523 = C:\\Program Files (x86)\\Dsa\\
PduSim.FEBDED63_DE5D_4DD5_8793_C1BD19DC4523 = C:\\Program Files (x86)\\Dsa\\PduSim\\
Data.FEBDED63_DE5D_4DD5_8793_C1BD19DC4523 = C:\\Program Files (x86)\\Dsa\\PduSim\\Data\\
```

## Install VW Simfrog

Internal only. Guide is in emotive.

## Setup Build environment

### Requirements

Required:
- gcc, g++ 7.5+ (multilib if cross compile)
- cmake 3.15+
- ninja-build
- xerces
- xqilla
- boost
- mcd 17+
- python3
- git

Optional:
- svn
- botan

### Setup Linux distro

```bash
sudo apt update -y 
sudo apt install -y vim tmux
wget -O - <https://gist.githubusercontent.com/vitran96/5c2b5d1733db0ce7657751890b9094d2/raw/0823806eda5a4d786083704d251f1fd63915f0ce/install_kitware_cmake.sh> | bash
sudo apt install -y build-essential ninja-build python3 git
sudo apt install -y gcc-multilib g++-multilib
```

### Botan

#### Clone repo:

```bash
git clone -b 2.11.0 <https://github.com/randombit/botan.git>
cd botan
```

#### Windows

i386, i686

```bash
python configure.py^
  --without-documentation^
  --disable-static-library^
  --link-method=copy^
  --cpu=x86_32^
  --os=windows^
  --cc=msvc^
  --prefix="D:\\Botan\\Win32"^
  --with-build-dir=""
nmake
nmake install
```

amd64

```bash
python configure.py^
  --without-documentation^
  --disable-static-library^
  --link-method=copy^
  --cpu=x86_64^
  --os=windows^
  --cc=msvc^
  --prefix="D:\\Botan\\Win64"^
  --with-build-dir=""
nmake
nmake install
```

#### Linux

i386

```bash
python3 configure.py \\
  --without-documentation \\
  --disable-static-library \\
  --link-method=copy \\
  --cpu=x86_32 \\
  --os=linux \\
  --cc=gcc \\
  --prefix="/opt/botan/x86_32"
cd build32
make -j
make install
```

amd64

```bash
python3 configure.py \\
  --without-documentation \\
  --disable-static-library \\
  --link-method=copy \\
  --cpu=x86_64 \\
  --os=linux \\
  --cc=gcc \\
  --prefix="/opt/botan/x86_64"
cd build64
make -j
make install
```

#### Linux (CXX11_ABI = 0)

amd64

```jsx
python3 configure.py \\
  --without-documentation \\
  --disable-static-library \\
  --cpu=x86_64 \\
  --os=linux \\
  --cc=gcc \\
  --extra-cxxflags="-D_GLIBCXX_USE_CXX11_ABI=0" \\
  --prefix="/opt/botan_abi_0/x86_64"
```

### Boost 1.71

#### Clone repo:

```bash
git clone --recurse-submodules -b boost-1.71.0 <https://github.com/boostorg/boost.git> boost1.71
cd boost1.71
```

or

```bash
git clone -b boost-1.71.0 <https://github.com/boostorg/boost.git> boost1.71
cd boost1.71
git submodule update --init
```

or

```bash
git clone -b boost-1.71.0 <https://github.com/boostorg/boost.git> boost1.71
cd boost1.71
git submodule init
git submodule update
```

**Note:** use `-a` to rebuild

#### Linux

```bash
./bootstrap.sh
```

i686

```bash
./b2 \\
  boost.locale.icu=off \\
  install -j14 \\
  link=static \\
  variant=release \\
  threading=multi \\
  cxxflags=-fPIC \\
  toolset=gcc \\
  address-model=32 \\
  architecture=x86 \\
  --prefix=/opt/boost_1.71/x86_32 \\
  --build-dir=build32
```

amd64

```bash
./b2 \\
  boost.locale.icu=off \\
  install -j14 \\
  link=static \\
  variant=release \\
  threading=multi \\
  cxxflags=-fPIC \\
  toolset=gcc \\
  address-model=64 \\
  architecture=x86 \\
  --prefix=/opt/boost_1.71/x86_64 \\
  --build-dir=build64
```

#### Linux (_GLIBCXX_USE_CXX11_ABI=0)

```bash
./bootstrap.sh
```

i686

```bash
./b2 \\
  boost.locale.icu=off \\
  install -j14 \\
  link=static \\
  variant=release \\
  threading=multi \\
  cxxflags=-fPIC \\
  define=_GLIBCXX_USE_CXX11_ABI=0 \\
  toolset=gcc \\
  address-model=32 \\
  architecture=x86 \\
  --prefix=/opt/boost_1.71_abi_0/x86_32 \\
  --build-dir=build32_abi_0
```

amd64

```bash
./b2 \\
  boost.locale.icu=off \\
  install -j14 \\
  link=static \\
  variant=release \\
  threading=multi \\
  cxxflags=-fPIC \\
  define=_GLIBCXX_USE_CXX11_ABI=0 \\
  toolset=gcc \\
  address-model=64 \\
  architecture=x86 \\
  --prefix=/opt/boost_1.71_abi_0/x86_64 \\
  --build-dir=build64_abi_0
```

### Xerces

#### Clone repo

```bash
git clone -b v3.2.2 <https://github.com/apache/xerces-c.git>
cd xerces-c
```

#### Windows

i386

```bash
mkdir build32
cd build32
cmake^
  -G "Visual Studio 14 2015"^
  -D BUILD_SHARED_LIBS=ON^
  -D CMAKE_INSTALL_PREFIX=C:/Xerces/Win32^
  ../
cmake --build . --config Release
cmake --build . --target install --config Release
```

amd64

```bash
mkdir build64
cd build64
cmake^
  -G "Visual Studio 14 2015 Win64"^
  -D BUILD_SHARED_LIBS=ON^
  -D CMAKE_INSTALL_PREFIX=C:/Xerces/Win64^
  ../
cmake --build . --config Release
cmake --build . --target install --config Release
```

#### Linux

amd64-i386

```bash
mkdir build32
cd build32
cmake \\
  -G "Ninja" \\
  -D CMAKE_C_FLAGS='-m32' \\
  -D CMAKE_CXX_FLAGS='-m32' \\
  -D CMAKE_BUILD_TYPE=Release \\
  -D transcoder=gnuiconv \\
  -D CMAKE_INSTALL_PREFIX=/opt/xerces/x86_32 \\
  ../
ninja
sudo ninja install
```

By environment

```bash
mkdir build64
cd build64
cmake \\
  -G "Ninja" \\
  -D CMAKE_BUILD_TYPE=Release \\
  -D transcoder=gnuiconv \\
  -D CMAKE_INSTALL_PREFIX=/opt/xerces/x86_64 \\
  ../
ninja
sudo ninja install
```

#### Linux (GLIBCXX_USE_CXX11_ABI=0)

amd64-i386

```bash
mkdir build32_abi_0
cd build32_abi_0
cmake \\
  -G "Ninja" \\
  -D CMAKE_C_FLAGS='-m32' \\
  -D CMAKE_CXX_FLAGS='-m32 -D_GLIBCXX_USE_CXX11_ABI=0' \\
  -D CMAKE_BUILD_TYPE=Release \\
  -D transcoder=gnuiconv \\
  -D CMAKE_INSTALL_PREFIX=/opt/xerces_abi_0/x86_32 \\
  ../
ninja
ninja install
```

By environment

```bash
mkdir build64_abi_0
cd build64_abi_0
cmake \\
  -G "Ninja" \\
  -D CMAKE_CXX_FLAGS="-D_GLIBCXX_USE_CXX11_ABI=0" \\
  -D CMAKE_BUILD_TYPE=Release \\
  -D transcoder=gnuiconv \\
  -D CMAKE_INSTALL_PREFIX=/opt/xerces_abi_0/x86_64 \\
  ../
ninja
ninja install
```

### XQilla

#### Clone repo

```bash
wget <https://sourceforge.net/projects/xqilla/files/XQilla-2.3.4.tar.gz>
tar -xzvf XQilla-2.3.4.tar.gz
cd XQilla-2.3.4
```

#### Windows

Currently via VS solution with some configuration to make it actually work

#### Linux

Compile 32bit on 64bit

```bash
mkdir build32
cd build32
../configure \\
  --host=i686-pc-linux-gnu \\
  "CFLAGS=-m32" \\
  "CXXFLAGS=-m32" \\
  "LDFLAGS=-m32" \\
  --with-xerces=/opt/xerces/x86_32 \\
  --prefix=/opt/xqilla/x86_32
make -j
make install
```

By environment

```bash
mkdir build64
cd build64
../configure \\
  --with-xerces=/opt/xerces/x86_64 \\
  --prefix=/opt/xqilla/x86_64
make -j
make install
```

#### Linux (GLIBCXX_USE_CXX11_ABI=0)

Compile 32bit on 64bit

```bash
mkdir build32_abi_0
cd build32_abi_0
../configure \\
  --host=i686-pc-linux-gnu \\
  "CFLAGS=-m32" \\
  "CXXFLAGS=-m32 -D_GLIBCXX_USE_CXX11_ABI=0" \\
  "LDFLAGS=-m32" \\
  --with-xerces=/opt/xerces_abi_0/x86_32 \\
  --prefix=/opt/xqilla_abi_0/x86_32
make -j
sudo make install
```

By environment

```bash
mkdir build64_abi_0
cd build64_abi_0
../configure \\
  "CXXFLAGS=-D_GLIBCXX_USE_CXX11_ABI=0" \\
  --with-xerces=/opt/xerces_abi_0/x86_64 \\
  --prefix=/opt/xqilla_abi_0/x86_64
make -j
sudo make install
```

### Poco

#### Clone repo

```bash
git clone -b poco-1.9.4-release <https://github.com/pocoproject/poco.git>
cd poco
```

#### Windows

i386

```bash
mkdir build32
cd build32
cmake^
  -G "Visual Studio 16 2019"^
  -A Win32^
  -D CMAKE_BUILD_TYPE=Release^
  -D BUILD_SHARED_LIBS=ON^
  -D CMAKE_INSTALL_PREFIX=C:/Poco/Win32^
  -D ENABLE_ENCODINGS=OFF^
  -D ENABLE_ENCODINGS_COMPILER=OFF^
  -D ENABLE_JSON=OFF^
  -D ENABLE_MONGODB=OFF^
  -D ENABLE_REDIS=OFF^
  -D ENABLE_PDF=OFF^
  -D ENABLE_NET=OFF^
  -D ENABLE_NETSSL=OFF^
  -D ENABLE_NETSSL_WIN=OFF^
  -D ENABLE_CRYPTO=OFF^
  -D ENABLE_DATA=OFF^
  -D ENABLE_DATA_SQLITE=OFF^
  -D ENABLE_DATA_MYSQL=OFF^
  -D ENABLE_DATA_ODBC=OFF^
  -D ENABLE_SEVENZIP=OFF^
  -D ENABLE_APACHECONNECTOR=OFF^
  -D ENABLE_CPPPARSER=OFF^
  -D ENABLE_POCODOC=OFF^
  -D ENABLE_PAGECOMPILER=OFF^
  -D ENABLE_PAGECOMPILER_FILE2PAGE=OFF^
  -D FORCE_OPENSSL=OFF^
  -D ENABLE_TESTS=OFF^
  -D POCO_STATIC=OFF^
  -D POCO_UNBUNDLED=OFF^
  -D POCO_MT=OFF^
  -D ENABLE_MSVC_MP=OFF^
  -D ENABLE_XML=ON^
  -D ENABLE_UTIL=ON^
  -D ENABLE_ZIP=ON^
  ..
cmake --build . --config Release
cmake --build . --target install Release
```

amd64

```bash
mkdir build64
cd build64
cmake^
  -G "Visual Studio 16 2019"^
  -A x64^
  -D CMAKE_BUILD_TYPE=Release^
  -D BUILD_SHARED_LIBS=ON^
  -D CMAKE_INSTALL_PREFIX=C:/Poco/Win64^
  -D ENABLE_ENCODINGS=OFF^
  -D ENABLE_ENCODINGS_COMPILER=OFF^
  -D ENABLE_JSON=OFF^
  -D ENABLE_MONGODB=OFF^
  -D ENABLE_REDIS=OFF^
  -D ENABLE_PDF=OFF^
  -D ENABLE_NET=OFF^
  -D ENABLE_NETSSL=OFF^
  -D ENABLE_NETSSL_WIN=OFF^
  -D ENABLE_CRYPTO=OFF^
  -D ENABLE_DATA=OFF^
  -D ENABLE_DATA_SQLITE=OFF^
  -D ENABLE_DATA_MYSQL=OFF^
  -D ENABLE_DATA_ODBC=OFF^
  -D ENABLE_SEVENZIP=OFF^
  -D ENABLE_APACHECONNECTOR=OFF^
  -D ENABLE_CPPPARSER=OFF^
  -D ENABLE_POCODOC=OFF^
  -D ENABLE_PAGECOMPILER=OFF^
  -D ENABLE_PAGECOMPILER_FILE2PAGE=OFF^
  -D FORCE_OPENSSL=OFF^
  -D ENABLE_TESTS=OFF^
  -D POCO_STATIC=OFF^
  -D POCO_UNBUNDLED=OFF^
  -D POCO_MT=OFF^
  -D ENABLE_MSVC_MP=OFF^
  -D ENABLE_XML=ON^
  -D ENABLE_UTIL=ON^
  -D ENABLE_ZIP=ON^
  ..
cmake --build . --config Release
cmake --build . --target install --config Release
```

#### Linux

Modify the source code before build

```bash
sed -i 's/Util XML //g' Zip/CMakeLists.txt
```

amd64-i386

```bash
mkdir build32
cd build32
cmake \\
  -G "Ninja" \\
  -D CMAKE_C_FLAGS='-m32' \\
  -D CMAKE_CXX_FLAGS='-m32' \\
  -D CMAKE_BUILD_TYPE=Release \\
  -D BUILD_SHARED_LIBS=ON \\
  -D CMAKE_INSTALL_PREFIX=/opt/poco/x86_32 \\
  -D ENABLE_ENCODINGS=OFF \\
  -D ENABLE_ENCODINGS_COMPILER=OFF \\
  -D ENABLE_JSON=OFF \\
  -D ENABLE_MONGODB=OFF \\
  -D ENABLE_REDIS=OFF \\
  -D ENABLE_PDF=OFF \\
  -D ENABLE_NET=OFF \\
  -D ENABLE_NETSSL=OFF \\
  -D ENABLE_NETSSL_WIN=OFF \\
  -D ENABLE_CRYPTO=OFF \\
  -D ENABLE_DATA=OFF \\
  -D ENABLE_DATA_SQLITE=OFF \\
  -D ENABLE_DATA_MYSQL=OFF \\
  -D ENABLE_DATA_ODBC=OFF \\
  -D ENABLE_SEVENZIP=OFF \\
  -D ENABLE_APACHECONNECTOR=OFF \\
  -D ENABLE_CPPPARSER=OFF \\
  -D ENABLE_POCODOC=OFF^
  -D ENABLE_PAGECOMPILER=OFF \\
  -D ENABLE_PAGECOMPILER_FILE2PAGE=OFF \\
  -D FORCE_OPENSSL=OFF \\
  -D ENABLE_TESTS=OFF \\
  -D POCO_STATIC=OFF \\
  -D POCO_UNBUNDLED=OFF \\
  -D POCO_MT=OFF \\
  -D ENABLE_MSVC_MP=OFF \\
  -D ENABLE_XML=ON \\
  -D ENABLE_UTIL=ON \\
  -D ENABLE_ZIP=ON \\
  ..
ninja
sudo ninja install
```

amd64

```bash
mkdir build64
cd build64
cmake \\
  -G "Ninja" \\
  -D CMAKE_BUILD_TYPE=Release \\
  -D BUILD_SHARED_LIBS=ON \\
  -D CMAKE_INSTALL_PREFIX=/opt/poco/x86_64 \\
  -D ENABLE_ENCODINGS=OFF \\
  -D ENABLE_ENCODINGS_COMPILER=OFF \\
  -D ENABLE_JSON=OFF \\
  -D ENABLE_MONGODB=OFF \\
  -D ENABLE_REDIS=OFF \\
  -D ENABLE_PDF=OFF \\
  -D ENABLE_NET=OFF \\
  -D ENABLE_NETSSL=OFF \\
  -D ENABLE_NETSSL_WIN=OFF \\
  -D ENABLE_CRYPTO=OFF \\
  -D ENABLE_DATA=OFF \\
  -D ENABLE_DATA_SQLITE=OFF \\
  -D ENABLE_DATA_MYSQL=OFF \\
  -D ENABLE_DATA_ODBC=OFF \\
  -D ENABLE_SEVENZIP=OFF \\
  -D ENABLE_APACHECONNECTOR=OFF \\
  -D ENABLE_CPPPARSER=OFF \\
  -D ENABLE_POCODOC=OFF^
  -D ENABLE_PAGECOMPILER=OFF \\
  -D ENABLE_PAGECOMPILER_FILE2PAGE=OFF \\
  -D FORCE_OPENSSL=OFF \\
  -D ENABLE_TESTS=OFF \\
  -D POCO_STATIC=OFF \\
  -D POCO_UNBUNDLED=OFF \\
  -D POCO_MT=OFF \\
  -D ENABLE_MSVC_MP=OFF \\
  -D ENABLE_XML=ON \\
  -D ENABLE_UTIL=ON \\
  -D ENABLE_ZIP=ON \\
  ..
ninja
sudo ninja install
```

###  Protobuf

#### Clone repo:

```bash
git clone -b v3.10.1 <https://github.com/protocolbuffers/protobuf.git>
cd protobuf
```

#### Windows

i386, i686

```bash
mkdir build32
cd build32
cmake^
  -G "Visual Studio 16 2019"^
  -A Win32^
  -D CMAKE_BUILD_TYPE=Release^
  -D BUILD_SHARED_LIBS=OFF^
  -D CMAKE_INSTALL_PREFIX=D:/Protobuf/Win32^
  -D protobuf_MSVC_STATIC_RUNTIME=OFF^
  -D protobuf_BUILD_TESTS=OFF^
  -D protobuf_BUILD_CONFORMANCE=OFF^
  -D protobuf_BUILD_EXAMPLES=OFF^
  -D protobuf_BUILD_PROTOC_BINARIES=OFF^
  -D protobuf_WITH_ZLIB=OFF^
  -D protobuf_VERBOSE=OFF^
  ../cmake
cmake --build . --config Release
cmake --build . --target install --config Release
```

amd64##

```bash
mkdir build64
cd build64
cmake^
  -G "Visual Studio 16 2019"^
  -A x64^
  -D CMAKE_BUILD_TYPE=Release^
  -D BUILD_SHARED_LIBS=OFF^
  -D CMAKE_INSTALL_PREFIX=D:/Protobuf/Win64^
  -D protobuf_MSVC_STATIC_RUNTIME=OFF^
  -D protobuf_BUILD_TESTS=OFF^
  -D protobuf_BUILD_CONFORMANCE=OFF^
  -D protobuf_BUILD_EXAMPLES=OFF^
  -D protobuf_BUILD_PROTOC_BINARIES=OFF^
  -D protobuf_WITH_ZLIB=OFF^
  -D protobuf_VERBOSE=OFF^
  ../cmake
cmake --build . --config Release
cmake --build . --target install --config Release
```

# Linux

amd64-i386

```bash
mkdir build32
cd build32
cmake \\
  -G "Ninja" \\
  -D CMAKE_C_FLAGS='-m32' \\
  -D CMAKE_CXX_FLAGS='-m32' \\
  -D CMAKE_BUILD_TYPE=Release \\
  -D BUILD_SHARED_LIBS=OFF \\
  -D CMAKE_INSTALL_PREFIX=/opt/protobuf/x86_32 \\
  -D CMAKE_POSITION_INDEPENDENT_CODE=ON \\
  -D protobuf_MSVC_STATIC_RUNTIME=OFF \\
  -D protobuf_BUILD_TESTS=OFF \\
  -D protobuf_BUILD_CONFORMANCE=OFF \\
  -D protobuf_BUILD_EXAMPLES=OFF \\
  -D protobuf_BUILD_PROTOC_BINARIES=OFF \\
  -D protobuf_WITH_ZLIB=OFF \\
  -D protobuf_VERBOSE=OFF \\
  ../cmake
ninja
sudo ninja insninjtall
```

amd64

```bash
mkdir build64
cd build64
cmake \\
  -G "Ninja" \\
  -D CMAKE_BUILD_TYPE=Release \\
  -D BUILD_SHARED_LIBS=OFF \\
  -D CMAKE_INSTALL_PREFIX=/opt/protobuf/x86_64 \\
  -D CMAKE_POSITION_INDEPENDENT_CODE=ON \\
  -D protobuf_MSVC_STATIC_RUNTIME=OFF \\
  -D protobuf_BUILD_TESTS=OFF \\
  -D protobuf_BUILD_CONFORMANCE=OFF \\
  -D protobuf_BUILD_EXAMPLES=OFF \\
  -D protobuf_BUILD_PROTOC_BINARIES=OFF \\
  -D protobuf_WITH_ZLIB=OFF \\
  -D protobuf_VERBOSE=OFF \\
  ../cmake
ninja
sudo ninja install
```

### Install Protobuf 3.19.3 (view)

#### Clone repo:

```bash
git clone -b v3.19.3 <https://github.com/protocolbuffers/protobuf.git> protobuf3.19.3
cd protobuf3.19.3
```

#### Windows

Init setup

```bash
copy cmake\\CMakeLists.txt deleteMe.txt
findstr /v CMAKE_MSVC_RUNTIME_LIBRARY deleteMe.txt > cmake\\CMakeLists.txt
del deleteMe.txt
```

#### VS solution

i386, i686

```bash
mkdir build32
cd build32
cmake^
  -G "Visual Studio 16 2019"^
  -A Win32^
  -D CMAKE_BUILD_TYPE=Release^
  -D BUILD_SHARED_LIBS=OFF^
  -D CMAKE_INSTALL_PREFIX=D:/Protobuf_3.19.3/Win32^
  -D protobuf_MSVC_STATIC_RUNTIME=OFF^
  -D protobuf_BUILD_TESTS=OFF^
  -D protobuf_BUILD_CONFORMANCE=OFF^
  -D protobuf_BUILD_EXAMPLES=OFF^
  -D protobuf_BUILD_PROTOC_BINARIES=OFF^
  -D protobuf_WITH_ZLIB=OFF^
  -D protobuf_VERBOSE=OFF^
  ../cmake
cmake --build . --config Release
cmake --build . --target install --config Release
```

amd64

```bash
mkdir build64
cd build64
cmake^
  -G "Visual Studio 16 2019"^
  -A x64^
  -D CMAKE_BUILD_TYPE=Release^
  -D BUILD_SHARED_LIBS=OFF^
  -D CMAKE_INSTALL_PREFIX=D:/Protobuf_3.19.3/Win64^
  -D protobuf_MSVC_STATIC_RUNTIME=OFF^
  -D protobuf_BUILD_TESTS=OFF^
  -D protobuf_BUILD_CONFORMANCE=OFF^
  -D protobuf_BUILD_EXAMPLES=OFF^
  -D protobuf_BUILD_PROTOC_BINARIES=OFF^
  -D protobuf_WITH_ZLIB=OFF^
  -D protobuf_VERBOSE=OFF^
  ../cmake
cmake --build . --config Release
cmake --build . --target install --config Release
```

#### Ninja

i386, i686

```bash
mkdir build32
cd build32
cmake^
  -G "Ninja"^
  -D CMAKE_BUILD_TYPE=Release^
  -D BUILD_SHARED_LIBS=OFF^
  -D CMAKE_INSTALL_PREFIX=D:/Protobuf_3.19.3/Win32^
  -D protobuf_MSVC_STATIC_RUNTIME=OFF^
  -D protobuf_BUILD_TESTS=OFF^
  -D protobuf_BUILD_CONFORMANCE=OFF^
  -D protobuf_BUILD_EXAMPLES=OFF^
  -D protobuf_BUILD_PROTOC_BINARIES=OFF^
  -D protobuf_WITH_ZLIB=OFF^
  -D protobuf_VERBOSE=OFF^
  ../cmake
ninja
ninja install
```

amd64

```bash
mkdir build64
cd build64
cmake^
  -G "Ninja"^
  -D CMAKE_BUILD_TYPE=Release^
  -D BUILD_SHARED_LIBS=OFF^
  -D CMAKE_INSTALL_PREFIX=D:/Protobuf_3.19.3/Win64^
  -D protobuf_MSVC_STATIC_RUNTIME=OFF^
  -D protobuf_BUILD_TESTS=OFF^
  -D protobuf_BUILD_CONFORMANCE=OFF^
  -D protobuf_BUILD_EXAMPLES=OFF^
  -D protobuf_BUILD_PROTOC_BINARIES=OFF^
  -D protobuf_WITH_ZLIB=OFF^
  -D protobuf_VERBOSE=OFF^
  ../cmake
ninja
ninja install
```

#### Linux

amd64-i386

```bash
mkdir build32
cd build32
cmake \\
  -G "Ninja" \\
  -D CMAKE_C_FLAGS='-m32' \\
  -D CMAKE_CXX_FLAGS='-m32' \\
  -D CMAKE_BUILD_TYPE=Release \\
  -D BUILD_SHARED_LIBS=OFF \\
  -D CMAKE_INSTALL_PREFIX=/opt/protobuf_3.19.3/x86_32 \\
  -D CMAKE_POSITION_INDEPENDENT_CODE=ON \\
  -D protobuf_MSVC_STATIC_RUNTIME=OFF \\
  -D protobuf_BUILD_TESTS=OFF \\
  -D protobuf_BUILD_CONFORMANCE=OFF \\
  -D protobuf_BUILD_EXAMPLES=OFF \\
  -D protobuf_BUILD_PROTOC_BINARIES=OFF \\
  -D protobuf_WITH_ZLIB=OFF \\
  -D protobuf_VERBOSE=OFF \\
  ../cmake
ninja
sudo ninja install
```

amd64

```bash
mkdir build64
cd build64
cmake \\
  -G "Ninja" \\
  -D CMAKE_BUILD_TYPE=Release \\
  -D BUILD_SHARED_LIBS=OFF \\
  -D CMAKE_INSTALL_PREFIX=/opt/protobuf_3.19.3/x86_64 \\
  -D CMAKE_POSITION_INDEPENDENT_CODE=ON \\
  -D protobuf_MSVC_STATIC_RUNTIME=OFF \\
  -D protobuf_BUILD_TESTS=OFF \\
  -D protobuf_BUILD_CONFORMANCE=OFF \\
  -D protobuf_BUILD_EXAMPLES=OFF \\
  -D protobuf_BUILD_PROTOC_BINARIES=OFF \\
  -D protobuf_WITH_ZLIB=OFF \\
  -D protobuf_VERBOSE=OFF \\
  ../cmake
ninja
sudo ninja install
```

## Packing VW MCD for build server

```bash
mcd_32=/opt/vwmcd_17/x86_32
mcd_64=/opt/vwmcd_17/x86_64

mkdir -p $mcd_32
mkdir -p $mcd_64

# do this for each installation
#cp $(find /opt/vw/lib -type f -xtype f) /opt/vwmcd_17/x86_?
#cp $(find /opt/dsa/lib -type f -xtype f) /opt/vwmcd_17/x86_?
#

createSymLinkFilesAtDir() {
  local dir=$1
  for item in $(ls $dir)
  do
    local temp=$(echo "${item%%.*}")
    ln -s $item $dir/$temp.so
  done
}

createSymLinkFilesAtDir $mcd_32
createSymLinkFilesAtDir $mcd_64
```

Symlink creation hardcode

```bash
for item in $(ls $mcd_32)
do
  temp=$(echo "${item%%.*}")
  ln -s $item $mcd_32/$temp.so
done

for item in $(ls $mcd_64)
do
  temp=$(echo "${item%%.*}")
  ln -s $item $mcd_64/$temp.so
done
```

Clean up symbolic

```bash
rm -f libboost_atomic.so
rm -f libboost_filesystem.so
rm -f libboost_system.so
rm -f libboost_thread.so
rm -f libmcdkernel.so
rm -f libpbl_mcd.so
rm -f libsystemparameterext.so
```

## Diag Manager build scripts

### Windows

#### i386

```bash
cmake^
  -G "Visual Studio 16 2019"^
  -A Win32^
  -S "D:/Workspace/Products/OtxDiagManager/trunk"^
  -B "D:/cmakeB/diag"^
  -D CMAKE_BUILD_TYPE=RelWithDebInfo^
  -D USE_VWMCD_LINK_LIBRARY=OFF^
  -D USE_VWMCDSP=OFF^
  -D ADD_DL_LIBS=OFF^
  -D USE_VWMCD90=OFF^
  -D USE_EXPERIMENTAL=OFF^
  -D BOOST_ROOT="D:/Boost/x32"^
  -D Botan_ROOT="D:/Botan/Win32"^
  -D Protobuf_ROOT="D:/Protobuf_3.19.3/Win32"
```

#### amd64

```bash
cmake^
  -G "Visual Studio 16 2019"^
  -A x64^
  -S "D:/Workspace/Products/OtxDiagManager/trunk"^
  -B "D:/cmakeB/diag64"^
  -D CMAKE_BUILD_TYPE=RelWithDebInfo^
  -D USE_VWMCD_LINK_LIBRARY=OFF^
  -D USE_VWMCDSP=OFF^
  -D ADD_DL_LIBS=OFF^
  -D USE_VWMCD90=OFF^
  -D USE_EXPERIMENTAL=OFF^
  -D BOOST_ROOT="D:/Boost/x64"^
  -D Botan_ROOT="D:/Botan/Win64"^
  -D Protobuf_ROOT="D:/Protobuf_3.19.3/Win64"
```

### Linux

**Note**: add `-D CMAKE_CXX_STANDARD_LIBRARIES="-ldl"` if error related to “dl” occur

#### i386 (on amd64)

```bash
cmake \\
  -G "Ninja" \\
  -S "/mnt/d/Workspace/Products/OtxDiagManager/trunk" \\
  -B "/mnt/d/cmakeB/diag_debian10_32" \\
  -D CMAKE_SYSTEM_PROCESSOR=i386 \\
  -D CMAKE_SYSTEM_NAME=Linux \\
  -D CMAKE_C_FLAGS='-m32' \\
  -D CMAKE_CXX_FLAGS='-m32' \\
  -D CMAKE_BUILD_TYPE=RelWithDebInfo \\
  -D USE_VWMCD_LINK_LIBRARY=OFF \\
  -D USE_VWMCDSP=OFF \\
  -D ADD_DL_LIBS=ON \\
  -D USE_VWMCD90=OFF \\
  -D USE_EXPERIMENTAL=OFF \\
  -D BOOST_ROOT="/opt/boost_1.71/x86_32" \\
  -D Botan_ROOT="/opt/botan/x86_32" \\
  -D Protobuf_ROOT="/opt/protobuf_3.19.3/x86_32"
ninja
```

#### amd64

```bash
cmake \\
  -G "Ninja" \\
  -S "/mnt/d/Workspace/Products/OtxDiagManager/trunk" \\
  -B "/mnt/d/cmakeB/diag_debian10_64" \\
  -D CMAKE_BUILD_TYPE=RelWithDebInfo \\
  -D USE_VWMCD_LINK_LIBRARY=OFF \\
  -D USE_VWMCDSP=OFF \\
  -D ADD_DL_LIBS=ON \\
  -D USE_VWMCD90=OFF \\
  -D USE_EXPERIMENTAL=OFF \\
  -D BOOST_ROOT="/opt/boost_1.71/x86_64" \\
  -D Botan_ROOT="/opt/botan/x86_64" \\
  -D Protobuf_ROOT="/opt/protobuf_3.19.3/x86_64"
ninja
```

### Linux (GLIBCXX_USE_CXX11_ABI=0)

**Note**: add `-D CMAKE_CXX_STANDARD_LIBRARIES="-ldl"` if error related to “dl” occur

#### i386 (on amd64)

```bash
cmake \\
  -G "Ninja" \\
  -S "/mnt/d/Workspace/Products/OtxDiagManager/trunk" \\
  -B "/mnt/d/cmakeB/diag_debian10_32_abi_0" \\
  -D CMAKE_SYSTEM_PROCESSOR=i386 \\
  -D CMAKE_SYSTEM_NAME=Linux \\
  -D CMAKE_BUILD_TYPE=RelWithDebInfo \\
  -D USE_VWMCD_LINK_LIBRARY=OFF \\
  -D USE_VWMCDSP=OFF \\
  -D ADD_DL_LIBS=ON \\
  -D USE_VWMCD90=OFF \\
  -D CMAKE_C_FLAGS='-m32' \\
  -D CMAKE_CXX_FLAGS="-m32 -D_GLIBCXX_USE_CXX11_ABI=0" \\
  -D USE_EXPERIMENTAL=OFF \\
  -D BOOST_ROOT="/opt/boost_1.71/x86_32" \\
  -D Botan_ROOT="/opt/botan/x86_32" \\
  -D Protobuf_ROOT="/opt/protobuf_3.19.3/x86_32"
ninja
```

#### amd64

```bash
cmake \\
  -G "Ninja" \\
  -S "/mnt/d/Workspace/Products/OtxDiagManager/trunk" \\
  -B "/mnt/d/cmakeB/diag_debian10_64_abi_0" \\
  -D CMAKE_BUILD_TYPE=RelWithDebInfo \\
  -D USE_VWMCD_LINK_LIBRARY=OFF \\
  -D USE_VWMCDSP=OFF \\
  -D ADD_DL_LIBS=ON \\
  -D USE_VWMCD90=OFF \\
  -D CMAKE_CXX_FLAGS="-D_GLIBCXX_USE_CXX11_ABI=0" \\
  -D USE_EXPERIMENTAL=OFF \\
  -D BOOST_ROOT="/opt/boost_1.71/x86_64" \\
  -D Botan_ROOT="/opt/botan/x86_64" \\
  -D Protobuf_ROOT="/opt/protobuf_3.19.3/x86_64"
ninja
```

## Runtime 7 install scripts

### Windows

#### i386

```bash
cmake^
  -G "Visual Studio 16 2019"^
  -A Win32^
  -S "D:/Workspace/Products/OpenTestSystem/trunk/src/Cpp"^
  -B "D:/cmakeB/rt7"^
  -D CMAKE_BUILD_TYPE=RelWithDebInfo^
  -D CPP_BUILD_TYPE="x32"^
  -D USE_VWMCD_LINK_LIBRARY=OFF^
  -D USE_VWMCDSP=OFF^
  -D ADD_DL_LIBS=OFF^
  -D USE_VWMCD90=OFF^
  -D Rt7InstallDir="D:/cmakeB/rt7/packing"^
  -D OtxDiagManagerSourceDir="D:/Workspace/Products/OpenTestSystem/trunk/src/Libs/Win32"^
  -D USE_EXPERIMENTAL=OFF^
  -D BOOST_ROOT="D:/Boost_1.71/x32"^
  -D XQilla_ROOT="D:/XQilla/Win32"^
  -D XercesC_ROOT="D:/XercesC/Win32"^
  -D Botan_ROOT="D:/Botan/Win32"^
  -D Poco_ROOT="D:/Poco/Win32"^
  -D Protobuf_ROOT="D:/Protobuf_3.19.3/Win32"
```

#### amd64

```bash
cmake^
  -G "Visual Studio 16 2019"^
  -A x64^
  -S "D:/Workspace/Products/OpenTestSystem/trunk/src/Cpp"^
  -B "D:/cmakeB/rt7_64"^
  -D CMAKE_BUILD_TYPE=RelWithDebInfo^
  -D CPP_BUILD_TYPE="x64"^
  -D USE_VWMCD_LINK_LIBRARY=OFF^
  -D USE_VWMCDSP=OFF^
  -D ADD_DL_LIBS=OFF^
  -D USE_VWMCD90=OFF^
  -D Rt7InstallDir="D:/cmakeB/rt7_64/packing"^
  -D OtxDiagManagerSourceDir="D:/Workspace/Products/OpenTestSystem/trunk/src/Libs/Win64"^
  -D USE_EXPERIMENTAL=OFF^
  -D BOOST_ROOT="D:/Boost_1.71/x64"^
  -D XQilla_ROOT="D:/XQilla/Win64"^
  -D XercesC_ROOT="D:/XercesC/Win64"^
  -D Botan_ROOT="D:/Botan/Win64"^
  -D Poco_ROOT="D:/Poco/Win64"^
  -D Protobuf_ROOT="D:/Protobuf_3.19.3/Win64"
```

#### Linux

Not too much staff at the moment.

#### Notes:

- Add `-D CMAKE_CXX_FLAGS="-pthread"` if build on Ubuntu 20.04

#### i686

```bash
cmake \\
  -G "Ninja" \\
  -S "/mnt/d/Workspace/Products/OpenTestSystem/trunk/src/Cpp" \\
  -B "/mnt/d/cmakeB/rt7_debian10_32" \\
  -D CMAKE_C_FLAGS='-m32' \\
  -D CMAKE_CXX_FLAGS='-m32' \\
  -D CMAKE_BUILD_TYPE=RelWithDebInfo \\
  -D CPP_BUILD_TYPE="x32" \\
  -D USE_VWMCD_LINK_LIBRARY=OFF \\
  -D USE_VWMCDSP=OFF \\
  -D ADD_DL_LIBS=ON \\
  -D USE_VWMCD90=OFF \\
  -D Rt7InstallDir="/mnt/d/cmakeB/rt7_debian10_32/packing" \\
  -D LUA_MATH_LIBRARY=/usr/lib32/libm.so \\
  -D OtxDiagManagerSourceDir="/mnt/d/cmakeB/diag_debian10_32/bin" \\
  -D USE_EXPERIMENTAL=OFF \\
  -D BOOST_ROOT="/opt/boost_1.71/x86_32" \\
  -D XQilla_ROOT="/opt/xqilla/x86_32" \\
  -D XercesC_ROOT="/opt/xerces/x86_32" \\
  -D Botan_ROOT="/opt/botan/x86_32" \\
  -D Poco_ROOT="/opt/poco/x86_32" \\
  -D Protobuf_ROOT="/opt/protobuf_3.19.3/x86_32"
ninja

```

#### amd64

```bash
cmake \\
  -G "Ninja" \\
  -S "/mnt/d/Workspace/Products/OpenTestSystem/trunk/src/Cpp" \\
  -B "/mnt/d/cmakeB/rt7_debian10_64" \\
  -D CMAKE_BUILD_TYPE=RelWithDebInfo \\
  -D CPP_BUILD_TYPE="x64" \\
  -D USE_VWMCD_LINK_LIBRARY=OFF \\
  -D USE_VWMCDSP=OFF \\
  -D ADD_DL_LIBS=ON \\
  -D USE_VWMCD90=OFF \\
  -D Rt7InstallDir="/mnt/d/cmakeB/rt7_debian10_64/packing" \\
  -D OtxDiagManagerSourceDir="/mnt/d/cmakeB/diag_debian10_64/bin" \\
  -D USE_EXPERIMENTAL=OFF \\
  -D BOOST_ROOT="/opt/boost_1.71/x86_64" \\
  -D XQilla_ROOT="/opt/xqilla/x86_64" \\
  -D XercesC_ROOT="/opt/xerces/x86_64" \\
  -D Botan_ROOT="/opt/botan/x86_64" \\
  -D Poco_ROOT="/opt/poco/x86_64" \\
  -D Protobuf_ROOT="/opt/protobuf_3.19.3/x86_64"
ninja

```

### Linux (GLIBCXX_USE_CXX11_ABI=0)

#### Notes:

- Add `-D CMAKE_CXX_FLAGS="-pthread"` if build on Ubuntu 20.04

#### i686

```bash
cmake \\
  -G "Ninja" \\
  -S "/mnt/d/Workspace/Products/OpenTestSystem/trunk/src/Cpp" \\
  -B "/mnt/d/cmakeB/rt7_debian10_32_abi_0" \\
  -D CMAKE_C_FLAGS='-m32' \\
  -D CMAKE_CXX_FLAGS='-m32 -D_GLIBCXX_USE_CXX11_ABI=0' \\
  -D CMAKE_BUILD_TYPE=RelWithDebInfo \\
  -D CPP_BUILD_TYPE="x32" \\
  -D USE_VWMCD_LINK_LIBRARY=OFF \\
  -D USE_VWMCDSP=OFF \\
  -D ADD_DL_LIBS=ON \\
  -D USE_VWMCD90=OFF \\
  -D Rt7InstallDir="/mnt/d/cmakeB/rt7_debian10_32_abi_0/packing" \\
  -D LUA_MATH_LIBRARY=/usr/lib32/libm.so \\
  -D OtxDiagManagerSourceDir="/mnt/d/cmakeB/diagM_u2004_32_abi_0/bin" \\
  -D USE_EXPERIMENTAL=OFF \\
  -D BOOST_ROOT="/opt/boost_1.71/x86_32" \\
  -D XQilla_ROOT="/opt/xqilla/x86_32" \\
  -D XercesC_ROOT="/opt/xerces/x86_32" \\
  -D Botan_ROOT="/opt/botan/x86_32" \\
  -D Poco_ROOT="/opt/poco/x86_32" \\
  -D Protobuf_ROOT="/opt/protobuf_3.19.3/x86_32"
ninja

```

#### amd64

```bash
cmake \\
  -G "Ninja" \\
  -S "/mnt/d/Workspace/Products/OpenTestSystem/trunk/src/Cpp" \\
  -B "/mnt/d/cmakeB/rt7_debian10_abi_0" \\
  -D CMAKE_BUILD_TYPE=RelWithDebInfo \\
  -D CPP_BUILD_TYPE="x64" \\
  -D USE_VWMCD_LINK_LIBRARY=OFF \\
  -D USE_VWMCDSP=OFF \\
  -D ADD_DL_LIBS=ON \\
  -D USE_VWMCD90=OFF \\
  -D CMAKE_CXX_FLAGS="-D_GLIBCXX_USE_CXX11_ABI=0" \\
  -D Rt7InstallDir="/mnt/d/cmakeB/rt7_debian10_abi_0/packing" \\
  -D OtxDiagManagerSourceDir="/mnt/d/cmakeB/diagM_u2004_abi_0/bin" \\
  -D USE_EXPERIMENTAL=OFF \\
  -D BOOST_ROOT="/opt/boost_1.71/x86_64" \\
  -D XQilla_ROOT="/opt/xqilla/x86_64" \\
  -D XercesC_ROOT="/opt/xerces/x86_64" \\
  -D Botan_ROOT="/opt/botan/x86_64" \\
  -D Poco_ROOT="/opt/poco/x86_64" \\
  -D Protobuf_ROOT="/opt/protobuf_3.19.3/x86_64"
ninja
```