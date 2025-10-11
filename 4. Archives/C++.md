A [[Programming language]] with [[Procedural paradigm]] and [[Object oriented paradigm]] support
C++ is also a super set of [[CLang]]

# clang compiler

Basic compilation

```bash
cl /EHsc /GA /MT klog_main.cpp User32.lib
```

# CMake
## Organize VS project

Link: [https://www.programmersought.com/article/11366491955/](https://www.programmersought.com/article/11366491955/)

Grouping source files

```cmake
source_group(<name> [FILES <src>...] [REGULAR_EXPRESSION <regex>])
source_group(TREE <root> [PREFIX <prefix>] [FILES <src>...])
```

Grouping projects

```cmake
# 1. Turn on the switch that allows folder creation
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
 # 2. Add the project to the folder
set_target_properties(hk_camera_test PROPERTIES FOLDER "CMakeTargets")
 # 3. Rename the project automatically created by cmake, the default name is "CMakePredefinedTargets" 
set_property(GLOBAL PROPERTY PREDEFINED_TARGETS_FOLDER "CMakeTargets")
```

## Generate [[ninja]] on [[Window OS]]

[https://stackoverflow.com/questions/31262342/cmake-g-ninja-on-windows-specify-x64](https://stackoverflow.com/questions/31262342/cmake-g-ninja-on-windows-specify-x64)

[https://cmake.org/pipermail/cmake/2019-May/069461.html](https://cmake.org/pipermail/cmake/2019-May/069461.html)

⇒ Load build environment of your VS version then gen Ninja just like Linux (same with nmake)

## Findxxx module

Link: https://cmake.org/cmake/help/v3.8/manual/cmake-developer.7.html?highlight=find#find-modules

