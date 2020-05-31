# Memory Management

### Outline
I. Overview Memory Type

#### I. Overview Memory Type
- Common memory tpes: RAM/ROM, Cache (L1/L2), Registers, Virtual Memory, Hard Disks, USB drives

#### IV. Dynamic Memory Allocation
1. Heap Memory
- Is dynamic memory. Grows upward while the stack grow in the opposite direction. 
- THe programmer can request allocation of memory by issuing a command such as **malloc** and **new**. The block of memory will remain allocated until the programmer explicitly issues a command such as **free** or **delete**. 
- Properties of heap memory:
    - Memory can be allocated in an arbitrary scope (e.g. inside a function) without it being deleted when the scope is left. 
    - Local variables on the stack allocated at compile-time. Thus, the size of string variable might not be appropriate as the length of the string will not be known until the program is executed and the user inputs it. With local variables, a solution is to allocate a long enough array and hope the length does not exceed the buffer size. With dynamically allocated heap memory, variables are allocated at run-time. This means that the size of the above-mentioned string variable can be tailored to the actual length of the user input.
    - Heap memory is only constrained by the size of the address space by available memory. If programmer forgets to deallocate a block of heap memory, it will remain unused until the program is terminated. This is called "memory leak".
    - Until stack, heap is shared among multiple threads.
    - When memory is allocated and deallocated on the stack, the stack pointer is simply shifted upwards or downwards. Due to the sequential structure of stack memory management, stack memory can be managed (by the operating system) easily and securely. With heap memory, allocation and deallocation can occur arbitrarily, depending on the lifetime of the variables. This can result in fragmented memory over time, which is much more difficult and expensive to manage. 
2. Malloc and Free
- Malloc return

3. New and Delete
- **malloc** and *free** are default way to allocate and deallocate memory in C. In C++, theya re also part of the standard and can be used to allocate block of memory on the heap.
- With class and OOP in C++, memory allocation and deallocation has become more complex: when an object is created, its constructor needs to be called to allow member initialization. On object deletion, the destructor is called to free resources and to allow for programmer-defined clean-up tasks. For this reason, C++ introduce **new** and **delete**, which represent the OOP counterpart to memory management with **malloc/free**.
- Example:
    ```cpp
    #include <stdlib.h>
    #include <iostream>

    class MyClass
    {
    private:
        int *_number;

    public:
        MyClass()
        {
            std::cout << "Allocate memory\n";
            _number = (int *)malloc(sizeof(int));
        }
        ~MyClass()
        {
            std::cout << "Delete memory\n";
            free(_number);
        }
        void setNumber(int number)
        {
            *_number = number;
            std::cout << "Number: " << _number << "\n";
        }
    };


    int main()
    {
        // allocate memory using malloc, this will fail because malloc does not
        // initiate Constructor and Destructor
        // comment these lines out to run the example below
        MyClass *myClass = (MyClass *)malloc(sizeof(MyClass));
        myClass->setNumber(42); // EXC_BAD_ACCESS
        free(myClass);
        
        // allocate memory using new
        // This works because Constructor and Destructor is called
        MyClass *myClass = new MyClass();
        myClass->setNumber(42); // works as expected
        delete myClass;

        return 0;
    }
    ```
- Difference between **new/delete** vs **malloc/free**:
    - Constructor/Destructor are called with new and delete. Unlike malloc.
    - Malloc return void pointer and needs to be cast into appropriate data type it points to. This is not type safe. New returns the correct type automatically - it is thus type-safe.
    - Operator overloading: malloc and free are functions defined in a library, their behavior cannot be changed easily. The new and delete operators can be overloaded by a class in order to include optional properietry behavior. 
- Placement New: in some cases, it makes sense to separate memory allocation from object construction. Consider a case where we need to reconstruct an object several times. If we were to use the standard **new/delete** construct, memory would be allocated and freed unneccesrily as only the content of the memory block changes but not its size. By separating allocation from construction, we can get a significant performance increase.
    - Exammple:
        ```cpp
        void *memory = malloc(sizeof(MyClass));
        MyClass *object = new (memory) MyClass;

        // Deallocate
        object->~MyClass();
        free(memory);
        ```
- Major advantage of new/delete over malloc/free is the ability of overloading. 
    - Example:
    ```cpp
    #include <iostream>
    #include <stdlib.h>

    class MyClass
    {
        int _mymember;

    public:
        MyClass()
        {
            std::cout << "Constructor is called\n";
        }

        ~MyClass()
        {
            std::cout << "Destructor is called\n";
        }

        void *operator new(size_t size)
        {
            std::cout << "new: Allocating " << size << " bytes of memory" << std::endl;
            void *p = malloc(size);

            return p;
        }

        void operator delete(void *p)
        {
            std::cout << "delete: Memory is freed again " << std::endl;
            free(p);
        }
    };

    int main()
    {
        MyClass *p = new MyClass();
        delete p;
    }
    ```
    - Example of overloading array of object:
    ```cpp
    #include <iostream>
    #include <stdlib.h>

    class MyClass
    {
        int _mymember;

    public:
        MyClass()
        {
            std::cout << "Constructor is called\n";
        }

        ~MyClass()
        {
            std::cout << "Destructor is called\n";
        }

        void *operator new[](size_t size)
        {
            std::cout << "new: Allocating " << size << " bytes of memory" << std::endl;
            void *p = malloc(size);

            return p;
        }

        void operator delete[](void *p)
        {
            std::cout << "delete: Memory is freed again " << std::endl;
            free(p);
        }
    };

    int main()
    {
        MyClass *p = new MyClass[3]();
        delete[] p;
    }
    ```

4. Typical Memory Management Problems
- Memory Leaks:
    - Occur when data is allocated on the heap at runtime, but not properly deallocated. 
    - A program that forgets to clear a memory block is said to have a memory leak.
    - For a program that runs, computes something, and quits immediately, memory leaks are usually not a big concern. Memory leaks are mostly problematic for programs that run for a long time and/or use large data structures. In such case, memory leaks can gradually fill the heap until allocation requests can no longer be properly met and the program stops responding or crashes completely.
- Buffer Overruns: 
    - Occur when memory outside the allocated limit is overriten and thus corrupted. This effect may not become immediately visible. When a problem finally does occur, cause and effect are often hard to discern.
    ```cpp
    char str[5];
    strcpy(str,"BufferOverrun");
    printf("%s",str);
    ```
- Uninitialized Memory: allocating memory on the heap without proper initialization sometimes contain garbage that can cause problems. 
- Incorrect pairing of allocation and deallocation: freeing a block of memory more than once will cause a program to crash. 
    Example:
    ```cpp
    // Wrong new and deletate
    double *pDbl=new double[5];
    delete pDbl;

    // pairing is correct but delete more than once
    char *pChr=new char[5];
    delete[] pChr;
    delete[] pChr;
    ```
- Invalid memory access: error occurs when trying to access a block of heap memory that has not yet or has already been deallocated. 
    Example:
    ```cpp
    char *pStr=new char[25];
    delete[] pStr;
    strcpy(pStr, "Invalid Access");
    ```
#### V. Resource Copying Policies
1. Copy Semantics
- In C++, a common way of safely accessing resources is by wrapping a manager class around the handle, which is initialized when the resource is acquired (in the class constructor) and released when it is deleted (in the class destructor). This concept is often referred to as Resource Acquisition is Initialization (RAII). One problem with this approach is that copying the manager object will also copy the handle of resource; this allows two objects access to the same resource and free the same resource. 
- **No copying policy**: simpliest policy, forbid copying and assigning class instances all together. 
- **Exclusive ownership policy**: whenever a resource manager object is copied, the resource handle is transferred from the source pointer to destination pointer. And the source pointer is set to `nullptr` to make ownership exclusive. At any time, the resource handle belongs only to one object. In concurrent programs, this may cause data race if there are two valid handles to the same resource. Better method for this case is move semantics.
- **Deep copying policy**: 
- **Shared ownership policy**: copying the handle instead of content (shallow copy) while at the same time keeping track of number of instances that also point to the same resources. Each time the instance goes out of scope, the counter is decremented. 

2. Lvalues and Rvalues
- Lvalues: have an address that can be accessed. They are expressions whose evaluation by the compiler determines the identity of objects or functions.
- Prvalues: do not have an address that is accessible directly. They are temporary expressions used to initialize objects or compute the value of operand and an operator.
- l stands for left, r stands for right. `int i = 42; // lvalue = rvalue;`. lvalue is an entity that points to a specific memory location. An rvalue is usually a short-lived object, which is only needed in a narrow local scope. 
- Double ampersand `int &&l` creates rvalue reference. 