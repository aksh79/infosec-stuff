# linux - ELF binaries
- executable and linkable format
### program headers
specifies information about the program
- INTERP - defines library that should be used to load this into memory, usually `ld`
- LOAD - defines a part of file that should be loaded to mem
program heads can be read using `readelf`
```bash
readelf -l binary
```
### section headers
defines sections of an executable, are not necessary but exists as metadata
- .text - executable code
- .plt/.got - dispatch and resolve library calls
- .data - global writable data.
- .rodata - global read only data, such as strings
- .bss - uninitialized global writable data
program heads can be read using `readelf`
```bash
readelf -S binary
```
### symbols
```bash
nm -a binary # will display all the symbols
nm -D binary # will print dynamic imports of the binary.
```
`strip` can be used to strip symbols from a binary
```bash
strip binary
```
### patchelf
```bash
patchelf --set-interpreter /some/other/ld
```
###  tracing system calls and library calls
`strace` and `ltrace` can be used to trace syscalls and library calls.
```bash

```
### objdump
```bash
objdump -M intel -d code
```
### objcopy
```bash
# dump a program section to a file
objcopy --dump-section .text=<outfile> binary
# update the program section from a file
objection --update-section .rodata=rodata.modifies binary
```
## data access
### local variables
- push/pop
- rsp relative (rsp+0xoffset)
- rbp relative (rbp-0xoffset)
### global variables from ELF sections
- rip relative (rip+0xoffset)

