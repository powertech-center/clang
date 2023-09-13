# [C] Language Extentions
>Get the most out of your code with properties, thread variables, string switches, interprocedural optimizations, and other language extensions.

*Please note that the compiler is under active development, we will update this article as new functionality becomes available. We will be glad for your participation in the development, testing and voicing of proposals. Feel free in [Issues](https://github.com/powertech-center/clang/issues), [Discussions](https://github.com/powertech-center/clang/discussions) or in the telegram group [PowerTech C/C++ Beta](https://t.me/powercpp_beta).*

### \_\_POWERTECH\_\_ definition
Our compiler fork always contains the built-in `__POWERTECH__` definition. Therefore, you can always develop code both for other compilers and taking into account the specifics of our fork.
```cpp
#include <stdio.h>
int main() {
    printf("Hello World"
        #ifdef __clang__
            " from clang compiler"
            #ifdef __POWERTECH__
                " (PowerTech edition)"
            #endif
        #endif
        "!\n"    
    );
    return 0;
}
```

```console
# Output:
Hello World from clang compiler (PowerTech edition)!
```
### [[smallptr]] attribute
In many algorithms that use a large number of pointers, a large amount of memory can be consumed on 64-bit processors, since a normal pointer is 8 bytes. If you can guarantee that the pointer value fits in the first 4 gigabytes of the address space, you can use 32-bit pointers with the `smallptr` attribute. This attribute is equivalent to the `__ptr32 __uptr` pair in the Microsoft compiler.
```cpp
#include <stdio.h>
int main() {
    char* foo = 0;
    char* __attribute__((smallptr)) bar = 0; // C style
    char* [[smallptr]] baz = 0; // C++ style
    printf("sizeof(foo) = %d, sizeof(bar) = %d, sizeof(baz) = %d\n",
        sizeof(foo), sizeof(bar), sizeof(baz));
    return 0;
}
```

```console
# Output:
sizeof(foo) = 8, sizeof(bar) = 4, sizeof(baz) = 4
```

### Fast atomic 128-bit operations
On 64-bit processors, you can perform atomic 128-bit operations. This is done on almost all x86-64 processors using the `cmpxchg16b` command. Since this instruction is absent on the first 64-bit processors, it was emulated by calling the `__sync_val_compare_and_swap_16@PLT` function. The standard compiler also calls the function by default, but if the `-mcx16` compiler option is specified, then the `cmpxchg16b` command is used. In our fork, the opposite is true - `cmpxchg16b` is used by default, but if you use the `-mno-cx16` compiler option, the function will be used.
```cpp
#include <atomic>
bool test(__int128* ptr, __int128 oldval, __int128 newval) {
    return __sync_bool_compare_and_swap(ptr, oldval, newval);
}
```

```x86asm
; PowerTech disassembler:
push    rbx
mov     rbx, rcx
mov     rax, rsi
mov     rcx, r8
lock    cmpxchg16b xmmword ptr [rdi]
sete    al
pop     rbx
ret
```

```x86asm
; Standard (or with "-mno-cx16" option) disassembler, calling __sync_val_compare_and_swap_16@PLT:
push    r14
push    rbx
push    rax
mov     rbx, rdx
mov     r14, rsi
call    __sync_val_compare_and_swap_16@PLT
xor     rdx, rbx
xor     rax, r14
or      rax, rdx
sete    al
add     rsp, 8
pop     rbx
pop     r14
ret
```