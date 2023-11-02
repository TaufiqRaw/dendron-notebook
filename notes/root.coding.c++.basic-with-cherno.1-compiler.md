---
id: mic9abtyc19wecdnm7ab0kw
title: 1 - Compiler
desc: ''
updated: 1698026442698
created: 1698021783120
---
`#define` Used to define a macro
> `#define INTEGER int` will replace **every** INTEGER in your code to be int 

`#undef`
Used to undefine a macro

`#include`
Used to include a file in the source code program, literally copy and paste it  

>THIS IS JUST EXAMPLE, DONT USE IT IRL

><span style="color:#2D68C4">main.cpp</span>  
>`int main(){`  
>`return 0`  
>`#include "curly.h"`

><span style="color:#2D68C4">curly.h</span>  
>`}`

><span style="color:#2D68C4">curly.i (the output preprocessor file from compiler, enabled in project > properties > c/c++ > preprocessor > preprocess to file) </span>  
>`int main(){`  
>`return 0`  
>`}`

`#ifdef`
Used to include a section of code if a certain macro is defined by `#define`

`#ifndef`
Used to include a section of code if a certain macro is not defined by `#define`

`#if`
Check for the specified condition

`#else`
Alternate code that executes when `#if` fails

`#endif`
Used to mark the end of `#if`, `#ifdef`, and `#ifndef`

## Definition vs Declaration

<div style="background-color:#aaaaaa12; padding:10px">

<center>
  <b>Declaration can appear multiple times in a program, but a definition must only appear once.</b>
</center>

</div>

### Declaration
A declaration is an **announcement to the compiler** that a certain entity (variable, function, or class) will exist in the program. it **does not allocate memory** or provide the actual implementation, compiler trust you that the definition exist.

>`extern int x;`

>`int add(int a, int b); `

### Definition
A definition is the actual implementation or storage for an entity. It allocates memory for the entity and provides the actual implementation.

>`int x;`

>```
>int add(int a, int b) {
  return a + b; 
}
>```


## Translation Unit
a translation unit is the result of the preprocessor handling a single source file, along with all the headers and source files that are included (either directly or indirectly) into that source file.

One .cpp file correspond to one .o *(object)* file then the linker will link them.

.o file contains binary, you can set compiler to output assembly instead in project > properties > c/c++ > output files > assembler output > Assembly-only listing