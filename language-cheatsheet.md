# Java to C++/Python Cheatsheet for Coding Interviews

## Basic Syntax Comparison

| Feature | Java | C++ | Python |
|---------|------|-----|--------|
| Variable Declaration | `int num = 5;` | `int num = 5;` | `num = 5` |
| Constants | `final int MAX = 100;` | `const int MAX = 100;` | `MAX = 100` |
| Comments | `// Single line` <br> `/* Multi-line */` | `// Single line` <br> `/* Multi-line */` | `# Single line` <br> `""" Multi-line """` |
| Main Function | `public static void main(String[] args)` | `int main()` | Script runs from top to bottom |
| Printing | `System.out.println("Hello");` | `std::cout << "Hello" << std::endl;` | `print("Hello")` |
| Input | `Scanner sc = new Scanner(System.in);` <br> `int n = sc.nextInt();` | `int n; std::cin >> n;` | `n = int(input())` |

## Data Structures

### Arrays

| Java | C++ | Python |
|------|-----|--------|
| `int[] arr = new int[10];` | `int arr[10];` <br> `vector<int> arr(10);` | `arr = [0] * 10` |
| `int[] arr = {1, 2, 3};` | `int arr[] = {1, 2, 3};` <br> `vector<int> arr = {1, 2, 3};` | `arr = [1, 2, 3]` |
| `arr.length` | `arr.size()` (vector) | `len(arr)` |
| `for(int i = 0; i < arr.length; i++)` | `for(int i = 0; i < arr.size(); i++)` | `for i in range(len(arr)):` |
| `for(int num : arr)` | `for(int num : arr)` | `for num in arr:` |

### String

| Java | C++ | Python |
|------|-----|--------|
| `String s = "hello";` | `string s = "hello";` | `s = "hello"` |
| `s.length()` | `s.length()` or `s.size()` | `len(s)` |
| `s.charAt(i)` | `s[i]` | `s[i]` |
| `s.substring(2, 4)` | `s.substr(2, 2)` | `s[2:4]` |
| `s1 + s2` | `s1 + s2` | `s1 + s2` |
| `s.equals("hello")` | `s == "hello"` | `s == "hello"` |
| `s.contains("lo")` | `s.find("lo") != string::npos` | `"lo" in s` |
| `s.toUpperCase()` | `transform(s.begin(), s.end(), s.begin(), ::toupper)` | `s.upper()` |

### Collections

| Java | C++ | Python |
|------|-----|--------|
| `ArrayList<Integer> list = new ArrayList<>();` | `vector<int> list;` | `list = []` |
| `list.add(1);` | `list.push_back(1);` | `list.append(1)` |
| `list.get(i)` | `list[i]` | `list[i]` |
| `list.set(i, 5)` | `list[i] = 5;` | `list[i] = 5` |
| `list.size()` | `list.size()` | `len(list)` |
| `list.remove(i)` | `list.erase(list.begin() + i);` | `list.pop(i)` or `del list[i]` |
| `Collections.sort(list)` | `sort(list.begin(), list.end())` | `list.sort()` or `sorted(list)` |

### HashMap/Dictionary

| Java | C++ | Python |
|------|-----|--------|
| `HashMap<String, Integer> map = new HashMap<>();` | `unordered_map<string, int> map;` | `map = {}` |
| `map.put("key", 42);` | `map["key"] = 42;` | `map["key"] = 42` |
| `map.get("key")` | `map["key"]` | `map["key"]` or `map.get("key")` |
| `map.containsKey("key")` | `map.find("key") != map.end()` | `"key" in map` |
| `map.remove("key")` | `map.erase("key")` | `del map["key"]` |
| `map.size()` | `map.size()` | `len(map)` |
| `for (String key : map.keySet())` | `for(auto& pair : map)` | `for key in map:` |

### Set

| Java | C++ | Python |
|------|-----|--------|
| `HashSet<Integer> set = new HashSet<>();` | `unordered_set<int> set;` | `s = set()` |
| `set.add(1);` | `set.insert(1);` | `s.add(1)` |
| `set.contains(1)` | `set.find(1) != set.end()` | `1 in s` |
| `set.remove(1)` | `set.erase(1)` | `s.remove(1)` |
| `set.size()` | `set.size()` | `len(s)` |

### Queue and Stack

| Java | C++ | Python |
|------|-----|--------|
| `Queue<Integer> q = new LinkedList<>();` | `queue<int> q;` | `from collections import deque` <br> `q = deque()` |
| `q.add(1);` or `q.offer(1);` | `q.push(1);` | `q.append(1)` |
| `q.remove()` or `q.poll()` | `q.front(); q.pop();` | `q.popleft()` |
| `q.peek()` | `q.front()` | `q[0]` |
| `Stack<Integer> stack = new Stack<>();` | `stack<int> stack;` | `stack = []` |
| `stack.push(1);` | `stack.push(1);` | `stack.append(1)` |
| `stack.pop();` | `stack.top(); stack.pop();` | `stack.pop()` |
| `stack.peek()` | `stack.top()` | `stack[-1]` |

## Common Algorithms

### Sorting

| Java | C++ | Python |
|------|-----|--------|
| `Arrays.sort(arr)` | `sort(arr, arr + n)` | `sorted(arr)` or `arr.sort()` |
| `Arrays.sort(arr, (a, b) -> a - b)` | `sort(arr, arr + n, [](int a, int b) { return a < b; })` | `sorted(arr, key=lambda x: x)` |

### Binary Search

| Java | C++ | Python |
|------|-----|--------|
| `Arrays.binarySearch(arr, target)` | `binary_search(arr, arr + n, target)` <br> `lower_bound(arr, arr + n, target)` | `bisect.bisect_left(arr, target)` |

## Control Flow

| Feature | Java | C++ | Python |
|---------|------|-----|--------|
| If-Else | `if (x > 0) { ... } else { ... }` | `if (x > 0) { ... } else { ... }` | `if x > 0: ...` <br> `else: ...` |
| For Loop | `for (int i = 0; i < n; i++) { ... }` | `for (int i = 0; i < n; i++) { ... }` | `for i in range(n): ...` |
| While Loop | `while (condition) { ... }` | `while (condition) { ... }` | `while condition: ...` |
| Switch | `switch(var) { case 1: ... break; }` | `switch(var) { case 1: ... break; }` | `if var == 1: ...` <br> `elif var == 2: ...` |

## Functions and Classes

| Feature | Java | C++ | Python |
|---------|------|-----|--------|
| Function | `public int add(int a, int b) { return a + b; }` | `int add(int a, int b) { return a + b; }` | `def add(a, b): return a + b` |
| Class | `public class MyClass { ... }` | `class MyClass { ... };` | `class MyClass: ...` |
| Constructor | `public MyClass(int val) { this.val = val; }` | `MyClass(int val) : val(val) {}` | `def __init__(self, val): self.val = val` |
| Method | `public void doSomething() { ... }` | `void doSomething() { ... }` | `def do_something(self): ...` |

## Memory Management

| Java | C++ | Python |
|------|-----|--------|
| Automatic garbage collection | Manual memory management with `new`/`delete` | Automatic garbage collection |
| `new Object()` | `new Object()` (must delete) | Just create objects |
| No explicit memory deallocation | `delete obj;` | No explicit memory deallocation |
| References | Pointers and references | References |

## Common Coding Interview Tricks

### Java

```java
// Using a priority queue (min-heap)
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
// Max-heap
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

// Convert char array to string
char[] charArray = {'h', 'e', 'l', 'l', 'o'};
String str = new String(charArray);

// String to char array
char[] chars = str.toCharArray();

// Integer to String
String s = Integer.toString(123);
// String to Integer
int n = Integer.parseInt("123");

// Check if character is letter or digit
Character.isLetterOrDigit(c);
```

### C++

```cpp
// Using a priority queue (max-heap by default)
priority_queue<int> maxHeap;
// Min-heap
priority_queue<int, vector<int>, greater<int>> minHeap;

// Convert integer to string
string s = to_string(123);
// String to integer
int n = stoi("123");

// Check if character is letter or digit
isalnum(c);

// Including necessary headers
#include <vector>
#include <string>
#include <unordered_map>
#include <algorithm>
#include <queue>
using namespace std;
```

### Python

```python
# Using a heap (min-heap implementation)
import heapq
minHeap = []
heapq.heappush(minHeap, 3)
smallest = heapq.heappop(minHeap)

# For max-heap, negate the values
maxHeap = []
heapq.heappush(maxHeap, -3)
largest = -heapq.heappop(maxHeap)

# Check if character is letter or digit
c.isalnum()

# Convert between types
s = str(123)  # Integer to string
n = int("123")  # String to integer

# List comprehension
squares = [x**2 for x in range(10)]

# defaultdict for counting
from collections import defaultdict
counter = defaultdict(int)
for word in words:
    counter[word] += 1
```

## Time Complexities Cheatsheet

| Data Structure | Access | Search | Insertion | Deletion |
|----------------|--------|--------|-----------|----------|
| Array/List     | O(1)   | O(n)   | O(n)      | O(n)     |
| LinkedList     | O(n)   | O(n)   | O(1)*     | O(1)*    |
| HashMap/Dict   | N/A    | O(1)   | O(1)      | O(1)     |
| Set            | N/A    | O(1)   | O(1)      | O(1)     |
| Stack          | O(n)   | O(n)   | O(1)      | O(1)     |
| Queue          | O(n)   | O(n)   | O(1)      | O(1)     |
| Binary Heap    | O(n)   | O(n)   | O(log n)  | O(log n) |
| BST (balanced) | O(log n) | O(log n) | O(log n) | O(log n) |

*With reference to insertion/deletion location

## Common Coding Interview Patterns

1. **Two Pointers**: Track two positions in an array/string
2. **Sliding Window**: Track a contiguous sequence
3. **Binary Search**: Search in sorted arrays or decision spaces
4. **DFS/BFS**: Traverse trees and graphs
5. **Dynamic Programming**: Optimize overlapping subproblems
6. **Greedy Algorithms**: Make locally optimal choices
7. **Backtracking**: Build solutions incrementally
8. **Divide and Conquer**: Break problems into subproblems