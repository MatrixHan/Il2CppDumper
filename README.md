# Il2CppDumper

[![Build status](https://ci.appveyor.com/api/projects/status/anhqw33vcpmp8ofa?svg=true)](https://ci.appveyor.com/project/Perfare/il2cppdumper/branch/master/artifacts)

中文说明请戳[这里](README.zh-CN.md)

Restore dll from Unity il2cpp binary file (except code)

## Features

* Complete DLL restore (except code)
* Supports ELF, ELF64, Mach-O, PE and NSO format
* Supports Unity 5.3 and greater
* Supports automated IDA script generation
  * rename method
  * name and tag metadata
  * makefunction to improve ida analysis
* Supports Android dumped .so file to bypass protection

## Usage

```
Il2CppDumper.exe <executable-file> <global-metadata> [unityVersion] [mode]
```

Or run `Il2CppDumper.exe` and choose the il2cpp executable file and `global-metadata.dat` file, then enter the information as prompted.

The program will then generate all the output files in current working directory.

### Extraction Modes

#### Manual

The parameters (`CodeRegistration` and `MetadataRegistration`) that are passed to `il2cpp::vm::MetadataCache::Register()` needs to be manually reverse engineered and passed to the program.

#### Auto

Automatically finds the `il2cpp_codegen_register()` function by signature matching and read out the first (`CodeRegistration`) and second (`MetadataRegistration`) parameter passed to the `il2cpp::vm::MetadataCache::Register()` method that will be invoked in the registration function. May not work well due to compiler optimizations.

#### Auto(Plus) - **Recommended**

Matches possible pointers in the data section with some guidance from global-metadata.

Supports metadata version 20 and up

only `CodeRegistration` address can be found on metadata version 16

#### Auto(Symbol)

Uses symbols in the il2cpp binary to locate `CodeRegistration` and `MetadataRegistration`.

Only supports ELF format file.

### Output files

#### dump.cs

Simple decompilation code. It is recommended to get better code from DummyDll using [dnSpy](https://github.com/0xd4d/dnSpy) or other tools.

#### script.py

Requires IDA and IDAPython. Can be loaded in IDA via `File -> Script file`.

#### stringliteral.json

Contains all stringLiteral information

#### DummyDll

Folder, containing all restored dll files

### Configuration

All the configuration options are located in `config.json`

Available options:

* `DumpMethod`, `DumpField`, `DumpProperty`, `DumpAttribute`, `DumpFieldOffset`, `DumpMethodOffset`, `DumpTypeDefIndex`
  * Whether to output these information to dump.cs

* `DummyDll`
  * Whether to generate dummy DLLs

* `MakeFunction`
  * Whether to add the MakeFunction code in script.py

* `ForceIl2CppVersion`, `ForceVersion`
  * If `ForceIl2CppVersion` is `true`, the program will use the version number specified in `ForceVersion` to choose parser for il2cpp binaries (does not affect the choice of metadata parser). This may be useful on some older il2cpp version (e.g. the program may need to use v16 parser on il2cpp v20 (Android) binaries in order to work properly)

## Common errors

#### `ERROR: Metadata file supplied is not valid metadata file.`  

The specified `global-metadata.dat` is invalid and the program cannot recognize it. Make sure you choose the correct file. Sometimes games may obfuscate this file for content protection purposes and so on. Deobfuscating of such files is beyond the scope of this program, so please **DO NOT** file an issue regarding to deobfuscating.

#### `ERROR: Can't use this mode to process file, try another mode.`

Try other extraction modes.

If all automated extraction modes failed with this error and you are sure that the files you supplied are not corrupted/obfuscated, please file an issue with the logs and sample files.

## Credits

- Jumboperson - [Il2CppDumper](https://github.com/Jumboperson/Il2CppDumper)