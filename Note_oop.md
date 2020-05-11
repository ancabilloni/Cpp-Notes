## OOP

### I. Intro
#### 1. Structure
- **Structures** allow developers to create their own types ("user-defined" types) to aggregate data relevant to their needs.
```cpp
struct Rectangle {
  float length;
  float width;
};

// Member initialization
struct Date {
  int day{1};
  int month{1};
  int year{0};
};
```
- C++ includes fundamental types, such as int and float. These fundamental types are sometimes called "primitives".
- Structures members can be private or public. By default, all member of structure are public, unless they are specifically marked private.
- Private members of a class are accessible only from within other member functions of the same class.
- There is a third access modifier called protected, which implies that members are accessible from other member functions of the same class (or from their "friends"), and also from members of their derived classes. 
- To access private members, we typically define public "accessor" and "mutator" member functions (sometimes called "getter" and "setter" functions).
```cpp
struct Date {
 public:
  int Day() { return day; }
  void Day(int day) { this->day = day; }
  int Month() { return month; }
  void Month(int month) { this->month = month; }
  int Year() { return year; }
  void Year(int year) { this->year = year; }

 private:
  int day{1};
  int month{1};
  int year{0};
};
```

#### 2. Classes
- Classes are like structures, provide a way for C++ programmers to aggregate data together in a way that makes sense in the context of a specific program. 
- By convention, programmers use structures when member variables are independent of each other, and use classes when member variables are related by an "invariant". An "invariant" is a rule that limits the values of member variables. 
- Classes members default to private (different with struct)
- As a general rule, member data subject to an invariant should be specified private, in order to enforce the invariant before updating the member's value.
- Constructor: member function of a class or struct that initialize an object. 
- A protected member variable or function is very similar to a private member but it provided one additional benefit that they can be accessed in child classes which are called derived classes.

#### 3. Encapsulation vs Abstraction
- Encapsulation: the grouping together of data and logic into a single unit. 
- Abstraction: refers to the separation of a class's interface from the details of its implementation. The interface provides a way to interact with an object, while hiding the details and implementation of how the class works.

#### 4. Scope Resolution
- C++ allows different identifiers (variable and function names) to have the same name, as long as they have different scope. For ex, two different functions can each declare the variable `int i`, because each variable only exists within the scope of its parent function.

- In some cases, scopes can overlap, in which case the compiler may need assistance in determining which identifier the programmer means to use. The process of determining which identifier to use is called "scope resolution".
- `::` is the scope resolution operator. This operator is used to specify which namespace or class to search in order to resolve an identifier. 
- Class example:
```cpp
class Date {
 public:
  int Day() const { return day; }
  void Day(int day);  // Declare member function Date::Day().
  int Month() const { return month; }
  void Month(int month) {
    if (month >= 1 && month <= 12) Date::month = month;
  }
  int Year() const { return year; }
  void Year(int year) { Date::year = year; }

 private:
  int day{1};
  int month{1};
  int year{0};
};

// Define member function Date::Day().
void Date::Day(int day) {
  if (day >= 1 && day <= 31) Date::day = day;
}
```
- **Namespace** allows programmers to group logically related variables and functions together. Namespaces help to avoid conflicts between two variables that have the same name in different parts of a program.
- Namespace example:
```cpp
namespace English {
void Hello() { std::cout << "Hello, World!\n"; }
}  // namespace English

namespace Spanish {
void Hello() { std::cout << "Hola, Mundo!\n"; }
}  // namespace Spanish

int main() {
  English::Hello();
  Spanish::Hello();
}
```

#### 5. Initializer Lists
- Initialize member variables to specific values, just before the class constructor runs.
- In fact, initialization lists ensure that member variables are initialized before the object is created. This is why class member variables can be declared const, but only if the member variable is initialized through an initialization list. Trying to initialize a const class member within the body of the constructor will not work. 
```cpp
Date::Date(int day, int month, int year) : year_(y) {
  Day(day);
  Month(month);
}
```
- Reasons init list exists:
  -  The compiler can optimize initialization faster from an initialization list than from within the constructor. 
  -  If you have a const class attribute, you can only initialize it using an initialization list. Otherwise, you would violate the const keyword simply by initializing the member in the constructor! 
  - Attributes defined as references must use initialization lists.

#### 5. Accessor Functions
- Public member functions that allow users to access object's data.
- `const` accessors should only retrieve data. They should not change the data stored in the object.
- The main role of `const` specifier in accessor methods is to protect member data. When you specify a member function as const, the compiler will prohibit that function from changing any of the object's member data.

#### 6. Mutator Functions
- "setter" function can apply logic ("invariants") when updating member data.
- Setter & Getter String
```cpp
// Partial example solution for Car class 
// getters and setters for brand only
#include <string>
#include <cstring>
#include <iostream>
// Define Car class
class Car {
    // Define private attributes
    private:
        int horse_power;
        int weight;
        char *brand;
    // Declare public getter and setter
    public:
        void SetBrand(std::string brand_name);
        std::string GetBrand() const;
};

// Define setter
void Car::SetBrand(std::string brand_name) {
    // Initialization of char array
    Car::brand = new char[brand_name.length() + 1];
    // copying every character from string to char array;
    strcpy(Car::brand, brand_name.c_str());
}

// Define getter
std::string Car::GetBrand() const {
    std::string result = "Brand name: ";
    // Specifying string for output of brand name
    result += Car::brand;
    return result;
};

// Test in main()
int main() {
    Car car;
    car.SetBrand("peugeot");
    std::cout << car.GetBrand() << "\n";
}
```

#### 7. Exception Sample
```cpp
#include <cassert>
#include <stdexcept>
#include <iostream>

class Student {
 public:
  // constructor
    Student (std::string _name, int _grade, float _gpa) : 
    name(_name), grade(_grade), gpa(_gpa) 
    {
        Validate(_grade, _gpa);
    }
  // accessors
    std::string Name() const { return name; }
    int Grade() const { return grade; }
    float GPA() const { return gpa; }
    
  // mutators
    void Validate(int _grade, float _GPA)
    {
        if (_grade < 0 || _grade > 12 || _GPA < 0.0f || _GPA > 4.0f)
            throw std::invalid_argument("invalid arguments");
    }
    
    void Grade(int _grade)
    {
        grade = (_grade >= 0 && _grade <= 12) ? _grade : throw std::invalid_argument("invalid grade");
    }
    
    void GPA(float _gpa)
    {
        gpa = (_gpa >= 0.0f && _gpa <= 4.0f) ? _gpa : throw std::invalid_argument("invalid gpa");
    }

 private:
  std::string name;
  int grade;
  float gpa;
};


int main() 
{
    Student student1("Bob", 11, 3.0);
    
    assert (student1.Name() == "Bob");
    assert (student1.Grade() == 11);
    assert (student1.GPA() == 3.0);
    
    try {
        Student student2("Brian", -1, 0.0);
    }
    catch(...) {
    // do stuff with exception... 
        std::cout << "Exception..." << std::endl;
    }
}
```
#### 8. STATIC in Class
- Class members can be declared static, which means that the member belongs to the entire class, instead of to a specific instance of the class. More specifically, a static member is created only once and then shared by all instances (i.e. objects) of the class. That means that if the static member gets changed, either by a user of the class or within a member function of the class itself, then all members of the class will see that change the next time they access the static member.
- static members are declared within their class (often in a header file) but in most cases they must be defined within the global scope. That's because memory is allocated for static variables immediately when the program begins, at the same time any global variables are initialized.

  ```cpp
  #include <cassert>

  class Foo {
  public:
    static int count;
    Foo() { Foo::count += 1; }
  };

  int Foo::count{0};

  int main() {
    Foo f{};
    assert(Foo::count == 1);
  }
  ```
- An exception to the global definition of static members is if such members can be marked as constexpr. In that case, the static member variable can be both declared and defined within the class definition:
  ```cpp
  struct Kilometer {
    static constexpr int meters{1000};
  };
  ```
- In addition to static member variables, C++ supports static member functions (or "methods"). Just like static member variables, static member functions are instance-independent: they belong to the class, not to any particular instance of the class.
  One corollary to this is that we can method invoke a static member function without ever creating an instance of the class. 

### II. Advanced

#### 1. Inheritance
- Base class: parent. Derive class: child
- **Public inheritance**: the public and protected members of the base class listed after the specifier keep their member access in the derived class
**Protected inheritance**: the public and protected members of the base class listed after the specifier are protected members of the derived class
**Private inheritance**: the public and protected members of the base class listed after the specifier are private members of the derived class
**Friend inheritance**: can access private members of the base class.
  ```cpp
  #include <assert.h>

  // Define class Square as friend of Rectangle
  class Square {
  private:
      int side;
      friend class Rectangle;
      
  public:
      Square(int _side) : side(_side) {}
  };


  // Declare class Rectangle
  class Rectangle {
  public:
      Rectangle(const Square& s);
      int Area();

  private:
      int width, height;
  };

  Rectangle::Rectangle(const Square& s) : width(s.side), height(s.side) {}
  int Rectangle::Area() { return width*height; }

  int main()
  {
      Square square(4);
      Rectangle rectangle(square);
      assert(rectangle.Area() == 16); 
  }
  ```

#### 2. Composition
- Composition is a closely related alternative to inheritance. Composition involves constructing ("composing") classes from other classes, instead of inheriting traits from a parent class.
- A common way to distinguish "composition" from "inheritance" is to think about what an object can do, rather than what it is. This is often expressed as "has a" versus "is a".
- From the standpoint of composition, a cat "has a" head and "has a" set of paws and "has a" tail. From the standpoint of inheritance, a cat "is a" mammal.
- There is no hard and fast rule about when to prefer composition over inheritance. In general, if a class needs only extend a small amount of functionality beyond what is already offered by another class, it makes sense to inherit from that other class. However, if a class needs to contain functionality from a variety of otherwise unrelated classes, it makes sense to compose the class from those other classes.
  ```cpp
  // Example solution for Circle class
  #include <iostream>
  #include <cmath>
  #include <assert.h>
  // Define PI
  #define PI 3.14159;


  // Define LineSegment struct
  struct LineSegment {
  // Define protected attribute length
  public:
      double length;
  };

  // Define Circle class
  class Circle {
  public:
      Circle(LineSegment& radius);
      double Area();

  private:
      LineSegment& radius_;
  };

  // Declare Circle class
  Circle::Circle(LineSegment& radius) : radius_(radius) {}

  double Circle::Area() 
  {
      return pow(Circle::radius_.length, 2) * PI;
  }

  // Test in main()
  int main() 
  {
      LineSegment radius {3};
      Circle circle(radius);
      assert(int(circle.Area()) == 28);
  }
  ```

#### 3. Polymorphism
- "Assumeing many forms". Describes a paradignm in which a function may behave differently depending on how it is called. The function will perform differently based on its inputs.
- **Overloading**: many functions with the same name. 
  ```cpp
  #include <ctime>

  class Date {
  public:
      Date(int day, int month, int year) : day_(day), month_(month), year_(year) {}
      Date(int day, int month) : day_(day), month_(month)  // automatically sets the Date to the current year
      {
          time_t t = time(NULL);
          tm* timePtr = localtime(&t);
          year_ = timePtr->tm_year;
      }

  private:
      int day_;
      int month_;
      int year_;
  };
  ```

  ```cpp
  #include <iostream>

  class Human {};

  void hello() { std::cout << "Hello, World!\n"; }
  void hello(Human human) { std::cout << "Hello, Human!\n";}

  int main(){
      hello();
      hello(Human());
  }
  ```

  - **operator+**
  ```cpp
  #include <assert.h>

  class Point {
  public:
      Point(int _x, int _y) : x(_x), y(_y) {}
      Point operator+(Point& other)
      {
          Point p(x + other.x, y + other.y);
          return p;
      }
      int x, y;
  };

  int main() {
    Point p1(10, 5), p2(2, 4);
    Point p3 = p1 + p2; // An example call to "operator +";
    assert(p3.x == p1.x + p2.x);
    assert(p3.y == p1.y + p2.y);
  }
  ```
