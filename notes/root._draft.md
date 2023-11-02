---
id: 50qv0q95ftb18ufoqn2r701
title: _draft
desc: ''
updated: 1698071639187
created: 1698050312006
---

todo : figure out what will get copied if we return by value, should we use reference for the variable that hold return value?

findout what these buzzword means :
RAII, meta-programming, expressiveness, type-safety, const-correctness, zero-cost abstraction, both oop and functional paradigm capability (don’t talk about CObject please, I still have nightmares several years after), macro avoidance, constexpr operations, concurrency, 

when defining a variable, the main consideration is its lifetime and its scope.

primitives tipes, how char can saved to number tipes and vice versa, boolean stored to a byte instead of bit

C++ takes default floating point value as double.
Double is 32bit in size whereas float is 16bit in size.
specifying ‘f’ tells the control to restrict data to 16bits.

function is costly, it create stack frame, pushing return address, etc


When using a symbol and the definition in other file, you need a declaration, e.g main.cpp and test.cpp use log(string) that defined in log.cpp, main and test can write declare on itself (you have two declaration) or it can use declaration from .h (header) file

using header file, we can declare log in that file and define it in log.cpp. then the main.cpp and test.cpp can #include it (it will copy paste it)

#pragma once in header file mean it will only included once in one translation unit, its important because include can have chain of include, example, common.h include log.h, log.h have definition of a struct (you cant define something twice!) so if you in main.cpp and #include "log.h" and #include "common.h" it duplicate log.h twice!. pragma once guard that shit to never happen, pragma once can also be written as
#ifndef _LOG_H
#define _LOG_H

struct Player {}

#endif

pragma once is just newer and simpler way to write them

#include "" or <>
"" is used to include file that relative to current file e.g "../log.h"

<> is used if the file is in include directory

<iostream> iostream is just normal header file, like log.h, the difference is that its part of c++ std library, the c++ std files doesnt have extension, while c std library have .h extension 


program is made out of memory, our instruction pointer (where line we execute now), and actual code is stored in memory

## pointers
in c++ you need to read a pointer declaration backwards.
int * const * a;
This one would essentially mean that you need to dereference a once to get const pointer to int.

if statement can also check if a pointer is null
if(pointer == nullptr) is same as if(pointer)

pointer for all types is just an integer that store a memory address,
so types is just semantic for us to make our life easier, and it helps pointer when manipulate that memory (read & write) e.g. int is 4 byte

you can write
void* ptr = 0; same as void* ptr = NULL; same as void* ptr = nullptr;

you can initiate array
int* arr = new int[8];
delete[] arr;

arr[2] //the [] operation are also dereference operator, its just dereferencing arr with offset of 8 bytes;

Delete doesn't delete anything, it just marks the memory as "being free for reuse", the compiler's not going to take the extra effort to wipe it back to 0 everytime something's deleted (this applies to stack allocated memory too), you still can access them, sometime the old data still exist, sometime it has changed, thats why accessing deleted variable will give you Undefined Behaviour.
note : Using certain compilers, the memory may be zeroed, mvs compiler doesnt do this for stack deletion, but it does when debugging heap delete.

when deleting a pointer's pointed value, its best to change the pointer to 0 afterwards (not required if you crave performance). 

## references
A reference variable provides a new name to an existing variable. It is **dereferenced implicitly** and does not need the dereferencing operator * to retrieve the value referenced. On the other hand, a pointer variable stores an address. You can change the address value stored in a pointer.

ref can create alias e.g
int a = 8;
int& ref = a;
ref++;
the compiler will not create ref variable, it will subtitute ref with a

ref can used to pass reference to function

void increment(int a){
  a++;
}

void increment_p(int* a){
  (*a)++;
}

void increment_r(int& a){
  a++;
}

int main(){
  int a = 5;

  increment(a)
  std::cout<< a << std::endl; // this will print 5

  increment_p(&a)
  std::cout<< a << std::endl; // must pass address, this will print 6

  increment_r(a)
  std::cout<< a << std::endl; // this will print 7
}

ref cant change what it referenced after it declared unlike pointer


## static in c++
when used outside class, the static mean that the symbol will only available on that translation unit, this allow

(main.cpp)
int test(){
  return 0;
}

int main(){
  return test(); //will return 0
}

(test.cpp)
static int test(){  //eventhough symbol is same with one in main, this one is static
  return 2;         //means it only available in test.cpp file
}

### when used inside class
struct Entity{
  static int x,y;

  static void print(){
    std::cout << x << ", " << y << endl; //static method can only access static variable
  }
}

int Entity::x; // static variable have to defined
int Entity::y;

int main(){
  Entity e;
  e.x = 2; //could work, but not the right way

  Entity::x = 2; //the right way
  Entity::y = 5;

  Entity::print();
}


when compiled, **non static** method look like
static void Print(Entity e){
  std::cout << e.x << ", " << e.y << endl;
}

while static looks like
static void Print(){
  std::cout << Entity::x << ", " << Entity::y << endl; 
    //this is why static can only access static variable
}

### local static

1. Static variables can persist beyond the initial call and hence static keyword can be used to extend the lifetime of a variable.
2. In simple cases, it is very similar to having a global variable which cannot be accessed beyond the local scope.

so if you want global variable behaviour, but localize the scope to a function, use static.

this is not really recommended, but you can do it to make your code cleaner (e.g. singleton instance)

int* get_static_array()
{
    static int result[50];
    return result;
}

every time this function called, same array always used:

int* a = get_static_array();
int* bb = get_static_array();
a[0] = 5;
bb[1] = 10;
std::cout << a[0] << "\n"; // gives you 5
std::cout << a[1] << "\n"; // gives you 10

#### usecase : singleton
#include <iostream>

class Singleton{
private:
    int state;
    
    Singleton()
        : state(0){}
public:
        
    static Singleton& get(){
        static Singleton instance;
        return instance;
    }
    
    void incrementState(){
        state++;
    }
    
    int getState() const {
        return state;
    }
    
};

void funcIncrementState(){
    Singleton::get().incrementState();
}

int main(){
    std::cout << Singleton::get().getState() << std::endl; // 0
    funcIncrementState();
    std::cout << Singleton::get().getState() << std::endl; // 1
}

## debugging in visual code
memory in visual code debugger is written in reverse, coz of endianness, so if you want to use pointer's address in visual code debugger, write them in reverse order

## enum
a way to give name to certain value, so you dont have to deal with integer in some place

enum by default is 32 bit integer
we can change that to any integer

enum Example : unsigned char{
 A = 5, B, C
}

default start of enum is 0

enum is just compiler enforced restriction for readable and semantics

you can also compare enums just like integer (they are integer in the first place)

when enum in class it elements parent become class
e.g

class Log{
  public : 
    enum Level{
      LevelError = 0, LevelWarning, LevelInfo
    }
}

main(){
  std::cout << Log::LevelError << std::endl; //notice its not Log::Level::LevelError
}


## constructor
if you dont provide constructor, c++ will give you default constructor, which looks like
class A{
  A(){}
}

int main(){
  A a; //this automatically call constructor
}

in c++ primitive types doesnt get automaticaly initialized!
c++ can overload constructor
c++ can also make constructor private

you can delete the default constructor by using A() = delete;
class A{
  A() = delete;
}

int main(){
  A a; //this will produce error now
}

there is copy constructor and move constructor;

## destructor
class A{
  A(){
    std::cout << "created" << std::endl;
  }
  ~A(){
    std::cout << "destroyed" << std::endl;
  }
}

void func(){
  A a; //A() called
} // ~A() called, because 'a' created on the stack (automatically destroyed when out of scope);

int main(){
  func();
}

you can call destructor manually `a.~A()`, but it is really not recomended, maybe when you use free(), maybe?


class size in memory is all of its member variable, and if there is a v-table
class A{
  public:
    int a; //4 byte
    char b; // 1 byte
}

sizeof(A) will output 5


pure virtual function (abstract function)
class Printable{
  public:
    virtual std::string getClassName() = 0; // the = 0 makes the function **pure** virtual
}

class A : public Printable{
  public
    std::string getClassName() override { //the override keyword is optional
      return "A"                          // but it ensure that the signature
    }                                     // match the virtual function
}

## inheritance
class A 
{
    public:
       int x;
    protected:
       int y;
    private:
       int z;
};

class B : public A
{
    // x is public
    // y is protected
    // z is not accessible from B
};

class C : protected A
{
    // x is protected
    // y is protected
    // z is not accessible from C
};

class D : private A    // 'private' is default for classes
{
    // x is private
    // y is private
    // z is not accessible from D
};


## array
when you write out of bound array, you might changed some other part of your variable or code, this is called Undefined Behaviour, so make sure you not write out of bound array.

<= comparison is slower than just <

array are contiguosly

array are just pointer
int example[5]; //example is just pointer
int* ptr = example;
//meaning example == ptr

example[2] = 5; //we write to the offset of 8 byte (int are 4 byte)
*(ptr + 2) = 5; //can also written like this
*(int*)((char*)ptr+8) = 5; //can also written like this

int example[5]; // created on stack (destroyed after out of scope)
int* example2 = new int[5]; // created on heap (alive until we use delete, or end off program)
delete[] example2;

new keyword create memory indirection, results in fragmentations, cache misses, etc
indirection will hit your performance, imagine cpu must first get address from the pointer's value, then it need to jump to actual memory that being pointed to. thats why whenever posible make array on stack.

int a[5];
sizeof(a) //20
sizeof(int) //4
sizeof(a) / sizeof(int) //5 which is array length

when you allocate array at stack it must be a compile-time know constant

std::array provide bounds checking and size() function
std::array<int, 5> a;

raw array are abit faster than std::array (very little speed advantage, but insanely dangerous)

when debug mode, every array defined has array guard as 'cc' hex in memory


## string
in c++ all string literals ("test string" (commonly called as c-string-literal  )) are const char* \, so its imutable

naturally you assign the literal to const char* variable if you omit the const in variable, it will produce undefined behaviour. You must not change any value of the C-String-Literal.
this is because string literal are stored in read only section in memory

you can create mutable char from literal char
char name[] = "Taufik";
name[5] = 'q'

this is because when using [] the compiler create name variable in stack memory, the c-string-literal still created in read-only memory but it get copied to the name variable in stack so you can mutate the shit.

const char* know its length by using null terminating character (0) or '\0'
"Taufiq" == {'T', 'a', 'u', 'f', 'i', 'q', 0}
if you dont add null termination the std::cout doesnt know when to end printing, it will continue until it found 0
const char name[6] = {'T', 'a', 'u', 'f', 'i', 'q'} //bad, need null termination
const char name[7] = {'T', 'a', 'u', '\0', 'f', 'i', 'q'} //bad, this will break the behaviour of this string
const char name[7] = {'T', 'a', 'u', 'f', 'i', 'q', '\0'} //good
const char name[7] = "Taufiq" //good, why the length 7?, coz the implicit \0 in string literal 

const char* doesnt mean its heap allocated, so no need to call delete (rule of thumb is if you dont use new u dont use delete)

std::string in c++ are template specialitation of basic string class with char as template parameter (so char is underlying datatype for std::string)

std::string name = "taufiq"
bool contains = name.find("iq") != std::string::npos; //npos is illegal position

to add two string
const char* name = "taufiq" + " ridho" //error, cant add const char* to const char*
you can use 
const char* name = std::string("taufiq") + " ridho"

string copying is quite slow, pass by const reference
void printString(const std::string& str)

## string types
const char* name = u8"Taufiq"; //the normal 8bit per character string (utf8) 1 byte
const wchar_t* name2 = L"Taufiq" //depend on compiler, on windows it 2 byte on linux its 4 byte
const char16_t* name3 = u"Taufiq" //16bit per char (utf16) 2 byte
const char32_t* name4 = U"Taufiq" //32bit per char (utf32) 4 byte

## const
in c++ const doesnt change generated code much, but it does enforce rules developer

its a promise that you will never change its value
like real life you can also break the promise (but dont do it)

### const in variable
const int MAX_AGE = 90

(forbiden tech) breaking const promise
int* a = new int;
*a = 2;
a = (int*)&MAX_AGE // in some compiler this will work, on other it crashes

### const in pointer
const int* a = new int; //you cant change the **content** of the pointer
int const* a2 = new int; // same as above

int* const a3 = new int; // you cant reasign the pointer to other address

const int* const a4 = new int; //self explanatory

### const in function
int square(const int num){ // you cant mutate the num variable
  return num * num;
}

### const in method(member function)
int getX() const // you cant change the member variable inside this function

const int* const getPointer() const //self explanatory

this is usefull when you have a function that takes const as a parameter, inside that function you can only call const method
int printEntity(const Entity& e)

const method also can called when its object are declared as a const variable

you can also overload method with const, one have const and one doesnt, when the overload method called inside function that takes const as a parameter, the const method will get called instead

### mutable
mutable allow a variable to mutated inside a const method

its also useful with lambda

## member initializer list
Class Entity{
private : 
  int x,y;
  std::string name;
public : 
  Entity()
    : x(0), y(0), name("John Doe"){}

  Entity(const std::string& _name)
    : x(0), y(0), name(_name){}
}

thing to note :
member init must initialize in same order as how members are defined, because the class will initialized in the order of the actual class member are defined.

why use this instead just initialize in constructor body?
its not just for style!
because when init in constructor body, you actually initialize the member using its default constructor first then you create a new object then reassign that member var (you actually create two object!) and this is bad for performance.


## instantiating object
in c++ **every** object must occupy some place in memory even if the object is empty.

this is fastest way for c++ to instantiate an object :
Entity entity; //default constructor is called, implicitly
Entity entity2 = Entity(); //same as above
Entity entity3("Taufiq"); //call the overload with param

there is a reason you cant use instantiation above:
* stack memory is quite small (usually 1mb).
* you want to explicitly control the lifetime of an object.

the solution is to use heap allocation:
Entity* entity = new Entity("Taufiq");

heap allocation is closer to java or c# way, in other language you cant instantiate object in stack frame.

in c++, if you can, you shouldn't using new, because :
* performance
* you need to manually free the memory

scope in c++ is not only just function scope, it can be block scope {} also, and other stuff.

## new
when using new, you specify the data types (datatype* var = new datatype), whether its primitives, class, or array. that type determine the size of allocation in bytes, then it ask os to find a contiguous free block of memory of that size. thats why its kinda slow (even though the os have free list which maintain the address of free memory)

new on class (Entity* entity = new Entity(), or the new Entity[length] counterparts) does two things:
* it allocate the entity
* it calls the constructor

new is just an operator just like +, -, etc. and you can override it.

new allocate memory by calling malloc(), you could say
Entity* e = new Entity();
Entity* e = (Entity*)malloc(sizeof(Entity));
the only difference is that the new **calls** the constructor, so use new instead.

when using new always call delete, or delete[] depending on the how new called.

delete calls the free() c-function but delete also : 
* calls the destructor
* marks the memory as released

new also support manual memory allocation, its called placement new:
char* a = new char[12]; //allocate 12 bytes
Entity* e = new(a) Entity(); //allocate Entity in a's address 
delete e;
delete[] a;

## Implicit construction & conversion
c++ only allow 1 step implicit conversion

you can have implicit construction
class Entity{
private :
  std::string name;
  int age;

public :
  Entity(std:string& _name)
    : name(_name), age(-1){}

  Entity(int _age)
    : name("unknown"), age(_age){}
}

this allows :
int main(){
  Entity e = 22 // the constructor that ask _age called instead
  Entity e2 = std::string("Taufiq")
}

also allows :
void printEntity(Entity* e)

you can call the function with:
printEntity(22);

but not:
printEntity("Taufiq");
this because string literal are types of const char*, so the program must convert const char* -> std::string -> entity, c++ only allow one step implicit conversion.

so you need to convert it first :
printEntity(std::string("Taufiq"));

the explicit keyword disallow implicit constructor calls
...
public :
  Entity(std:string& _name) explicit
    : name(_name), age(-1){}

  Entity(int _age) explicit
    : name("unknown"), age(_age){}
...

this will prohibit : 
entity e = 22; //error
printEntity(22) //error
printEntity(Entity(22)) //allowed

## overloading operator
### as method
class Vector2{
private :
  float x,y;
public :
  Vector2(float x, float y)
    : x(x), y(y){}
  
  Vector2 add(const Vector2& other) const {
    return Vector2(this.x + other.x, this.y + other.y);
  }

  Vector2 operator+(const Vector2& other) const {
    return add(other);
  }
}

### as function
std::ostream& operator<<(std::ostream& stream, const Vector2& other){
  stream << other.x << ", " << other.y;
  return stream;
}

## this keyword
class Entity{
public :
  int x,y

  Entity(){
    //here, this keyword is same as :
    Entity* const e = this;
  }

  getX() const {
    //here, this keyword is same as :
    const Entity* const e = this;
  }
}

now if you have non member function (non-method function) you can use this to pass object to this function from inside itself
void printEntity(const Entity& e)

you can use :
...
  Entity(){
    printEntity(*this);
  }
...

you can do some bizzare stuff like :
delete this;
but do not do this!.

## Lifetime
this is bad function:
int* CreateArray(){
  int arr[50] //allocate array in stack
  return arr; // return the pointer
} // array allocated in heap is destroyed
// pointer returned by function is now invalid ! (dangling pointer)

### scoped pointer
more complex version in standard lib called unique pointer

class ScopedPtr{
private :
  Entity* e;

public :
  ScopedPtr(Entity* ptr)
    : e(ptr){}

  ~ScopedPtr(){
    delete e;
  }
}

int main(){
  ScopedPtr e = new Entity(); // entity allocated in heap
} // entity deleted because scoped pointer out of scoped

this type of pointer also good for timer, mutex, etc.

## Unique, shared, and weak pointer
### Unique
a scoped pointer, have to be unique, you cant copy unique pointer
std::unique_ptr<Entity> entity(new Entity()); //unique pointer constructor are explicit
std::unique_ptr<Entity> entity = std::make_unique<Entity>(); //same as above, but this has exception safety

### Shared
pointer that has a counter that tracks how many pointer refer to it, the object will be deleted if the counter reach 0
std::shared_ptr<Entity> entity = std::make_shared<Entity>() //use this function instead of new, so the program doesnt have to do two allocation, it allocate Entity with the shared pointer control block (that store its reference count) at same time.

{
  std::shared_ptr<Entity> e0;
  {
    std::shared_ptr<Entity> e1 = std::make_shared<Entity>(); // create shared pointer, rc is now 1
    e0 = e1; //rc is now 2
  } //rc is now 1
} //rc is now 0, destroying the entity.

### Weak Pointer
point to shared pointer but doesnt increase the shared pointer reference counter (weak pointer doesnt own the memory)

here are expert's advice :
1. NEVER use new or delete. It has to be very clear wether a pointer owns the object or doesn't. If it owns the object (for example, it is creating the object), use a smart pointer and make_unique or make_shared.

2. By default, use unique_ptr because it has almost no overhead. If you know it will need to have several owners, use shared_ptr.

3. When the pointer will not own the object, use raw pointers. For example, when passing an object to a function, the pointer that receives it will not own the object (it will still be alive once the function returns), so don't pass the smart pointer, pass a raw pointer.
To pass a unique_ptr as a raw pointer to a function, the best way is to dereference it and pass it by reference. So the function is just for example "void foo(const Class& myObject)", and you call it with "foo(*myPointer)".
Another way would be to pass the adress itself, with the method "unique_ptr.get()".

4. Again, never use delete on a raw pointer and assume it does not own the object.

5. When you create an object inside a function and want to return it, do it as a unique_ptr. The receiver will be able to do whatever they want with it. Keep in mind that now, passing a local object from a function by copy will not actually copy it, but move it as an r-value. So it will move into the new unique_ptr, without a compiler error. Again, the receiver can do whatever they want, so they can move it into a shared_ptr or even a raw pointer.

6. When you want to transfer ownership of a unique_ptr, you can do it as a r-value using std::move(). This is basically what I just explained that C++ does when returning from a function. You can do it to transfer the ownership to a function.

## copy constructor
everytime* you using asignment operator, you are copying

struct Vector2{
  float x,y;
}

int main(){
  Vector2 v1 = {2f, 3.2f};
  Vector2 v2 = v1 //you **copy** v2 to v1;
  v2.x = 4f;

  print(v1.x); // 2.0
  print(v2.x); // 4.0

  *Vector2 v3 = new Vector2{2f, 3.2f};
  *Vector2 v4 = v3; // you **copy** the pointed address of v3 (which points to vector2{2f, 3.2f}) to v4
  v4.x = 4f;

  print(v3.x); //4.0
  print(v4.x); //4.0

}

*everytime except reference, you cant reassign reference, you just copy the underlying thing of that ref to underlying thing of other ref. 

note : if you assign a normal var to reference, you copy the underlying thing of that ref to the var.

### diy string class
#include <iostream>
#include <cstring>

class String{
private :
    char* buffer;
    int size;
public : 
    String(const char* str){
        size = strlen(str);
        
        std::cout << (void*)buffer <<std::endl; // this buffer point to address that can hold a byte
        buffer = new char[size+1]; //without this you cant use delete[], and cause an UB if memcpy the const char
        std::cout << (void*)buffer <<std::endl; // now buffer point to address that can hold [size + 1] byte

        //you can remove the double buffer address allocation by allocate new char[size+1] in initializer list instead
        
        memcpy(buffer, str, size+1);
        buffer[size] = 0; // add null terminator
    }
    
    ~String(){
        delete[] buffer;
    }
    
    char& operator[](int i) const {
        return buffer[i];
    }
    
    friend std::ostream& operator<<(std::ostream& stream, const String& other);
};

std::ostream& operator<<(std::ostream& stream, const String& other){
    stream << other.buffer;
    return stream;
}

int main(){
    String test("bababoey");
    String b = test;
    b[1] = 'u';
    std::cout << test << std::endl; // result = bubaboey
} //will throw error, double free detected, this because you shallow copy the char* buffer

### copy constructor in action
by default c++ provide copy constructor like

String(const String& other){
  memcpy(this, &other, sizeof(other));
}
or same as
String(const String& other)
  : buffer(other.buffer), size(other.size){}

that means the copy constructor just do shallow copy, meaning if there is pointer, it just copy the pointer, not allocating new underlying pointer data and copy it.

copy constructor takes const ref of its type

the solution is to define your own copy constructor
String(const String& other)
  : size(other.size){ 
  buffer = new char[size+1];
  memcpy(buffer, other.buffer, size+1);
  // no need to add null terminator, because we know our String (the parameter of copy constructor) always have null termination 
}

//now if you want to pass string to function use const reference instead, 
so it will not copy, const on parameter also mean we cant pass temporary r value to the function

## Arrow operator

int main(){
  Entity e;
  Entity* ep = &e;

  ep.print() 

  // error,because ep is pointer, to be able to use dot operator do:
  Entity& er = *ep;
  er.print();

  //or

  (*ep).print(); 

  //or

  ep->print();
}

so arrow operator is just a shortcut for derefer our pointer then dot operator.
you can also overload them

### getting offset of member variable memory
struct Vector3{
  float x,y,z;
};

int main(){
  int offsetX = (int) &((Vector3*)0)->x; // arrow operator is evaluated before & operator
  int offsetY = (int) &((Vector3*)0)->y; // so we get the address of the member variable
  int offsetZ = (int) &((Vector3*)0)->z;

  std::cout << offsetX << std::endl; //0
  std::cout << offsetY << std::endl; //4
  std::cout << offsetZ << std::endl; //8
}

0 can also changed to nullptr

(int) &((Vector3*)0)->x

C++ treats addresses as pointers, then this line of code means:
- Cast 0 into an valid Vector3 address (pointer).
- Returns x's address (remember the Vector3 address is 0, so x's address (the first member of Vector3) should be also 0).
- Cast this address into int.

this example is useful when we serializing data into stream of bytes.

### overloading usecase : smart pointer

from cpp reference says:
"The overload of operator -> must either return a raw pointer, or return an object (by reference or by value) for which operator -> is in turn overloaded."

In English, cpp will recursively call the arrow operator until it gets back a raw pointer, which it will then deref for you (or at least that is my understanding)

#include <iostream>

class Entity{
    public :
    void print(){
        std::cout << "bababoey" << std::endl;
    }
};

class ScopedPtr{
private :
    Entity* data;
public :
    ScopedPtr(Entity* data)
        : data(data){}
    
    ~ScopedPtr(){
        delete data;
    }
    
    Entity* operator->(){
        return data;
    }

    const Entity* operator->() const {
        return data;
    }
};

int main(){
    ScopedPtr e = new Entity();
    e->print();
}

now when we use arrow operator on ScopedPtr its dereference its data instead of itself, and we dont need to do e.getData()->print();

#### dumb case (must not do in realworld)
if the ScopePtr is a pointer itself (which is dumb, dont do this, its only for research purpose) "ScopedPtr* e = new ScopedPtr(new Entity);", we need to do (*e)->print(); 

## Vector (dynamic array)
* storing object inline :
  std::vector<Vertex> vertices;
* or storing pointer :
  std::vector<Vertex*> vertices;

storing inline is fastest, it gives you contiguous allocation in stack memory so its more optimal when you iterate them, change all of em or wathever, coz they are in same cache line, but will be trouble when the vector needs copying (when its capacity runs out) (the solution is moving instead of copying it)

### looping
for(const Vertex& v : vertices)
  std::cout << v << std::endl;

always pass them by reference, even in loop.

### optimizing
when optimizing its best to knowing your environment : how do things work, knowing whats going on, what do i need to do and what should happen.

#include <iostream>
#include <vector>

class Vertex{
public : 
  float x,y,z;
  Vertex(float x, float y, float z)
      : x(x), y(y), z(z){}
  Vertex(const Vertex& other)
      : x(other.x), y(other.y), z(other.z){
      std::cout<<"copied"<<std::endl;
  }
};

int main() {
  std::vector<Vertex> v;
  v.push_back({2,4,5});
  v.push_back({2,5,3});
  return 0;
}

this will output :
copied
copied
copied

there is 3 time copy because we allocate vector in heap {2,4,5}, then we copy them to vector element 'v.push_back(...', we can use emplace_back to create new vector object in the vector element address instead

int main() {
  std::vector<Vertex> v;
  v.emplace_back(2,4,5);
  v.emplace_back(2,5,3);
  return 0;
}

this will output : 
copied

this last copy is because when we doesnt specify how many element the vector must allocate first, it will be only 1. then when 2nd element about to be inserted to vector, the vector must double it size first (allocate size^2, then copy the old data to new address, then delete the old data). we can specify how many element by using v.reserve(int)

int main() {
  std::vector<Vertex> v;
  v.reserve(2);
  v.emplace_back({2,4,5});
  v.emplace_back({2,5,3});
  return 0;
}

done, no more copying.

## Library
- There are two parts in a library usually: includes and libraries. İnclude directory has a bunch of header files (basically, those header files are interfaces for the libraries) and lib directory has those pre-built binaries (the definition).
- Static linking happens at compile time and added to the executable. when you compile a static lib, you then link it into either executable (if you making app) or library (if you making library)
  static linking can have many optimization, coz compiler and linker know more about the lib, and the lib gets loaded to memory.
- dynamic linking happens at runtime, when we launch the .exe, the dynamic link lib get executed.
  there is two version :
  * static dynamic version, the exe know what function that it can use.
  * full dynamic, the exe doesnt know what in it, but want to use some stuff from it.
- We have to point our compiler to header files (include files) and then we also have to point out our linker to library files.

library file types :
.dll file is the dynamic link library
dll.lib is static library that we use with the dll, it contains series of pointer that locate every function and symbol inside of .dll file, so the linker can link them immediately (this file is required for the static-dynamic version of dynamic linking. when you want to create your own dll.lib, you must compile it with it's .dll counterparts at the same time).
.lib is the static library, this is what we link if we dont want compile-time linking, and if we use this, we dont need .dll file to be with .exe file at runtime.

static linking steps :
* create dependency folder inside ${SolutionDir} folder.
* put dependency there, e.g. for glfw, create glfw folder there
* put the include and lib folder inside glfw folder (the lib compiler version (mingw, vc-2015, etc) doesnt matter, but pick the latest)
* project > properties > all configuration (dont forget to check the configuration)
* add include (headers) : project > properties > additional include directory > ${SolutionDir}dependencies\GLFW\include.
  now you can include file relative to this path e.g. #include <GLFW/glfw3.h> you can use quotes also (quote check relative path first), 
  the convention is using angle bracket for external lib :
  * (binary compiled lib)
  * and use quote if the source file is in this solution (whether its different project, or same doesnt matter) 
* link the definition : 
  * project > properties > additional library directory > ${SolutionDir}dependencies\GLFW\lib-vc2015
  * project > properties > linker > input > glfw3.lib (remember, the .lib for static) (no need to specify path because you already do them in additional lib dir, but you can specify path in this instead (less cleaner))

dynamic linking steps :
* using same project from static version
* project > property > linker > input > glfw3dll.lib
* place the .dll file in same location as executable 
* include directive can remain identical to static version

If you use lib + dll，you don't need __declspec(dllimport) because all the functions or variables you wan't to import have defined in lib as pointer;but if you use LoadLibary API to import dll, with __declspec(dllimport) you can tell the compiler which function or variable you want to import, it will reduce the import time.

note : you can omit the #include <GLFW/glfw3.h> and provide declaration in the file :
int glfwinit(); //this will throw error, because glfw is c library, so you need to write code bellow instead
extern "C" int glfwinit();

## making library and use them
example step :
* create new "Game" solution
* solution > add > new project > empty project > give name "Engine"
* make sure  under Game project properties configuration type set to application
* for engine we want them as lib so, engine project > configuration properties > general > configuration type > static library (.lib) make sure configuration for all configuration

now when we compile the solution, game compiled to executable, and engine compiled to .lib

assume this the folder structure :
solution 'Game' :
  Engine
    Engine.h
    Engine.cpp
  Game
    Game.cpp

now if we want to link game to engine we can :
  * relative include from game (bad, if we change the engine folder structure)
  * add linker input with the compiled library (better, but still bad, if we change engine project name we need to change input name, and if we only compile the game project but the engine library has changed, we wont compile and get the latest engine library)
  * using visual studio automation, (best)
    * Game project > add > reference > select Engine project > ok

remember to add engine folder to compiler include directories to be able to #include the header from Game.cpp

## C++ multiple return types
we can use :
* Reference out parameter (can return differnt type) (good performance, eventough there is copy)
  static void parseShader(const std::string& filePath, std::string& outVertexSource, std::String& outFragmentSource){
    ...
    std::string vs = ss[0].str();
    std::string fs = ss[1].str();

    outVertexSource = vs; //copying
    outFragmentSource = fs; //copying
  }

  std::string vs, fs;
  parseShader("res/shaders/Basic.shader", vs, fs);

* Pointer out parameter (can return differnt type) (good performance) (can be passed null, if caller doesnt care about that value) (make the parameter stand-out (because its a pointer param) so devs know that its different parameter ), we must be careful, if the out value is stack allocated, we must copy the data, instead of the pointer. 
  static void parseShader(const std::string& filePath, std::string* outVertexSource, std::String* outFragmentSource){
    ...
    std::string vs = ss[0].str();
    std::string fs = ss[1].str();

    if(vs)
      *outVertexSource = vs; //copying data
    if(fs)
      *outFragmentSource = fs; //copying data
  }

  std::string fs;
  parseShader("res/shaders/Basic.shader", nullptr, fs) // nullptr passed instead of vs, that mean we dont care about vs in this example

* return array (can only return same type), slower due to heap allocation, we dont know how big the array
  static std::string* parseShader(const std::string& filePath){
    ...
    return new std::string[]{vs,fs};
  }

* return std::array (can only return same type), array store it element in stack
  static std::array<std::string, 2> parseShader(const std::string& filePath){
    ...
    std::array<std::string, 2> results;
    results[0] = vs;
    results[1] = fs;
    return results;
  }

* return vector (can only return same type) vector store it element in heap, slower than array.
  static std::vector<std::string> parseShader(const std::string& filePath){
    ...  
    std::vector<std::string> results;
    results.push_back(vs);
    results.push_back(fs);
    return results;
  }

* return tuple (can return different type)
  #include <utility> //for make_pair 
  #include <tuple>

  static std::tuple<std::string, std::string> parseShader(const std::string& filePath){
    ...  
    return std::make_pair(vs,fs);
  }

  auto sources = parseShader("res/shaders/Basic.shader");
  std::string vs = std::get<0>(sources);
  std::string fs = std::get<1>(sources);

  //that getter function above is goofy syntax

  //we can use tupple tie (c++11) for cleaner code
  std::string vs, fs;
  tie(vs,fs) = ParseShader("res/shaders/Basic.shader") // tie() is in the <tuple> header

  //or we can use structured binding (c++17)
  auto [vs, fs] = parseShader("res/shaders/Basic.shader");

* return pair (can return different type) (can only return 2 item)
  #include <utility> //for make_pair 
  #include <tuple>

  static std::tuple<std::string, std::string> parseShader(const std::string& filePath){
    ...  
    return std::make_pair(vs,fs);
  }

  auto sources = parseShader("res/shaders/Basic.shader");
  std::string vs = sources.first;
  std::string fs = sources.second;

* Struct (best way) (can return different type) (what variable available is very clear because the variable are named, unlike array, tuple, and pair)
  struct ShaderProgramSource{
    std::string vertexSource;
    std::string fragmentSource;
  };

  static ShaderProgramSource parseShader(const std::string& filePath){
    ...
    return { vs, fs };
  }


## Templates
More powerful than other language generics, a bit like macro

With templates, you must either write AND use them inside a SINGLE source (cpp) file, OR you must write them ENTIRELY inside a header file. Unlike a regular function or class, you can NOT declare them inside a header file and then define them inside a source file. The linker won't be able to link the templates if you do.

templates allow compiler to write code for you based on the rules that you given (meta-programming)

template is so powerful, so use it with responsibility.
irresponsible template usage e.g. using template so complex that when it have an issues, no one understand whats going on. like having a template generate an entire meta-language. finding out what code being compiled with complex template can take hours, so keep it simple.

* Template Parameter
  Template<typename T> // can be written as Template<class T>
  void print(T value){
    std::cout << value << std::endl;
  }

  this isnt real function, this function only get created when we call it,  based on what type we call it with, the compiler will then compile it  with that type.
  the code above can also written using overloading if you want to support  only small set of type.

  int main(){
    print(5); //the type specified implicitly
    print("Taufiq");
    print<long int>(6); //type specified explicitly
  }

  if print function never get called, it will never get compiled, and wont  throw error even if there is something wrong with the function (some   compiler will throw error tho)

* Template Class
  template<typename T, int N>
  class Array{
  private :
      T data[N];
  public :
      T& operator[](int i){
          return data[i];
      }
  };

  int main(){
      const int ARRAY_LENGTH = 3;

      Array<int, ARRAY_LENGTH> arr;

      for(int i = 0; i < ARRAY_LENGTH; i++)
          arr[i] = i;

      for(int i = 0; i < ARRAY_LENGTH; i++)
          std::cout<< arr[i] << std::endl;
  }

  the compiler must know how many element the array have, and template evaluated at compile time, so it will work

## Stack Vs Heap

when app run, it allocate area of memories.
stack and heap is two area that we have in ram, there are many more area, but we only care about stack and heap.
stack have predefined size, heap also kindof predefined, but it can grow and change as the app goes on.

allocating memory in stack is just one cpu instruction. when we allocate variable in stack, the stack pointer just move, the memory is stored on top of each other like a stack, so the allocated memory for all stack variable is contiguous, that why the memory can be cached in cpu (hot memory, no cache misses).

allocating and deleting memory in heap is very heavy, it has to go through freelist, and ask for memory then book-keep them. 
the access performance penalty of heap is **usually** negligible.

the cache misses of cpu between stack and heap is not really that much, the main performace cost of heap is the allocation.

the only reason to allocate on heap is because of you cant allocate on stack, rule of thumb for heap allocation is :
* longer lifetime than the scope is necessary
* allocating large object (e.g. 40 mb texture)

you can preallocate heap (e.g. 3 gb block of memory), then heap allocate from that preallocated memory to bypass the heap allocation performance cost.

A few notes:
- to be clear, each program/process on our computer has its own stack/heap
- each thread will create its own stack when it gets created, whereas the heap is shared amongst all threads

## Macro
using preprocessor in c++ to macrofy operation
preprocessor stage is basically text editing stage in which we can control what code get fed to compiler.

macro is pure text replacing, you can replace ANYTHING, thats the different from template.
dont overuse macro, because its really powerful.

when macro definition is in another file, it **will** become a confussion for other programmer, so dont obfuscate your code using macro.

* macro example
#define MACRO_NAME <anything>

the <anything> part can become literally anything, a statement, curly braces, symbol, anything.
this macro will find the the word MACRO_NAME in our code, then it will replace the word with the <anything> part.

macro can have parameter, argument, and variable, so we can customize the way we call that macro.

you can define preprocessor definition in visual studio in project > properties > c/c++ > preprocessor > preprocessor definitions
then use the definition for ifdef macro

useful usecase :
#if PR_DEBUG == 1
#define LOG(x) std::cout << x << std::endl;
#else
#define LOG(x)
#endif

#ifdef PR_DEBUG can work too, but you cant really do #define PR_DEBUG == 0

the macro above when there is no PR_DEBUG in preprocessor definitions (in compiler or in the code above it) will have empty output
Its sometimes better to define the empty output as "{}" or "((void)0)" - These will make it a valid line of code without it doing anything. This is important if your macro can be called from within a single condition line, like if(x) MACRO();
but example above works too.

you can turnoff block of macro by wrap em with
#if 0
...
#endif

common pitfalls of parameterized macros, such as the following 2 examples:
#define MAX(x, y) x > y ? x : y

int main() { return MAX(7,5) * 10; }

Here main() will return 7 instead of 70:
7 > 5 ? 7 : 5 * 10  ==>  7

To fix this, do:
#define MAX(x, y) (x > y ? x : y)

Another issue can be seen in this example:
#define MULT(x, y) (x * y)
int main() { return MULT(2+5, 10); }

Here main() will return 52 instead of 70:
2+5*10  ==> 52

To fix this, do:
#define MULT(x,y) ((x) * (y))
Of course, y can be a pointer so it is possible that *(y) is seen as a dereference. In addition, either x or y can be almost any text which can result in weird generated code.

The takeaway is that **parameterized macros are NOT functions: they simply replace symbols with parameterized text**.

## auto
automatically assign variable types from the expression return types

std::string getState()

int main(){
  auto state = getState(); //state will become string
}

if the api return types changed:
int* getState()
this will break code.

so use auto minimally or none at all.

the best use is for :
  * iterator
    std::vector<std::string> strings;

    for(std::vector<std::string>::iterator it = strings.begin();
      it != strings.end(); it++)

    or you can write :
    for(auto it = strings.begin(); it != strings.end(); it++)

  * long type name
    DeviceManager dm;
    const std::unordered_map<std::string, std::vector<Device*>>& devices = dm.getDevices();

    you can do:
    const auto& devices = dm.getDevices();

    or
    using DeviceMap = std::unordered_map<std::string, std::vector<Device*>> //you can define this using inside the DeviceManager instead.
    const DeviceMap& devices = dm.getDevices();

    or
    typedef std::unordered_map<std::string, std::vector<Device*>> DeviceMap;
    const DeviceMap& devices = dm.getDevices();

    if the api is gonna change frequently dont use auto or you dig your own grave.

TIP: In current MVS2022, if you double click left ctrl, the inferred type will be displayed between auto and the name. 

auto can used function types and in trailing return types
* auto getName()
* auto getName() -> char*
  this is just style choice, personally its redundant, just put char* instead of auto

## std::array (c++ array) vs c array
main difference :
  * c++ :
    1. has out of bound checking (when in debug mode)
    2. has zero overhead constexpr size getter function
      constexpr means the compiler will just replace the function call itself with 5, so something like:
      int s = ary.size();
      // literally becomes : 
      int s = 5;
      // with no function call at runtime.

to get the size from std::array template, use
template<size_t Length>
void printArray(const std::array<int, Length>& data) //can also template the type to be more general than only accepting int
  
## pointer function
void print()
int add(int x, int y)

you can declare variable that hold function pointer with :
* auto printFunctionPointer = &print //you can omit the &, there is implicit conversion for function pointer
* int(*addFunctionPointer)(int, int) = print; // the *addFunctionPointer is the variable that will hold the pointer
* int(*addFunctionPointer)(int,int);
  addFunctionPointer = add;
* typedef int(*AdderFunctionType)(int,int);
  AdderFunctionType addFunctionPointer = add;

weird syntax, i know, its primitive c types, no one use it in c++ anymore
the c++ way :
#include <functional>
std::function<int(int,int)> addFunctionPointer = add;

but remember, using std::function introduces potentially unnecessary runtime overhead. Only use std::function if you require its runtime flexiblity (compared to a template parameter) and/or do not want bigger binary sizes.

### function pointer as a parameter
void forEach(const std::vector<int>& vec, void(*callback)(int)){ 
  //can also use c++ way instead : std::function<void(int)>& callback
  for(int item : vec)
    callback(item);
}

## Lambda
a way to define a function without defining a function.
whenever you have a function pointer, you can use lambda instead.

refer to this if you not sure things work : https://en.cppreference.com/w/cpp/language/lambda

syntax :
[](int value){std::cout << value;}

[...] : capture variable
(...) : parameter
{...} : body

add mutable if you want to mutate variable captured in lambda, even passed by value variables

## using namespace
never ever using namespace in header.
use it very rarely or even better never use namespace, if you use it use it for your own library and use it in smallest scope possible (e.g. inside if,for, or function blocks).

## namespace
The "::" is called the scope resolution operator.

the c lang doesnt have namespace, so thats why glfw function have glfw prepend to its name (e.g. glfw_init())

you can nest namespace
namespace A{
  namespace B {
    int func()
  }
  void func()
}

class is a namespace of its own, that why we use :: on class also (for static, and class inside that class).

* using namespace
int main(){
  using namespace A::func;
  func();
}

//can also
int main(){
  using namespace A;
  using namespace func;
  func();
}

* alias for namespace : 
namespace X = A::B;
X::func()

## Thread
#include <thread>
#include <iostream>

bool isFinished = false;

void doWork() {
    using namespace std::literals::chrono_literals; //to use 1s literal

    while (!isFinished) {
        std::cout << "press enter to continue..." << std::endl;
        std::this_thread::sleep_for(1s); // if this code doesnt exist, the cpu usage will be 100% 
    }
}

int main() {
    std::thread worker(doWork);

    std::cin.get();
    isFinished = true;

    worker.join(); //this will block current thread, and wait until the other thread finished

    std::cout << "enter pressed" << std::endl;
}

## Timing in c++
chrono library is platform independent timing library, it quite fast, but you can have faster timer by using platform dependent timing library.

#include <iostream>
#include <thread>
#include <chrono>

constexpr int ARR_LENGTH = 50;

class Timer {
private : 
	std::chrono::steady_clock::time_point begin, end;
	std::chrono::duration<float> duration;
public :
	Timer()
		: begin(std::chrono::high_resolution_clock::now()) {}
	~Timer() {
		end = std::chrono::high_resolution_clock::now();
		duration = end - begin;
		float ms = duration.count() * 1000.0f;
		std::cout << "Timer took :"<< ms << "ms" << std::endl;
	}
};

int* initHeapArr() {
	Timer timer;
	int* arrInt = new int[ARR_LENGTH];
	for (int i = 0; i < ARR_LENGTH; i++)
		arrInt[i] = 0;
	return arrInt;
}

void initStackArr() {
	Timer timer;
	int arrInt[ARR_LENGTH];
	for (int i = 0; i < ARR_LENGTH; i++)
		arrInt[i] = 0;
}

int main() {
	delete initHeapArr(); //0.015ms, 0.0067ms, 0.0365ms
	initStackArr(); // 0.0002ms, 0.0004 ms, 0.0007ms
}

## multidimensional array

int* array = new int[50] // 200 bytes
int** a2d = new int*[50] // 200 bytes (in 32 bit os)

for(i=0; i<50; i++)
  a2d[i] = new int[50]

remember the **new** keyword job is just to allocate a memory (it can also call constructor if the type is not primitives), so in a way it loosely enforce typing, but in the example above, a2d and array look the same from computer prespective. 
its only the programmer that will see the diferent semantic beetween an array of int, or an array of pointer to int (can be array of integer)
and the compiler that will kinda enforce typing, e.g. you can do a2d[0] = nullptr, but not array[0] = nullptr.

now if we create multidimentional array the int** way its gonna be hard to maintain **and** the memory is not contiguous. so we can just using one dimensional array and some function to make the one dimension array act like 2d or 3d array. 

why its hard to maintain?
because you cant do delete[][] a2d;

you must delete each stored pointer first :
for(i=0; i<50; i++)
  delete[] a2d[i]
delete[] a2d;

now imagine that as a 3d array.

if you only do delete[], you will leak memory, because you only delete array of pointer, and not the pointer of ints

note: when storing image in memory you can use 1d array instead of 2d array.

## sorting
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
	std::vector<int> vec = { 3,1,4,5,2 };
	std::sort(vec.begin(), vec.end(), [](const int& a, const int& b) {
		if (a == 1)
			return false;
		if (b == 1)
			return true;
		return a < b;
	});

	for (const auto& i : vec) {
		std::cout << i << ", "; // 2 ,3 ,4 ,5 ,1 ,
	}
}

the callback for sort fuction takes two variable a and b, if you want a bubble up, then the callback must return true.

## type punning
c++ is strongly typed language, but its not as strongly forced like java, or c# in a way that in c++ the type are only enforced by compiler, but we can directly access the memory, and you can treat it as any type you want (as double, as class), and you can get around c++ type system rather easily, in some (uncommon) case its ok to do that (e.g. serializing plain old data struct with no pointer, into bits).

### very bad cicumventing type system example : 
int a = 50;
double& b = (double*) &a; // you can use double b = *(double*) &a to create a copy of a memory value, and interpret it as double (undefined behaviour), but atleast you can write to b(8byte memory) without changing value of a (4byte memory) that can crash our program.
std::cout << b << std::endl; //Undefined behaviour, because double is 8 byte memory address, while int is 4 byte memory
b = 5; //even batshit insane, because b point to a, and a only hold 4 byte, well we write to double, mean we gonna write 8 byte of memory (this will cause undefined behaviour, even crash)

### ok example, struct pointer trick:
struct Entity {
	int x, y;
};

int main() {
	Entity e = { 5,8 };
	int* ep = (int*) &e;
	int y = * (int*) ((char*)&e + 4); //copy the content of e.y into y variable. (char*) so we read memory in a byte, offset by 4, then read memory as int
	std::cout << ep[0] << ", " << ep[1] << std::endl; // 5, 8
	std::cout << y << std::endl; // 8
}

this example useful when you want maximum speed. example kinda slow function :
struct Entity {
  int x, y;
   
  int* getPositions(){
    int* positions = new array[2];
    positions[0] = &x;
    positions[1] = &x;
    return positions;
  }
}
you can make it faster function by :
struct Entity {
  int x, y;
   
  int* getPositions(){
    return &x;
  }
}

good note from experts when you do struct pointer tricks :

* Before some newbie programmer does any of those pointer tricks, you should also teach them about packing because when you start mixing types inside a struct and/or switching platforms, the code will not work if the padding changes.  I've been in the industry as a ASM/C/C++ programmer since the mid 80s, and to be honest other than when messing around, people don't do stuff like that in production code (unless they want to be fired).

* It's worth noting that type punning via arbitrary types, even if they're the same size has undefined behaviour. You can argue that it might appear to work in some situations or with optimisations turned off, but it's generally just not a good idea. Enabling optimisations, compiling to a different platform, adding a comment above the code etc... could break the code. Your initial Entity example for example violates the strict aliasing rule and isn't legal C++.

just remember, type punning is controversial topic, you might get fired if you use it in production.

## Union
Struct like data structure that can only occupy 1 member allocated memory.
(so if you changed one member var of union the other member will get changed, because it literally point to same memory address)
the usage of union is closely related to type punning.

usually union used anonymously.

int main() {
	struct Union {
		union {
			short a;
			char b;
		};
	};

	Union u;
	u.a = 84;
	std::cout << u.a << ", " << u.b << std::endl; //84, T
}

### usecase : vector2d-ify vector4
struct Vector2{
  float x,y;
};

struct Vector4{
  float x,y,z,w;
};

we want to be able to split vector 4 xy, zw into vector 2

we can do :
struct Vector4 {
  ...
  Vector2 getA() {
    return *(Vector2*)&x;
  };
  Vector2 getB() {
    return *(Vector2*)&z;
  };
};

the return will create a copy because its return by value
![](/assets/cherno-cpp/return-by-value-memory-copying.jpg){max-height: 150px, display: block, margin: 0 auto}

or we can do union so we dont do copying :

struct Vector4 {
    union {
        struct {
            float x, y, z, w;
        };
        struct {
            Vector2 a, b;
        };
    };
};

int main(){
    Vector4 v4 = { 4,5,3,2 };
    Vector2& aV4 = v4.a;
    std::cout << aV4.x << ", " << aV4.y;
}

To put it a different way, your creating variables like normal but your telling C++ to place them all starting at the same address. This greatly saves on memory used with the downside that you can only use one at a time because they all start at the same address. As you can imagine these were enormously helpful in the 90's when memory was limited, they still have use cases today like his example and the stuff you can do with them is quite a lot but you have to be careful because it can feel like your working with different unique variables and in reality your working with different variables all occupying the same starting space. Unions can tend to trip you up if your're not careful leading to really wacky bugs but they can be a powerful tool if used seldomly and correctly.

One thing to keep in mind though is that structs can use padding bytes. so when you for instance want to use a union to access members of different types in a byte array, you might end up getting weird results.

## Polymorph
class Base {
public :
	Base() {
		std::cout << "Base Constructor" << std::endl;
	}
	virtual ~Base() {
		std::cout << "Base Destructor" << std::endl;
	}
};

class Derived : public Base {
public :
	Derived() {
		std::cout << "Derived Constructor" << std::endl;
	}
	~Derived() {
		std::cout << "Derived Destructor" << std::endl;
	}
};

int main(){
	std::cout << "========== Base =============" << std::endl;
	Base* b = new Base();
	delete b;

	std::cout << "========== Derived =============" << std::endl;
	Derived* d = new Derived();
	delete d;

	std::cout << "========== Polymorph =============" << std::endl;
	Base* p = new Derived();
	delete p; // if the base destructor doesnt marked as virtual, the derived destructor wont get called
}

result :
========== Base =============
Base Constructor
Base Destructor
========== Derived =============
Base Constructor
Derived Constructor
Derived Destructor
Base Destructor
========== Polymorph =============
Base Constructor
Derived Constructor
Derived Destructor
Base Destructor

so if you intend to extend your class, make sure to mark its destructor virtual

## casting in c++
### c style casting
* implicit
int i = 5;
double d = 5; 

* explicit
int i = 5;
double d = (double) 5; 

cons : 
hard to find,
sometime allow narrowing (double to int),
doesnt give compile time check.

### c++ casting
c++ casting are not different with c style casting per se, you can do anything here with c style, the main different is, c++ style casting allow you to convey your intention in english words, so other programmer know wtf you about to do.

* static cast
  The static cast performs conversions between compatible types. It is similar to the C-style cast, but is more restrictive. 
    For example, the C-style cast would allow an integer pointer to point to a char.
    char c = 10;       // 1 byte
    int *p = (int *)&c; // 4 bytes
    *p = 5; // run-time error: stack corruption

  In contrast to the C-style cast, the static cast will allow the compiler to check that the pointer and pointee data types are compatible, which allows the programmer to catch this incorrect pointer assignment during compilation.
  int *q = static_cast<int*>(&c); // compile-time error

  pros :
  * static_cast<>() gives you a compile time checking ability, C-Style cast doesn't.
  * static_cast<>() is more readable and can be spotted easily anywhere inside a C++ source code, C_Style cast is'nt.
  * Intentions are conveyed much better using C++ casts.

* reinterpret_cast
  Converts between types by reinterpreting the underlying bit pattern.
  commonly used in type punning.

  If you don't know what reinterpret_cast stands for, don't use it. If you will need it in the future, you will know.

* dynamic cast
  Safely converts pointers and references to classes up, down, and sideways along the inheritance hierarchy.
  run on runtime

  dynamic_cast< target-type >( expression )		

  dynamic_cast is useful when you don't know what the dynamic type of the object is. If the cast fails and target-type is a pointer type, it returns a null pointer of that type. If the cast fails and target-type s.

  class Base ...
  class Derived : Base ...
  class OtherDerived : Base ...

  Derived derived; 
  Base *base = &derived;
  OtherDerived* dynamic_cast<OtherDerived*>(base); //nullptr
  
  You can not use dynamic_cast for downcast (casting to a derived class) if the argument type is not polymorphic. For example, the following code is not valid, because Base doesn't contain any virtual function.

  An "up-cast" (cast to the base class) is always valid with both static_cast and dynamic_cast, and also without any cast, as an "up-cast" is an implicit conversion

## Conditional and action breakpoint in mvs
Right click on breakpoint > check condition / action
powerful because we can change this breakpoint in runtime, no need to recompile app for this breakpoint to take effect.

### Action
log message to console by writing {expression} (e.g. mouse position is = {(float)x})

### Condition
will execute action if the condition is satisfied

