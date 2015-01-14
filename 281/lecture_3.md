# Lecture 2: Complexity Analysis

## The `vector<>` template

* `#include <vector>`

#### Description

* Variable-size array
* Implemented as a container template
* Must specify *type* at compile time – however, not locked into the size of the vector

#### Adding values
* `.push_back()` member function resizes the vector and appends the additional element to the end.
* More efficient to use `.resize(int)` if you know the size of the vector ahead of time, then to add all elements.
	* Don't have to resize every time.

#### Accessing elements

* `vector<>` overloads `operator[]()`, so indexing can be done like an array.
* Alternatively, the member function `.at()` function can be used.
* **Out-of-bounds** checking.

#### Iteration

```cpp
for (size_t i = 0; i < values.size(); ++i) {
 	cout << values[i] << endl;
}
```

## Other STL containers

#### Stack

* A last-in, first-out data structure
* `#include <stack>`
* Initialization: `stack<int> values;`
* Can push and pop, as well as a new operation: `T top();`. This returns the value on the top of the stack.

#### Queue

* A first-in, first-out data structure
* `#include <queue>``
