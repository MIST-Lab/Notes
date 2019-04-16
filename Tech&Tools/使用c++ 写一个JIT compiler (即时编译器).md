# 使用c++ 写一个JIT compiler (即时编译器)

原文出处: <https://solarianprogrammer.com/2018/01/10/writing-minimal-x86-64-jit-compiler-cpp/>

github repo: <https://github.com/sol-prog/x86-64-minimal-JIT-compiler-Cpp>

Writing a minimal x86-64 JIT compiler in C++ - Part 1 

- [Part 1 - Generate and use a simple function at runtime](https://solarianprogrammer.com/2018/01/10/writing-minimal-x86-64-jit-compiler-cpp/)
- [Part 2 - Call a C++ function from a function generated at runtime](https://solarianprogrammer.com/2018/01/12/writing-minimal-x86-64-jit-compiler-cpp-part-2/)

In this article, I will show you how to write a minimal, bare-bones, [x86-64](https://en.wikipedia.org/wiki/X86-64) [JIT compiler](https://en.wikipedia.org/wiki/Just-in-time_compilation) in C++ that runs on macOS, Linux and could potentially run on Windows through [WSL](https://blogs.msdn.microsoft.com/wsl/).

For our purposes, JIT compilation is a technique through which a program generates machine code at runtime, based on the user input. A C++ program is AOT (ahead of time) compiled, which typically means that once the original code was compiled for a particular machine it can’t be changed at runtime (and from a security point of view this is a desirable feature). A simple, useful application, of a C++ JIT compiler is on the fly compilation of a new function that is based on other functions already defined in the original code.

Let’s start with an even simpler example. Write a C++ program that asks the user for his name and generates, at runtime, a function that simply prints a greeting. While not a very practical program (you really don’t need to compile this to a separate function), this example will exemplify how to create and execute code at runtime.

Since machine code is inherently non portable between various processors, we need to chose a particular processor to run our example. For this article, we’ll use the x86-64 Intel processor as a our target machine. Even more restrictive, machine code that runs on an operating system is not portable between various operating systems. For example, if your machine runs both Windows and Linux, a piece of code compiled for Linux won’t run on Windows and vice-versa, not without some translation layer. As the target OS, we’ll use Linux, but the code should be trivial to port to macOS and I will show you how to do it. In theory, you should be able to follow along on Windows 10 if you use the Windows Subsystem for Linux (WSL).

You can find the complete source code for the next examples on the GitHub [repo](https://github.com/sol-prog/x86-64-minimal-JIT-compiler-Cpp) for this article.

All the programs from this article were checked with GCC 5.4 on Linux, Apple Clang 9.0.0 and GCC 7.2 on macOS. Example, on macOS:

```
1 clang++ -std=c++14 -stdlib=libc++ -Wall -pedantic hello_x.cpp -o hello_x
2 ./hello_x
```

on Linux:

```
1 g++ -std=c++14 -Wall -pedantic hello_x.cpp -o hello_x
2 ./hello_x
```

Note, if you want to use C++17, check my article about compiling [GCC 7.2 on macOS](https://solarianprogrammer.com/2017/05/21/compiling-gcc-macos/) or, if you prefer to use Clang, pass *-std=c++1z* to the compiler.

So, what we want is to generate machine code for the equivalent C++ code, particularly for the highlighted line, that uses the system [write](http://man7.org/linux/man-pages/man2/write.2.html) function to print a string:

```
 1 // hello_0.cpp
 2 #include <iostream>
 3 #include <string>
 4 
 5 int main() {
 6     // Get the user name
 7     std::string name;
 8     std::cout << "What is your name?\n";
 9     std::getline(std::cin, name);
10     std::string hello_name = "Hello, " + name + "!\n";
11 
12     // Greet the user
13     std::cout << hello_name;
14 }
```

To better exemplify what we need to do, let’s use the OS [write](http://man7.org/linux/man-pages/man2/write.2.html) function to print the greeting:

```
 1 // hello_1.cpp
 2 #include <iostream>
 3 #include <string>
 4 #include <unistd.h>
 5 
 6 int main() {
 7     // Get the user name
 8     std::string name;
 9     std::cout << "What is your name?\n";
10     std::getline(std::cin, name);
11     std::string hello_name = "Hello, " + name + "!\n";
12 
13     // Greet the user
14     write(STDIN_FILENO, (const void *) hello_name.c_str(), hello_name.size());
15 }
```

Basically, what we want to do is to generate at runtime a function *greeting()* that will replace line *14* from the above code:

```
1 int main() {
2 
3 	// ....
4 
5     // Greet the user
6     greeting();
7 }
```

Let’s write in x86-64 assembly the body of *greeting* from the above code:

```
 1 # Enforce Intel syntax
 2 .intel_syntax noprefix
 3 
 4 # Call the "write" system function (man 2 write)
 5 # ssize_t write(int fd, const void *buf, size_t count);
 6 mov rax, 1							# Store the "write" system call number 0x1 for Linux or 0x2000004 for macOS
 7 # Put the function arguments in the rdi, rsi, rdx, r10, r8, r9 registers
 8 mov rdi, 1							# Store where to write stdin which is 0x1 for Linux and macOS
 9 lea rsi, [rip + 0xa]				# Store the location of the string to write (0xa instructions from the current instruction pointer)
10 mov rdx, 17							# Store the length of the string
11 # Call the function
12 syscall
13 ret
14 .string "Hello, Your Name\n"
```

Try to assemble and disassemble. Assuming you saved the above code in a file named *chunk.s*, this is how you can see the generated code on Linux:

```
1 as chunk.s -o chunk.o
2 objdump -M intel -D chunk.o
```

On macOS, the syntax for *objdump* is a bit different:

```
1 as chunk.s -o chunk.o
2 objdump -x86-asm-syntax=intel -D chunk.o
```

This is what I see on my Linux machine (I kept only the disassembled machine code for brevity):

```
 1    0:	48 c7 c0 01 00 00 00 	mov    rax,0x1
 2    7:	48 c7 c7 01 00 00 00 	mov    rdi,0x1
 3    e:	48 8d 35 0a 00 00 00 	lea    rsi,[rip+0xa]        # 0x1f
 4   15:	48 c7 c2 11 00 00 00 	mov    rdx,0x11
 5   1c:	0f 05                	syscall
 6   1e:	c3                   	ret
 7   1f:	48                   	rex.W
 8   20:	65 6c                	gs ins BYTE PTR es:[rdi],dx
 9   22:	6c                   	ins    BYTE PTR es:[rdi],dx
10   23:	6f                   	outs   dx,DWORD PTR ds:[rsi]
11   24:	2c 20                	sub    al,0x20
12   26:	59                   	pop    rcx
13   27:	6f                   	outs   dx,DWORD PTR ds:[rsi]
14   28:	75 72                	jne    0x9c
15   2a:	20 4e 61             	and    BYTE PTR [rsi+0x61],cl
16   2d:	6d                   	ins    DWORD PTR es:[rdi],dx
17   2e:	65 0a 00             	or     al,BYTE PTR gs:[rax]
```

Please note the *0x1f* from the first highlighted line, this indicates where you point when you go *rip+0xa* from the current instruction.

Let’s store the above machine code in a vector of eight bits unsigned integers. For brevity, I will show only the code that is relevant for each step. You can find the complete code, as mentioned, on the GitHub repository for this [article](https://github.com/sol-prog/x86-64-minimal-JIT-compiler-Cpp).

First line, line *0:*, from the above code can be stored as (please note the different system call numbers stored in *rax* based on the OS):

```
1     std::vector<uint8_t> machine_code {
2         #ifdef __linux__
3         0x48, 0xc7, 0xc0, 0x01, 0x00, 0x00, 0x00,           //Store the "write" system call number 0x01 for Linux
4         #elif __APPLE__
5         0x48, 0xc7, 0xc0, 0x04, 0x00, 0x00, 0x02,           //Store the "write" system call number 0x02000004 for macOS
6         #endif
7 
8     //  .....
```

The remaining code is the same for both systems:

```
 1     // Store the machine code in memory
 2     std::vector<uint8_t> machine_code {
 3         #ifdef __linux__
 4         0x48, 0xc7, 0xc0, 0x01, 0x00, 0x00, 0x00,           //Store the "write" system call number 0x01 for Linux
 5         #elif __APPLE__
 6         0x48, 0xc7, 0xc0, 0x04, 0x00, 0x00, 0x02,           //Store the "write" system call number 0x02000004 for macOS
 7         #endif
 8         0x48, 0xc7, 0xc7, 0x01, 0x00, 0x00, 0x00,           //Store stdin file descriptor 0x01
 9         0x48, 0x8d, 0x35, 0x0a, 0x00, 0x00, 0x00,           //Store the location of the string to write (3 instructions from the current instruction pointer)
10         0x48, 0xc7, 0xc2, 0x00, 0x00, 0x00, 0x00,           //Store the length of the string (initially zero)
11         0x0f, 0x05,                                         //Execute the system call
12         0xc3                                                //return instruction
13     };
```

Next, we need to store the message size from index *24* to *27* in the machine code vector:

```
1     // Add the message size
2     size_t message_size = hello_name.size();
3     machine_code[24] = (message_size & 0xFF) >> 0;
4     machine_code[25] = (message_size & 0xFF00) >> 8;
5     machine_code[26] = (message_size & 0xFF0000) >> 16;
6     machine_code[27] = (message_size & 0xFF000000) >> 24;
```

We can abstract the above piece of code in a separate function:

```
1 // Add the message size
2 void append_message_size(std::vector<uint8_t> &machine_code, const std::string &hello_name) {
3     size_t message_size = hello_name.length();
4 
5     machine_code[24] = (message_size & 0xFF) >> 0;
6     machine_code[25] = (message_size & 0xFF00) >> 8;
7     machine_code[26] = (message_size & 0xFF0000) >> 16;
8     machine_code[27] = (message_size & 0xFF000000) >> 24;
9 }
```

Now, the message body can be appended to the end of the vector:

```
1     // Append the message to the machine code vector:
2     for(auto c : hello_name) {
3         machine_code.emplace_back(c);
4     }
```

At this point, we are done with the code generation part for our toy example. All we have to do now, is to transfer the *code* to an executable memory region and call it. You can find the complete code of the last example in [hello_2.cpp](https://github.com/sol-prog/x86-64-minimal-JIT-compiler-Cpp) on the GitHub repository for this article.

On Linux and macOS, you allocate memory pages with [mmap](http://man7.org/linux/man-pages/man2/mmap.2.html) as a multiple of:

```
1 	sysconf(_SC_PAGE_SIZE);
```

Here is an utility function that will give you an estimate of the required memory for a given generated machine code:

```
 1 // hello_3.cpp
 2 #include <iostream>
 3 #include <string>
 4 #include <vector>
 5 #include <unistd.h>
 6 #include <sys/mman.h>
 7 
 8 // ....
 9 
10 int main() {
11 	// Get the user name
12 	//  ....
13 
14 	// Store the machine code in memory
15 	//  ...
16 
17     // Get the required memory size for mmap
18     size_t required_memory_size = estimate_memory_size(machine_code.size());
19     // ...
20 }
21 
22 // Return a multiple of the machine page size that can store the generated machine code
23 size_t estimate_memory_size(size_t machine_code_size) {
24     size_t page_size_multiple = sysconf(_SC_PAGE_SIZE);     // Get the machine page size
25     size_t factor = 1, required_memory_size;
26 
27     for(;;) {
28         required_memory_size = factor * page_size_multiple;
29         if(machine_code_size <= required_memory_size) break;
30         factor++;
31     }
32     return required_memory_size;
33 }
34 
35 //   ...
```

Next, we allocate the required memory size and transfer the generated machine code to the executable memory:

```
 1 // ....
 2 
 3 int main() {
 4 
 5 	//  ...
 6 
 7     // Get the required memory size for mmap
 8     size_t required_memory_size = estimate_memory_size(machine_code.size());
 9 
10     uint8_t *mem = (uint8_t*) mmap(NULL, required_memory_size, PROT_READ | PROT_WRITE | PROT_EXEC, MAP_PRIVATE | MAP_ANONYMOUS ,-1, 0);
11     if(mem == MAP_FAILED) {
12         std::cerr << "Can't allocate memory\n"; std::exit(1);
13     }
14 
15     // Copy the generated machine code to the executable memory
16 	for(size_t i = 0; i < machine_code.size(); ++i) {
17 	    mem[i] = machine_code[i];
18 	}
19 }
20 
21 // ....
```

All we have to do at this point, is to get a pointer to the beginning of our executable code and cast it to a function pointer, after which we can use the generated function:

```
 1 // hello_3.cpp
 2 
 3 // ....
 4 
 5 int main() {
 6 
 7 	//  ...
 8     // Copy the generated machine code to the executable memory
 9     //  ...
10 
11     void (*func)();
12     // Cast the address of our generated code to a function pointer and call the function
13     func = (void (*)()) mem;
14     func();
15 
16     // Release the mapped memory
17     munmap(mem, required_memory_size);
18 }
19 
20 // ....
```

You can find the complete code for the last example on GitHub in file [hello_3.cpp](https://github.com/sol-prog/x86-64-minimal-JIT-compiler-Cpp) . Here is the result of running *hello_3.cpp* on a macOS machine:

```
 1 $ clang++ -std=c++14 -stdlib=libc++ -Wall -pedantic hello_3.cpp -o hello_3
 2 $ ./hello_3
 3 What is your name?
 4 Solarian Programmer
 5 
 6 Machine code generated:
 7 48 c7 c0 4 0 0 2
 8 48 c7 c7 1 0 0 0
 9 48 8d 35 a 0 0 0
10 48 c7 c2 1c 0 0 0
11 f 5 c3 48 65 6c 6c
12 6f 2c 20 53 6f 6c 61
13 72 69 61 6e 20 50 72
14 6f 67 72 61 6d 6d 65
15 72 21 a
16 
17 Hello, Solarian Programmer!
18 $
```



这篇文章将讲解如何写一个即时编译器