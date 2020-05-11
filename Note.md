### I. Intro
#### 1. Compiled vs Scripted Languages
- Compiler checks rule and tells you whether the program will work: systematically and early. It generates much better codes. It also requires to think harder when writing the codes.
- Dynamically language checks during runtime. But you can get it to run quickly even if it's not completely correct.

#### 2. Basics
- Vector:
  - push_back(): append to end of vector
  - back(): take reference of the last element
  - pop_back(): remove the last element in the list
- Read file: `std::ifstream` object to handle input file streams. Need to include `<fstream>`

  ```cpp
  std::ifstream my_file;
  my_file.open(path);

  // Or single line
  std::ifstream my_file(path);
  ```

  Check if stream has been created successfully with true/false boolean. Use getline() to get each line in the stream.

  ```cpp
  std::ifstream my_file;
  my_file.open("files/1.board");
  if (my_file) {
      std::cout << "The file stream has been created!" << "\n";
      std::string line;
      while (getline(my_file, line)) {
          std::cout << line << "\n";
      }
  }   
  ```

- Processing strings: 
    - Streaming **ints** from a **string** with `istringstream` from `<sstream>` header.

    ```cpp
    string a("1 2 3");

    istringstream my_stream(a);

    int n;
    my_stream >> n;
    cout << n << "\n";
    // OUTPUT: 1
    ```

    - Check if there is more string to stream
    ```cpp
    while (my_stream) {
        my_stream >> n;
        if (my_stream) {
            cout << "That stream was successful: " << n << "\n";
        }
        else {
            cout << "That stream was NOT successful!" << "\n";            
        }
    }
    /* OUTPUT:
    That stream was successful: 1
    That stream was successful: 2
    That stream was successful: 3
    That stream was NOT successful!
    */
    ```
- Convert to string: `std::to_string(variable)`
- `enum`, enumerator, is a way to define a type in C++ with values that are restricted to a fixed range.
    - Scope enum: `enum class Name {"a", "b", "c"};`
    ```cpp
    // Create the enum Color with fixed values.
    enum class Color {white, black, blue, red};

    // Create a Color variable and set it to Color::blue.
    Color my_color = Color::blue;

    // Test to see if my car is red.
    if (my_color == Color::red) {
        cout << "The color of my car is red!" << "\n";
    } else {
        cout << "The color of my car is not red." << "\n";
    }

    // OUTPUT: The color of my car is not red.
    ```

- `switch` with `enum`
```cpp
enum class Direction {kUp, kDown, kLeft, kRight};

Direction a = Direction::kUp;

switch (a) {
    case Direction::kUp : cout << "Going up!" << "\n";
        break;
    case Direction::kDown : cout << "Going down!" << "\n";
        break;
    case Direction::kLeft : cout << "Going left!" << "\n";
        break;
    case Direction::kRight : cout << "Going right!" << "\n";
        break;
    }
```

- Pass by Value: a copy of values of an object is passed to the function, and not actual object itself.
- Pass by Reference: an alias (alternative name) of the same variable.
- **const**: meaning roughly "I promise not to change this value."...The compiler enforces the promise made by const, even though the variable can only be evaluated at run time. A common usage of **const** is to guard against accidentally changing a variable, especially when it is passed-by-reference as a function argument.
**constexpr**: meaning roughly "to be evaluated at compile time." This is used primarily to specify constants.
**The major difference** between const and constexpr, though, is that constexpr must be evaluated at compile time.
- Array: fixed length, lower level data structure than vector and can be more efficient in term of memory and element access. Array may require careful memory management.
==============
- Pointer is a variable that stores the memory address of an object in your program.
This is extremely important to remember. For the & symbol, if it appears on the left side of an equation (e.g. when declaring a variable), it means that the variable is declared as a reference. If the & appears on the right side of an equation, or before a previously defined variable, it is used to return a memory address, as in the example above.

```cpp
    int i = 5;
    // A pointer pointer_to_i is declared and initialized to the address of i.
    int* pointer_to_i = &i;
    
    // Print the memory addresses of i and j
    cout << "The address of i is:          " << &i << "\n";
    cout << "The variable pointer_to_i is: " << pointer_to_i << "\n";
    cout << "The value of the variable pointed to by pointer_to_i is: " << *pointer_to_i << "\n";

    /* OUTPUT:
    The address of i is:          0x7ffe7e1cc06c
    The variable pointer_to_i is: 0x7ffe7e1cc06c
    The value of the variable pointed to by pointer_to_i is: 5
    */
```
- Map (hash table, hash map, or dictionary) is a data structure that use key/value pair to store data. and provides efficient lookup and isnertion of the data.
Unordered map example:
```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <string>
using std::vector;
using std::cout;
using std::unordered_map;
using std::string;


int main() {
    // Create strings to use in the hash table.
    string key = "word";
    string def_1 = "a unit of language, consisting of one or more spoken sounds or their written representation, that functions as a principal carrier of meaning";
    string def_2 = "speech or talk: to express one's emotion in words";
    string def_3 = "a short talk or conversation: 'Marston, I'd like a word with you.'";
    string def_4 = "an expression or utterance: a word of warning";
    unordered_map <string, vector<string>> my_dictionary;

    // Check if key is in the hash table.
    if (my_dictionary.find(key) == my_dictionary.end()) {
        cout << "The key 'word' is not in the dictionary." << "\n";
        cout << "Inserting a key-value pair into the dictionary." << "\n\n";
        // Set the value for the key.
        my_dictionary[key] = vector<string> {def_1, def_2, def_3, def_4};
    }

    // The key should now be in the hash table. You can access the
    // value corresponding to the key with square brackets [].
    // Here, the value my_dictionary[key] is a vector of strings.
    // We iterate over the vector and print the strings.
    cout << key << ": \n";
    auto definitions = my_dictionary[key];
    for (string definition : definitions) {
        cout << definition << "\n";
    }
}
```

### II. A* Search

A* pseudocode:
>> 
    while the list of open nodes is nonempty:
        1. Sort the open list by f-value
        2. Pop the optimal cell (called the current cell).
        3. Mark the cell's coordinates in the grid as part of the path.
        4. if the current cell is the goal cell:
            return the grid.
        5. else, expand the search to the current node's neighbors. This includes the following steps:
            - Check each neighbor cell in the grid to ensure that the cell is empty: it hasn't been closed and is not an obstacle.
            - If the cell is empty, compute the cost (g value) and the heuristic, and add to the list of open nodes.
            - Mark the cell as closed.

![Image0](./images/a-star-code-structure.png)

- Heuristic function
```cpp
int Heuristic(int x1, int y1, int x2, int y2) {
  return abs(x2 - x1) + abs(y2 - y1);
}
```
- AddToOpen
```cpp
void AddToOpen(int x, int y, int g, int h, vector<vector<int>> &openlist, vector<vector<State>> &grid) {
  // Add node to open vector, and mark grid cell as closed.
  openlist.push_back(vector<int>{x, y, g, h});
  grid[x][y] = State::kClosed;
}
```

- CheckValidCell()
```cpp
// Write CheckValidCell here. Check that the 
// cell is on the grid and not an obstacle (i.e. equals kEmpty).

enum class State {kEmpty, kObstacle, kClosed, kPath, kStart, kFinish};

bool CheckValidCell(int x, int y, vector<vector<State>> &grid)
{
  if ( x >= 0 && x < grid.size() && y >= 0 && y < grid[0].size() )
    return grid[x][y] == State::kEmpty;
  return false;
}
```

- CellSort(). Calculate f = g + h(x,y)
```cpp
bool Compare(const vector<int> a, const vector<int> b) {
  int f1 = a[2] + a[3]; // f1 = g1 + h1
  int f2 = b[2] + b[3]; // f2 = g2 + h2
  return f1 > f2; 
}

void CellSort(vector<vector<int>> *v) {
  sort(v->begin(), v->end(), Compare);
}
```
- ExpandNeighbors()
```cpp
/** 
 * Expand current nodes's neighbors and add them to the open list.
 */

// directional deltas
const int delta[4][2]{{-1, 0}, {0, -1}, {1, 0}, {0, 1}};

void ExpandNeighbors(vector<int> currNode, const int goal[2], vector<vector<int>> &open, vector<vector<State>> &grid) {

  // TODO: Get current node's data.
  int x = currNode[0];
  int y = currNode[1];
  int g = currNode[2];
  int h = currNode[3];

  // TODO: Loop through current node's potential neighbors.
  for (auto d : delta) {
    int x2 = x + d[0];
    int y2 = y + d[1];
    // TODO: Check that the potential neighbor's x2 and y2 values are on the grid and not closed.
    if (CheckValidCell(x2,y2,grid)) {

      // TODO: Increment g value, compute h value, and add neighbor to open list.
      int g2 = g + 1;
      int h2 = Heuristic(x2, y2, goal[0], goal[1]);
      AddToOpen(x2,y2,g2,h2,open,grid);
    }
  } 

} //End the function
```
- A* implementation

```cpp
/** 
 * Implementation of A* search algorithm
 */
vector<vector<State>> Search(vector<vector<State>> grid, int init[2], int goal[2]) {
  // Create the vector of open nodes.
  vector<vector<int>> open {};
  
  // Initialize the starting node.
  int x = init[0];
  int y = init[1];
  int g = 0;
  int h = Heuristic(x, y, goal[0],goal[1]);
  AddToOpen(x, y, g, h, open, grid);

  while (open.size() > 0) {
    // Get the next node
    CellSort(&open);
    auto current = open.back();
    open.pop_back();
    x = current[0];
    y = current[1];
    grid[x][y] = State::kPath;

    // Check if we're done.
    if (x == goal[0] && y == goal[1]) {
      // TODO: Set the init grid cell to kStart, and 
      // set the goal grid cell to kFinish before returning the grid. 
      grid[init[0]][init[1]] = State::kStart;
      grid[goal[0]][goal[1]] = State::kFinish;
      return grid;
    }
    
    // If we're not done, expand search to current node's neighbors.
    ExpandNeighbors(current, goal, open, grid);
  }
  
  // We've run out of new nodes to explore and haven't found a path.
  cout << "No path found!" << "\n";
  return std::vector<vector<State>>{};
}
```
- To print
```cpp
string CellString(State cell) {
  switch(cell) {
    case State::kObstacle: return "⛰️   ";
    case State::kPath: return "🚗   ";
    case State::kStart: return "🚦   "; //for kStart
    case State::kFinish: return "🏁   "; // for kFinish.
    default: return "0   "; 
  }
}

void PrintBoard(const vector<vector<State>> board) {
  for (int i = 0; i < board.size(); i++) {
    for (int j = 0; j < board[i].size(); j++) {
      cout << CellString(board[i][j]);
    }
    cout << "\n";
  }
}
```

### III. Writing Multifile Programs

- Header files, or .h files, allow related function, method, and class declarations to be collected in one place. The corresponding definitions can then be placed in .cpp files. The compiler considers a header declaration a "promise" that the definition will be found later in the code, so if the compiler reaches a function that hasn't been defined yet, it can continue on compiling until the definition is found. This allows functions to be defined (and declared) in arbitrary order.
..
- The #include statement for the header used quotes " " around the file name, and not angle brackets <>. We have stored the header in the same directory as the .cpp file, and the quotes tell the preprocessor to look for the file in the same directory as the current file - not in the usual set of directories where libraries are typically stored.
..
- There is a preprocessor directive:
    >#ifndef HEADER_EXAMPLE_H
    >#define HEADER_EXAMPLE_H
    
    at the top of the header, along with an #endif at the end. This is called an "include guard". Since the header will be included into another file, and #include just pastes contents into a file, the include guard prevents the same file from being pasted multiple times into another file. This might happen if multiple files include the same header, and then are all included into the same main.cpp, for example. The ifndef checks if HEADER_EXAMPLE_H has not been defined in the file already. If it has not been defined yet, then it is defined with #define HEADER_EXAMPLE_H, and the rest of the header is used. If HEADER_EXAMPLE_H has already been defined, then the preprocessor does not enter the ifndef block. Note: There are other ways to do this. Another common way is to use an #pragma oncepreprocessor directive, but we won't cover that in detail here. See this Wikipedia article for examples.

- 