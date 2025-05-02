# Chapter 1 Quiz: Python Deep Dive & Best Practices

Select the best answer for each question.

## **Question 1**

Consider the following code:

```
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Before")
        result = func(*args, **kwargs)
        print("After")
        return result
    return wrapper

@my_decorator
def say_whee(name="World"):
    print(f"Whee, {name}!")

say_whee("Pythonista")
```

What will be the exact output when `say_whee("Pythonista")` is called?

a.
```
Whee, Pythonista!
```

b.
```
Before
After
```

c.
```
Before
Whee, Pythonista!
After
```

d.
```
Whee, Pythonista!
Before
After
```

??? success "Click to see the answer"
    The correct answer is **c)**. The decorator's `wrapper` function executes first, printing "Before". Then, it calls the original `say_whee` function, which prints "Whee, Pythonista!". Finally, the `wrapper` function continues after the original function call and prints "After".

## **Question 2**

Which pair of special methods correctly represents the core of the Context Manager Protocol, and what are their primary roles?

a. `__init__` (for setup) and `__del__` (for cleanup) b. `__enter__` (for setup) and `__exit__` (for cleanup) c. `__call__` (for setup) and `__next__` (for cleanup) d. `__iter__` (for setup) and `__yield__` (for cleanup)

??? success "Click to see the answer"
    The correct answer is **b)**. The `with` statement relies on the `__enter__` method to perform setup actions when entering the block and the `__exit__` method to perform cleanup actions when exiting the block.

## **Question 3**

When processing a very large sequence of numbers (e.g., millions of items), why is using a generator expression like `sum(x*x for x in large_sequence)` generally preferred over a list comprehension like `sum([x*x for x in large_sequence])`?

a. Generator expressions are always faster than list comprehensions. b. Generator expressions use significantly less memory because they generate items one by one, avoiding the creation of a large intermediate list. c. List comprehensions cannot handle sequences with more than a few thousand items. d. Generator expressions automatically handle errors within the sequence, while list comprehensions do not.

??? success "Click to see the answer"
    The correct answer is **b)**. The main advantage of generator expressions in this scenario is memory efficiency. They produce items lazily (one at a time as needed by the `sum()` function), whereas the list comprehension creates the entire list of squared numbers in memory first, which can be problematic for very large sequences.

## **Question 4**

Consider the following Python code involving nested functions (closures):

```
x = "Global"

def outer_func():
    x = "Enclosing Original"
    def inner_func():
        print(f"Inner sees: {x}")

    x = "Enclosing Modified"
    inner_func()

outer_func()
```

What will be printed when `outer_func()` is called?

a. Inner sees: Global b. Inner sees: Enclosing Original c. Inner sees: Enclosing Modified d. An error will occur because `x` was changed after `inner_func` was defined.

??? success "Click to see the answer"
    The correct answer is **c)**. Closures in Python capture variables by reference, not by their value at the time the inner function is defined. When `inner_func` is eventually *called*, it looks up the *current* value of `x` in its enclosing scope (`outer_func`). Since `x` in `outer_func` was changed to "Enclosing Modified" before `inner_func` was called, that's the value it prints.

## **Question 5**

What is the primary purpose of the `*args` parameter in a function definition like `def func(a, b, *args):`?

a. To collect all keyword arguments into a dictionary. b. To specify required positional arguments. c. To collect any additional positional arguments passed to the function into a tuple. d. To indicate that the function is a generator.

??? success "Click to see the answer"
    The correct answer is **c)**. `*args` allows a function to accept an arbitrary number of positional arguments beyond the explicitly named ones, collecting them into a tuple named `args`.

## **Question 6**

In object-oriented programming, what does `super().__init__(...)` typically do inside the `__init__` method of a derived class?

a. It creates an instance of the superclass. b. It calls the `__init__` method of the parent class (superclass) to initialize the inherited attributes. c. It prevents the parent class's `__init__` from being called. d. It checks if the object is an instance of the superclass.

??? success "Click to see the answer"
    The correct answer is **b)**. `super()` provides a way to call methods defined in the parent class(es), and `super().__init__(...)` is commonly used to ensure the initialization logic of the parent class is executed.

## **Question 7**

What is the main difference in intended use between the `__str__` and `__repr__` dunder methods?

a. `__str__` is for debugging, `__repr__` is for user display. b. `__str__` should return a user-friendly string representation, while `__repr__` should return an unambiguous, developer-focused representation (ideally one that could recreate the object). c. `__str__` is called by `print()`, `__repr__` is called by `str()`. d. There is no significant difference; they are interchangeable.

??? success "Click to see the answer"
    The correct answer is **b)**. `str()` (and `print`) defaults to `__str__` if available, aiming for readability. `repr()` defaults to `__repr__`, aiming for an unambiguous representation useful for developers.

## **Question 8**

Why is using the `@property` decorator often preferred over exposing attributes directly (e.g., using `my_object.value = 10`)?

a. It makes attribute access significantly faster. b. It allows you to add logic (like validation or computation) to the getting or setting of an attribute while maintaining a simple attribute access syntax. c. It is required for attributes used in dunder methods. d. It automatically makes attributes read-only.

??? success "Click to see the answer"
    The correct answer is **b)**. Properties allow you to control access, add validation, or compute values when an attribute is accessed or modified, without changing the external syntax `object.attribute`.

## **Question 9**

What problem does `functools.wraps` solve when writing decorators?

a. It makes the decorated function run faster. b. It allows decorators to accept arguments. c. It prevents the decorator from executing. d. It preserves the original function's metadata (like `__name__` and `__doc__`) on the wrapped function.

??? success "Click to see the answer"
    The correct answer is **d)**. Without `functools.wraps`, the decorated function would appear to have the name and docstring of the inner `wrapper` function, which can be confusing for debugging and introspection. `wraps` copies this metadata from the original function to the wrapper.

## **Question 10**

Which keyword is essential for defining a generator function in Python?

a. `generate` b. `return` c. `yield` d. `next`

??? success "Click to see the answer"
    The correct answer is **c)**. The presence of the `yield` keyword in a function definition automatically makes it a generator function. It yields values one at a time instead of returning a single value.

## **Question 11**

What exception is raised by an iterator's `__next__` method to signal that there are no more items to iterate over?

a. `IndexError` b. `StopIteration` c. `EndOfStreamError` d. `ValueError`

??? success "Click to see the answer"
    The correct answer is **b)**. The `StopIteration` exception is the standard way the iterator protocol signals the end of the sequence. `for` loops automatically handle this exception.

## **Question 12**

If an exception occurs inside a `with` block, what happens regarding the context manager's `__exit__` method?

a. `__exit__` is skipped entirely. b. `__exit__` is called, and the exception details (type, value, traceback) are passed as arguments. c. `__exit__` is called, but all its arguments are `None`. d. The program terminates immediately before `__exit__` can be called.

??? success "Click to see the answer"
    The correct answer is **b)**. The `__exit__` method is guaranteed to be called even if an exception occurs within the `with` block. The exception details are passed to it, allowing for cleanup and optional exception handling/suppression.

## **Question 13**

Consider the following code:

```
def process_value(val):
    try:
        print("Start Try")
        result = 100 / int(val)
        print("Try Success")
        return result
    except ValueError:
        print("ValueError Caught")
        return "Invalid Number"
    finally:
        print("Finally Block Executed")

print(process_value("5"))
print("---")
print(process_value("abc"))
```

What is the exact output?

a.

```
Start Try
Try Success
Finally Block Executed
20.0
---
Start Try
ValueError Caught
Finally Block Executed
Invalid Number
```

b.

```
Start Try
Try Success
20.0
---
Start Try
ValueError Caught
Invalid Number
```

c.

```
Start Try
Try Success
Finally Block Executed
20.0
---
Start Try
Finally Block Executed
ValueError Caught
Invalid Number
```

d.

```
Start Try
Try Success
20.0
Finally Block Executed
---
Start Try
ValueError Caught
Invalid Number
Finally Block Executed
```

??? success "Click to see the answer"
    The correct answer is **a)**. The `finally` block executes regardless of whether an exception occurred or was caught in the `try`/`except` blocks. It runs *after* the `try` or `except` block finishes, but *before* the function returns or the exception propagates (if not caught).

## **Question 14**

Which statement accurately describes the difference between the `else` and `finally` blocks in a `try...except...else...finally` structure?

a. Both `else` and `finally` execute only if no exception occurs in the `try` block. b. `else` executes only if an exception occurs, while `finally` executes regardless of exceptions. c. `else` executes only if no exception occurs in the `try` block, while `finally` executes regardless of whether an exception occurred or not. d. `else` executes regardless of exceptions, while `finally` executes only if no exception occurs.

??? success "Click to see the answer"
    The correct answer is **c)**. The `else` block is conditional on the `try` block completing without errors. The `finally` block provides a guarantee of execution for cleanup actions, irrespective of what happened in the `try` and `except` blocks.

## **Question 15**

What is the Pythonic way to get both the index and the value while iterating over a list `my_list`?

a.

```
index = 0
for value in my_list:
    print(f"Index {index}: {value}")
    index += 1
```

b.

```
for index in range(len(my_list)):
    value = my_list[index]
    print(f"Index {index}: {value}")
```

c.

```
for index, value in enumerate(my_list):
    print(f"Index {index}: {value}")
```

d.

```
for value in my_list:
    index = my_list.index(value)
    print(f"Index {index}: {value}")
```

??? success "Click to see the answer"
    The correct answer is **c)**. The built-in `enumerate()` function is the standard and most Pythonic way to iterate over both the index and value of a sequence simultaneously.

## **Question 16**

Consider `list1 = ['a', 'b']` and `list2 = [1, 2, 3]`. What will `list(zip(list1, list2))` produce?

a. `[('a', 1), ('b', 2), (None, 3)]` b. `[('a', 1), ('b', 2)]` c. `[('a', 'b'), (1, 2, 3)]` d. An error because the lists have different lengths.

??? success "Click to see the answer"
    The correct answer is **b)**. `zip` pairs elements from iterables until the *shortest* iterable is exhausted. It stops after pairing 'b' with 2.

## **Question 17**

Which naming style does PEP 8 recommend for Python class names?

a. `snake_case` (e.g., `my_class`) b. `camelCase` (e.g., `myClass`) c. `UPPER_SNAKE_CASE` (e.g., `MY_CLASS`) d. `CapitalizedWords` (also known as CapWords or PascalCase) (e.g., `MyClass`)

??? success "Click to see the answer"
    The correct answer is **d)**. PEP 8 specifically recommends using the `CapitalizedWords` convention for class names.

## **Question 18**

Given the following multiple inheritance structure (a "diamond pattern"):

```
class A:
    def ping(self):
        print("Ping from A")

class B(A):
    def ping(self):
        print("Ping from B")
        super().ping()

class C(A):
    def ping(self):
        print("Ping from C")
        super().ping()

class D(B, C): # Note the order: B, then C
    def ping(self):
        print("Ping from D")
        super().ping()

d = D()
d.ping()
```

What will be the exact output of `d.ping()`?

a.

```
Ping from D
Ping from B
Ping from A
```

b.

```
Ping from D
Ping from C
Ping from A
```

c.

```
Ping from D
Ping from B
Ping from C
Ping from A
```

d.

```
Ping from D
Ping from B
Ping from C
Ping from A
Ping from A
```

??? success "Click to see the answer"

```
The correct answer is **c)**. Python uses the C3 linearization algorithm to determine the Method Resolution Order (MRO). For class D(B, C), the MRO is `(D, B, C, A, object)`. When `super().ping()` is called:
1. In `D`, `super()` refers to `B`. Output: `Ping from D`. Call `B.ping()`.
2. In `B`, `super()` refers to `C` (the *next* class in D's MRO after B). Output: `Ping from B`. Call `C.ping()`.
3. In `C`, `super()` refers to `A` (the *next* class in D's MRO after C). Output: `Ping from C`. Call `A.ping()`.
4. In `A`, `ping()` executes. Output: `Ping from A`. `super()` would refer to `object`, which doesn't have `ping`.
The final output follows the MRO: D -> B -> C -> A.
```

## **Question 19**

Which of the following variable names violates typical PEP 8 naming conventions for regular variables or functions?

a. `user_id` b. `MAX_CONNECTIONS` c. `calculateTotalAmount` d. `_internal_helper`

??? success "Click to see the answer"
    The correct answer is **c)**. PEP 8 recommends `snake_case` (lowercase with underscores) for function and variable names (`calculate_total_amount`). `UPPER_SNAKE_CASE` is for constants, `_leading_underscore` indicates internal use, and `camelCase` (like `calculateTotalAmount`) is generally discouraged for variables/functions in Python, though common in other languages.

## **Question 20**

Consider the function definition: `def process_items(id, *items, status="pending", **details):`. Which of the following function calls is **invalid**?

a. `process_items(101, 'apple', 'banana', status="done", user="admin")` b. `process_items(102, 'item1', 'item2', priority=3)` c. `process_items(103, status="failed", error_code=5, 'item3')` d. `process_items(104)`

??? success "Click to see the answer"
    The correct answer is **c)**. Positional arguments (`'item3'`) cannot follow keyword arguments (`status="failed", error_code=5`). Once keyword arguments are used in a call, all subsequent arguments must also be keyword arguments. `*items` collects positional arguments after `id`, and `**details` collects any keyword arguments not explicitly named (`status` in this case).