---
id: o6hojxpor07kue2uk44133y
title: 2 - Linker
desc: ''
updated: 1698025840666
created: 1698024346446
---
In C++, a linker **takes one or more object files** and combines them into a single executable file. During this process, it **resolves symbols**, such as function and variable names, to memory addresses and handles references to libraries.

Even if u don’t have multiple files linker still needs to link main function. It needs to know the starting point of your program. Cherno gives example by creating a file that didn’t have the main function, this file compiled successfully however when linking it generated a linking error saying entry point not found.

## Static

you can add `static` keyword infront of function definition to ensure to the linker that the definition will **only used in this file**

>```
>static int Multiply(int a, int b){
  Log("Multiply");
  return a * b;
}
>```

in header file, if you include the header in multiple file, and if there is definition in it you will get `already defined` error (remember **`#include`** directive is just copy pasting code), you can use :
* **Inline**  
mean the compiler will inline the function if it find the function call
  ><span style="color:#2D68C4">main.cpp</span>  
  >```
  >#include "log.h"
  >int main(){
  >   log("hi ninjas")
  >}
  >```

  ><span style="color:#2D68C4">log.h</span>  
  >```
  >inline void log(const char* message){
  >  std::cout << message << std::endl;
  >}
  >```

  ><span style="color:#2D68C4">log.i</span>  
  >```
  >#include "log.h"
  >int main(){
  >   std::cout << "hi ninjas" << std::endl;
  >}
  >```
* **Static**  
  ><span style="color:#2D68C4">log.h</span>  
  >```
  >static void log(const char* message){
  >  std::cout << message << std::endl;
  >}
  >```

  which mean each file that include the header will have their own version of log (a lot of wasted spaces)
* **Just the definition (best option)**
  ><span style="color:#2D68C4">log.h</span>  
  >```
  >void log(const char* message)
  >```

  ><span style="color:#2D68C4">log.cpp</span>  
  >```
  >#include "log.h"
  >void log(const char* message){
  >  std::cout << message << std::endl;
  >}
  >```