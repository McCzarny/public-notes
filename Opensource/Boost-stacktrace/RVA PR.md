Instead of printing absolute addresses use relative ones so they can be used later to decode the stack trace.

## Motivation

It’s quite common to release apps without debug symbols while keeping them internally. Currently the lib prints absolute addresses which require the base address to decode them using symbols. With this change, the base address won’t be needed as values are relative to the beginning of the loaded module/binary.

The implementation for unix is **straightforward** as the existing code is used. For Windows, I’ve implemented similar logic using Windows API.

## Manual testing of Windows implementation

Here are 2 runs of `trivial_windbg_lib.exe` with .pdb and without .pdb file:

```shell
>./trivial_windbg_lib.exe
 0# boost::stacktrace::basic_stacktrace<std::allocator<boost::stacktrace::frame> >::init at C:\\Users\\czarneckim\\repositories\\boost\\boost\\stacktrace\\stacktrace.hpp:110
 1# main at C:\\Users\\czarneckim\\repositories\\boost\\libs\\stacktrace\\test\\test_trivial.cpp:14
 2# __scrt_common_main_seh at D:\\a\\_work\\1\\s\\src\\vctools\\crt\\vcstartup\\src\\startup\\exe_common.inl:288
 3# BaseThreadInitThunk in C:\\WINDOWS\\System32\\KERNEL32.DLL
 4# RtlUserThreadStart in C:\\WINDOWS\\SYSTEM32\\ntdll.dll

> mv ./trivial_windbg_lib.pdb{,_} 
> ./trivial_windbg_lib.exe
 0# 0x0000000000001DE3 in C:\\Users\\czarneckim\\repositories\\boost\\bin.v2\\libs\\stacktrace\\test\\trivial_windbg_lib.test\\msvc-14.3\\release\\x86_64\\asynch-exceptions-on\\cxxstd-latest-iso\\debug-symbols-on\\threading-multi\\trivial_windbg_lib.exe
 1# 0x0000000000001FEE in C:\\Users\\czarneckim\\repositories\\boost\\bin.v2\\libs\\stacktrace\\test\\trivial_windbg_lib.test\\msvc-14.3\\release\\x86_64\\asynch-exceptions-on\\cxxstd-latest-iso\\debug-symbols-on\\threading-multi\\trivial_windbg_lib.exe
 2# 0x000000000000246C in C:\\Users\\czarneckim\\repositories\\boost\\bin.v2\\libs\\stacktrace\\test\\trivial_windbg_lib.test\\msvc-14.3\\release\\x86_64\\asynch-exceptions-on\\cxxstd-latest-iso\\debug-symbols-on\\threading-multi\\trivial_windbg_lib.exe
 3# BaseThreadInitThunk in C:\\WINDOWS\\System32\\KERNEL32.DLL
 4# RtlUserThreadStart in C:\\WINDOWS\\SYSTEM32\\ntdll.dll

> mv ./trivial_windbg_lib.pdb{_,}
> winaddr2line.exe -f -e trivial_windbg_lib.pdb 0x0000000000001DE3
boost::stacktrace::basic_stacktrace<std::allocator<boost::stacktrace::frame> >::init
C:\\Users\\czarneckim\\repositories\\boost\\boost\\stacktrace\\stacktrace.hpp:110
```

I’ve also decoded the returned address using x64dbg running `trivial_windbg_lib.exe+0x0000000000001DE3`:

/// IMAGE with a decoded stack trace

## Control

As it was suggested in #180, the new logic is enabled by default, but can be disabled with `BOOST_STACKTRACE_DISABLE_OFFSET_ADDR_BASE` (With non header-only mode, it requires rebuilding the lib)

## Further work

If the implementation would be accepted, I’m happy to do the rest of work, like changes in documentation or test cases. It’s my first contribution to this repo, so I would be very grateful for some guidance what is required.