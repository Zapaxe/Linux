# Linux

Linux x86_64 syscall bindings for [Rux](https://rux-lang.dev).

## Installation

You can install the package using Rux CLI

```sh
rux add Linux
```

Add `Linux` to your `Rux.toml`:

```toml
[Dependencies]
Linux = "*"
```

## Overview

This package exposes a small Linux syscall foundation. It does not depend on
libc; calls are backed by Rux compiler support for
`__rux_linux_syscall0` through `__rux_linux_syscall6`.

### Raw syscall wrappers

| Function | Description |
| --- | --- |
| `Syscall0` ... `Syscall6` | Invoke a Linux x86_64 syscall with 0 to 6 arguments |
| `IsError(result)` | Returns `true` for Linux negative errno results |
| `Errno(result)` | Converts a negative errno result to a positive errno value |

### Basic helpers

| Function | Description |
| --- | --- |
| `Read(fd, buffer, count)` | Call `read(2)` |
| `Write(fd, buffer, count)` | Call `write(2)` |
| `Close(fd)` | Call `close(2)` |
| `Exit(code)` | Call `exit(2)` |
| `GetPid()` | Call `getpid(2)` |
| `Mmap(addr, length, prot, flags, fd, offset)` | Call `mmap(2)` |
| `Munmap(addr, length)` | Call `munmap(2)` |
| `Brk(addr)` | Call `brk(2)` |

All syscall helpers return the raw Linux result as `int64`: non-negative values
are successful results, while values from `-4095` through `-1` represent errno.

## Constants

- File descriptors: `Stdin`, `Stdout`, `Stderr`
- Syscall numbers: `SYS_READ`, `SYS_WRITE`, `SYS_CLOSE`, `SYS_MMAP`,
  `SYS_MUNMAP`, `SYS_BRK`, `SYS_GETPID`, `SYS_EXIT`
- Memory flags: `PROT_READ`, `PROT_WRITE`, `PROT_EXEC`, `MAP_PRIVATE`,
  `MAP_ANONYMOUS`

## Example

```rux
import Linux::{ Write, Stdout };

func Main() -> int32 {
    let msg = "hello from Linux\n";
    let result = Write(Stdout, msg.data, msg.length);
    return result < 0i64 ? 1i32 : 0i32;
}
```

The `Examples/Smoke` package exercises `Read`, `Write`, `GetPid`,
`Mmap`, and `Munmap`:

```sh
cd Examples/Smoke
rux build
printf 'hello\n' | ./Bin/Debug/LinuxSmoke
```

## Requirements

- Linux x86_64
- A Rux compiler with Linux syscall thunk support

## License

[MIT](LICENSE)
