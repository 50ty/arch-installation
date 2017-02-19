# Boost

This is boost msvc build vs 2017

## Steps

1) Run bootstrap.bat in boost directory

2) Update the project-config.jam: 

```
#!python
import option ; 
 
using msvc : 14.0 : "C:/Program Files (x86)/Microsoft Visual Studio/2017/Community/VC/Tools/MSVC/14.10.24930/bin/HostX64/x64/cl.exe";
 
option.set keep-going : false ; 
```

3) Run "Developer Command Prompt for VS 2017 RC" from Windows Start Menu to boostrap from a shell configured using the x86 vcvars or x64 vcvars.


4) Run b2 in that command prompt:




## Links: 

* http://stackoverflow.com/questions/41464356/build-boost-with-msvc-14-1-vs2017-rc
* https://studiofreya.com/2016/09/29/how-to-build-boost-1-62-with-visual-studio-2015/